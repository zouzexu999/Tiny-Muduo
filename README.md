# Tiny-Muduo

<p align="left">
  <img src="https://img.shields.io/badge/language-C%2B%2B11-orange.svg" alt="language">
  <img src="https://img.shields.io/badge/platform-Linux-lightgrey.svg" alt="platform">
  <img src="https://img.shields.io/badge/build-CMake-brightgreen.svg" alt="build">
  <img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="license">
</p>

> A high-performance non-blocking network library modeling after [Muduo](https://github.com/chenshuo/muduo), implemented in Modern C++11.

## 📖 Introduction

**Tiny-Muduo** is a reactor-pattern-based network library developed on Linux, designed for high-concurrency TCP connections. It strips away the complex template magic of the original Muduo library and rewrites core components using **C++11 features** (smart pointers, lambda expressions, `std::function`, `std::bind`, etc.), making the code cleaner and easier to understand.

**Tiny-Muduo** 是一个基于 Reactor 模式的高性能网络库。它去除了原版 Muduo 中复杂的 Boost 依赖，完全使用 C++11 重构。核心目标是提供一个轻量级、易于学习且性能优秀的网络编程框架。

## ✨ Key Features

- **Event Loop Model**: One Loop Per Thread + Non-blocking I/O + Epoll (Level Trigger).
- **Modern C++**: Heavy use of `std::shared_ptr`, `std::unique_ptr` for memory management, avoiding raw pointers.
- **Components**:
  - **Buffer**: A dynamic buffer similar to Netty's ByteBuf (prependable + readable + writable).
  - **TimerQueue**: Efficient timer management using `timerfd` and `std::set`.
  - **AsyncLogging**: Double-buffering asynchronous logging system for high performance.
  - **TcpConnection**: Manages lifecycle of connections using `shared_from_this`.
- **Thread Safe**: Multi-threaded TCP server implementation with thread pool.

## 🏗️ Architecture

Tiny-Muduo follows the **Multi-Reactors** pattern:

```mermaid
graph TD
    Client(Clients) --> |SYN| Acceptor
    
    subgraph MainReactor [Main Reactor / Acceptor]
        Acceptor(Acceptor)
    end
    
    subgraph SubReactors [Sub Reactors / EventLoop Thread Pool]
        Loop1(EventLoop 1)
        Loop2(EventLoop 2)
        Loop3(EventLoop 3)
    end
    
    Acceptor -.-> |New Connection & Round Robin| Loop1
    Acceptor -.-> |New Connection| Loop2
    Acceptor -.-> |New Connection| Loop3
    
    Loop1 --> |Read/Write| Conn1(TcpConnection)
    Loop2 --> |Read/Write| Conn2(TcpConnection)
    Loop3 --> |Read/Write| Conn3(TcpConnection)
