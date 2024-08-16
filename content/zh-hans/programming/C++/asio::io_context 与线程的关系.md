---
title: "asio::io_context 与线程的关系"
date: 2023-03-04T16:49:00+08:00
categories: 
- C++
tags:
- C++
- boost
- asio
---

之前看了两篇教程：

* [Multithreaded execution](https://dens.website/tutorials/cpp-asio/multithreading)
* [Multithreaded execution, part 2](https://dens.website/tutorials/cpp-asio/multithreading-2)

说的是两种io_context和thread之间的组合方式。

* 第一种：多个线程可以使用同一个io_context对象并`io_context.run()`。这样相当于一个线程池，asio会自动使用空闲的线程并发执行异步操作，但是会产生线程不安全的问题（有解决方法）。
* 第二种：每个线程拥有一个自己的io_context对象并`io_context.run()`。这样每个io_context都是在各自所属的线程上运行。

那么引发一个思考，io_context会自己新建线程完成异步行为嘛？答案是不会，io_context相当于一个`asyncio event loop`，每执行完一个任务后才能执行下一个任务，那么在单线程模式下，回调的编码方式肯定不如协程的编码方式。

以下代码修改了官方例子，每次异步写之前睡20秒，如果io_context会新建线程的话，回调是不会被阻塞的，但测试发现有20秒延迟。

```cpp
//
// async_tcp_echo_server.cpp
// ~~~~~~~~~~~~~~~~~~~~~~~~~
//
// Copyright (c) 2003-2022 Christopher M. Kohlhoff (chris at kohlhoff dot com)
//
// Distributed under the Boost Software License, Version 1.0. (See accompanying
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
//

#include <cstdlib>
#include <iostream>
#include <memory>
#include <utility>
#include <boost/asio.hpp>

using boost::asio::ip::tcp;

class session
  : public std::enable_shared_from_this<session>
{
public:
  session(tcp::socket socket)
    : socket_(std::move(socket))
  {
  }

  void start()
  {
    do_read();
  }

private:
  void do_read()
  {
    auto self(shared_from_this());
    socket_.async_read_some(boost::asio::buffer(data_, max_length),
        [this, self](boost::system::error_code ec, std::size_t length)
        {
          if (!ec)
          {
            do_write(length);
          }
        });
  }

  void do_write(std::size_t length)
  {
    auto self(shared_from_this());
    boost::asio::async_write(socket_, boost::asio::buffer(data_, length),
        [this, self](boost::system::error_code ec, std::size_t /*length*/)
        {
          if (!ec)
          {
            do_read();
          }
        });
    // 每次异步写之前睡20秒
    std::this_thread::sleep_for(std::chrono::milliseconds(20000));
  }

  tcp::socket socket_;
  enum { max_length = 1024 };
  char data_[max_length];
};

class server
{
public:
  server(boost::asio::io_context& io_context, short port)
    : acceptor_(io_context, tcp::endpoint(tcp::v4(), port))
  {
    do_accept();
  }

private:
  void do_accept()
  {
    acceptor_.async_accept(
        [this](boost::system::error_code ec, tcp::socket socket)
        {
          if (!ec)
          {
            std::make_shared<session>(std::move(socket))->start();
          }

          do_accept();
        });
  }

  tcp::acceptor acceptor_;
};

int main(int argc, char* argv[])
{
  try
  {
    if (argc != 2)
    {
      std::cerr << "Usage: async_tcp_echo_server <port>\n";
      return 1;
    }

    boost::asio::io_context io_context;

    server s(io_context, std::atoi(argv[1]));

    io_context.run();
  }
  catch (std::exception& e)
  {
    std::cerr << "Exception: " << e.what() << "\n";
  }

  return 0;
}
```

nc测试命令

```bash
nc -v 127.0.0.1 55550
```
