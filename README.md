# Multi-Threaded HTTP/1.1 Server in C++17

A lightweight, zero-dependency HTTP/1.1 static web server built from scratch in C++17 using POSIX sockets and a custom producer-consumer thread pool.

## Features

- -*Thread Pool Concurrency:** Pre-spawned worker threads synchronized via `std::mutex` and `std::condition_variable` (zero busy-waiting).
- **Static File Routing & MIME Types:** Serves `.html`, `.css`, `.js`, `.json`, and binary assets (`.png`, `.jpg`) with proper `Content-Type` and `Content-Length` headers.
- **Path Traversal Protection:** Sanitizes requested URIs using `std::filesystem::weakly_canonical` to block `../` directory escape attempts (`403 Forbidden`).
- -*Thread-Safe Access Logging:** Mutex-synchronized console logging with client IP, HTTP method, URI, status code, and response latency.
- -*Graceful Shutdown:** Cleanly handles `SIGINT` (`Ctrl+C`), closing listening sockets and joining all worker threads via RAII.

## Architecture

```text
Main Acceptor Thread (socket -> bind -> listen -> accept)
         |
         v (client_fd)
[+-------------------------]
|   Thread-Safe Queue     |  <-- std::mutex + std::condition_variable
[+----[-------+-------+----]
     v       v       v
  Worker1 Worker2 WorkerN  --> Parse HTTP -> Sanitize Path -> Send Response -> Log
```

## Build & Run

### Compile
```bash
make
```

### Usage
```bash
./http_server -p 8080 -t 4 -d ./www
```

## License

This project is licensed under the [MIT License](LICENSE).
