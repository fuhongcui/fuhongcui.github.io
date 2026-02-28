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
#include <atomic>
#include <condition_variable>
#include <future>
#include <iostream>
#include <syncstream>
#include <mutex>
#include <queue>

class ThreadPool
{
    using Task = std::packaged_task<void()>;
public:
    ThreadPool(unsigned int treadCount)
    {
        for(unsigned int i = 0; i < treadCount; ++i)
        {
            const std::string threadName = "WorkThread [" + std::to_string(i) + "]";
            std::thread thread([this, threadName]() {
                pthread_setname_np(pthread_self(), threadName.c_str());
                std::osyncstream(std::cout) << threadName << " => Startup" << std::endl;
                while(true)
                {
                    Task task;
                    {
                       std::osyncstream(std::cout) << threadName << " => Waitting task ..." << std::endl;
                        std::unique_lock<std::mutex> taskLock(m_task_mutex);
                        m_task_condition.wait(taskLock, [&]() {
                            return m_stop || (!m_pause && !m_tasks.empty());
                        });
                        if(m_stop && m_tasks.empty())
                        {
                            break;
                        }
                        if(!m_tasks.empty())
                        {
                            task = std::move(m_tasks.front());
                            m_tasks.pop();
                        }
                    }
                    if(task.valid())
                    {
                        std::osyncstream(std::cout) << threadName << " => Running task ..." << std::endl;
                        task();
                    }
                }
                std::osyncstream(std::cout) <<threadName << " => Exit" << std::endl;
            });
            m_threads.emplace_back(std::move(thread));
        }

    }
    ~ThreadPool()
    {
        m_stop = true;
        m_pause = false;
        m_task_condition.notify_all();
        for(auto&& t : m_threads)
        {
            if(t.joinable())
            {
                t.join();
            }
        }
    }
    void Pause()
    {
        m_pause = true;
        m_task_condition.notify_all();
    }
    void Resume()
    {
        m_pause = false;
        m_task_condition.notify_all();
    }
    template<class F, class... Args>
    auto CommitTask(F&& f, Args&&... args) -> std::future<std::invoke_result_t<F, Args...>>
    {
        using ReturnType = std::invoke_result_t<F, Args...>;
        std::packaged_task<ReturnType()> packagedTask([f = std::forward<F>(f), args = std::make_tuple(std::forward<Args>(args)...)]() mutable {
            return std::apply(std::move(f), std::move(args));
        });
        std::future<ReturnType> taskReturn = packagedTask.get_future();
        {
            std::unique_lock<std::mutex> taskLock(m_task_mutex);
            m_tasks.emplace([t = std::move(packagedTask)]() mutable {
                t();
            });
        }
        m_task_condition.notify_one();
        return taskReturn;
    }
private:
    std::vector<std::thread>    m_threads;
    std::atomic<bool>           m_stop = false;
    std::atomic<bool>           m_pause = false;
    std::mutex                  m_task_mutex;
    std::condition_variable     m_task_condition;
    std::queue<Task>            m_tasks;
};
```

#### 使用示例
```c++
#include "ThreadPool.h"
#include <thread>

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
        auto ret = threadPool.CommitTask(WorkTask, 100000);
        taskRet.emplace_back(std::move(ret));
    }
    threadPool.Pause();
    std::this_thread::sleep_for(std::chrono::seconds(5));
    threadPool.Resume();

    for(auto&& taskRetValue : taskRet)
    {
        std::cout << taskRetValue.get() << std::endl;
    }
    return 0;
}

```