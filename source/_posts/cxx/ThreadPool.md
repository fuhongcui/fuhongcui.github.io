---
title: 一个简单的 C++ 线程池
tags: 线程
categories:
  - cxx
abbrlink: 60ebd1f7
date: 2026-02-28 16:34:37
---
### C++17 实现一个简单的线程池

#### 实现代码
```c++
#pragma once

#include <future>
#include <mutex>
#include <condition_variable>
#include <queue>

class ThreadPool
{
    using Task = std::packaged_task<void()>;
public:
    ThreadPool(unsigned int threadCount)
    {
        for(unsigned int i = 0; i < threadCount; ++i)
        {
            std::string threadName = "WorkThread [" + std::to_string(i) + "]";
            std::thread thread([this, threadName = std::move(threadName)]() {
                pthread_setname_np(pthread_self(), threadName.c_str());
                while(true)
                {
                    Task task;
                    {
                        std::unique_lock<std::mutex> lock(m_task_mutex);
                        m_waiting_thread_count++;
                        m_task_cv.wait(lock, [this]() {
                            return m_stop || (!m_pause && !m_tasks.empty());
                        });
                        m_waiting_thread_count--;
                        if(m_stop && m_tasks.empty())
                        {
                            break;
                        }
                        if(!m_tasks.empty())
                        {
                            task = std::move(m_tasks.front());
                            m_tasks.pop();
                            m_task_count--;
                        }
                    }
                    if(task.valid())
                    {
                        m_running_thread_count++; 
                        task();
                        m_running_thread_count--;
                    }
                }
            });
            m_threads.emplace_back(std::move(thread));
        }
    }
    ~ThreadPool()
    {
        {
            std::unique_lock<std::mutex> lock(m_task_mutex);
            m_stop = true;
            m_pause = false;   
        }
        m_task_cv.notify_all();
        for(auto&& t : m_threads)
        {
            if(t.joinable())
            {
                t.join();
            }
        }
    }
    ThreadPool(const ThreadPool&) = delete;
    ThreadPool& operator=(const ThreadPool&) = delete;
    template<class F, class... Args>
    auto CommitTask(F&& f, Args&&... args) -> std::future<std::invoke_result_t<F, Args...>>
    {
        using ReturnType = std::invoke_result_t<F, Args...>;
        std::packaged_task<ReturnType()> packagedTask([f = std::forward<F>(f), args = std::make_tuple(std::forward<Args>(args)...)]() mutable {
            return std::apply(std::move(f), std::move(args));
        });
        std::future<ReturnType> taskReturn = packagedTask.get_future();
        {
            std::unique_lock<std::mutex> lock(m_task_mutex);
            m_tasks.emplace([task = std::move(packagedTask)]() mutable {
                task();
            });
            m_task_count++;
        }
        m_task_cv.notify_one();
        return taskReturn;
    }
    void Pause()
    {
        {
            std::unique_lock<std::mutex> lock(m_task_mutex);
            m_pause = true;
        }
        m_task_cv.notify_all();
    }
    void Resume()
    {
        {
            std::unique_lock<std::mutex> lock(m_task_mutex);
            m_pause = false;
        }
        m_task_cv.notify_all();
    }
    unsigned int GetRunningThreadCount() const
    {
        return m_running_thread_count.load();
    }
    unsigned int GetWaitingThreadCount() const
    {
        return m_waiting_thread_count.load();
    }
    unsigned int GetTaskCount() const
    {
        return m_task_count.load();
    }
private:
    std::vector<std::thread>    m_threads;
    std::mutex                  m_task_mutex;
    std::condition_variable     m_task_cv;
    std::queue<Task>            m_tasks;
    bool                        m_stop = false;
    bool                        m_pause = false;
    std::atomic<unsigned int>   m_running_thread_count = 0;
    std::atomic<unsigned int>   m_waiting_thread_count = 0;
    std::atomic<unsigned int>   m_task_count = 0;
};
```

#### 使用示例
```c++
#include "ThreadPool.h"
#include <iostream>

unsigned int WorkTask(unsigned int value)
{
    unsigned int totalValue = 0;
    for(unsigned int i = 0; i < value + 1; ++i)
    {
        totalValue +=i;
    }
    return totalValue;
}

int main()
{
    ThreadPool threadPool(10);
    constexpr int taskACount = 10000;

    std::vector<std::future<unsigned int>> taskRet;
    for(unsigned i = 0; i < taskACount; ++i)
    {
        auto ret = threadPool.CommitTask(WorkTask, 9999999);
        taskRet.emplace_back(std::move(ret));
    }
    // threadPool.Pause();
    // threadPool.Resume();
    while(true)
    {
        std::cout << "Running thread count: " << threadPool.GetRunningThreadCount() << std::endl;
        std::cout << "Waiting thread count: " << threadPool.GetWaitingThreadCount() << std::endl;
        std::cout << "Task count: " << threadPool.GetTaskCount() << std::endl;
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }
    for(auto&& taskRetValue : taskRet)
    {
        (void)taskRetValue.get();
    }
    return 0;
}

```