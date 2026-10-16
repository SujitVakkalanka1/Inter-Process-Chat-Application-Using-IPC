
# Inter-Process Chat Application Using IPC

A terminal-based chat application developed in C that demonstrates Inter-Process Communication (IPC) using POSIX named pipes (FIFOs) on Ubuntu Linux.

## 1. Abstract

Inter-Process Communication (IPC) allows independent processes to exchange data. This project implements a two-user chat application in which Alice and Bob communicate through named pipes. It demonstrates important Operating Systems concepts such as FIFO creation, process management, file descriptors, input/output operations, and resource cleanup.

## 2. Objectives

- Understand Inter-Process Communication.
- Implement communication between independent processes.
- Use POSIX named pipes (FIFOs) for bidirectional messaging.
- Learn Linux system calls and file descriptor operations.
- Handle process termination and communication errors.
- Compile and execute a C program on Ubuntu Linux.

## 3. Technologies Used

- **Programming Language:** C
- **Operating System:** Ubuntu Linux
- **Compiler:** GCC
- **IPC Mechanism:** POSIX Named Pipes (FIFOs)
- **I/O Multiplexing:** `select()` or `poll()`
- **Build Tool:** GNU Make (optional)

## 4. Project Architecture

Two named pipes are used for communication:

- **FIFO A → B:** Transfers messages from Alice to Bob.
- **FIFO B → A:** Transfers messages from Bob to Alice.

```text
+------------------+       FIFO A -> B       +------------------+
|   Alice Process  | -----------------------> |    Bob Process   |
|   Terminal 1     |                          |    Terminal 2    |
|                  | <----------------------- |                  |
+------------------+       FIFO B -> A       +------------------+
```

Each participant can send and receive messages through separate communication channels.

## 5. Project Structure

```text
ipc-chat/
├── chat.c
├── Makefile
└── README.md
```

- `chat.c` — Main C source code.
- `Makefile` — Automates compilation and cleanup.
- `README.md` — Project documentation.

## 6. Prerequisites

Install GCC and Make on Ubuntu:

```bash
sudo apt update
sudo apt install build-essential
```

Verify the installation:

```bash
gcc --version
make --version
```

## 7. Compilation

Navigate to the project directory:

```bash
cd ~/ipc-chat
```

If the project contains a Makefile, compile it using:

```bash
make
```

Alternatively, if the application consists of a single C source file:

```bash
gcc -Wall -Wextra -Wpedantic -std=c11 -o ipc-chat chat.c
```

## 8. Execution

Open two separate Ubuntu terminal windows.

**Terminal 1 — Alice**

```bash
./ipc-chat Alice demo-room
```

**Terminal 2 — Bob**

```bash
./ipc-chat Bob demo-room
```

These commands assume the program accepts a username followed by a session identifier. Adjust them to match the actual implementation.

Both participants must use the same session identifier.

Once connected, type a message and press Enter. The other participant should receive it.

To leave the chat, type:

```text
/exit
```

## 9. Important System Calls

| Function | Purpose |
|---|---|
| `mkfifo()` | Creates a named pipe. |
| `open()` | Opens a FIFO for communication. |
| `read()` | Reads incoming data. |
| `write()` | Sends outgoing data. |
| `close()` | Closes a file descriptor. |
| `unlink()` | Removes a FIFO pathname during cleanup. |
| `select()` | Monitors multiple file descriptors. |
| `poll()` | Alternative method for monitoring file descriptors. |
| `fork()` | Creates a child process, if required by the implementation. |
| `waitpid()` | Waits for a child process to terminate. |

A FIFO is a byte stream, not a message queue. The application must handle message boundaries and partial reads and writes correctly.

## 10. Testing Checklist

- [ ] Application compiles successfully.
- [ ] Alice can send messages to Bob.
- [ ] Bob can send messages to Alice.
- [ ] Multiple messages are delivered correctly.
- [ ] Messages containing spaces and punctuation work.
- [ ] Incoming messages appear without requiring the receiver to press Enter.
- [ ] `/exit` terminates the session gracefully.
- [ ] Ctrl+C is handled safely.
- [ ] Unexpected peer termination is handled.
- [ ] Invalid arguments display helpful usage information.
- [ ] FIFO resources are cleaned up correctly.

Mark a test as passed only after verifying it on Ubuntu.

## 11. Troubleshooting

### GCC is not installed

```bash
sudo apt update
sudo apt install build-essential
```

### Executable not found

Check the current directory:

```bash
pwd
ls -la
```

### FIFO communication hangs

Ensure both participants use the same session identifier and that the FIFO-opening logic handles connection startup correctly.

### Messages are truncated or merged

Review the message-framing protocol and ensure the program handles partial reads and writes.

### Permission errors

Check the permissions of the project directory and FIFO paths. Root privileges should not normally be required.

## 12. Limitations

- The baseline application supports two participants on the same computer.
- Messages are not encrypted by default.
- User authentication is not included by default.
- Chat history is not persistent unless explicitly implemented.
- The application uses a terminal-based interface.

## 13. Future Enhancements

- Support multiple users.
- Add timestamps and chat history.
- Add authentication and access control.
- Develop a terminal interface using `ncurses`.
- Add automated integration tests.
- Compare FIFOs with message queues, shared memory, and Unix domain sockets.

## 14. Conclusion

The Inter-Process Chat Application demonstrates how independent Linux processes communicate using POSIX named pipes. It provides practical experience with IPC, file descriptors, process management, concurrent input/output, and resource cleanup.

## 15. References

- Linux manual pages: `mkfifo(3)`, `fifo(7)`, `read(2)`, `write(2)`, `select(2)`, `poll(2)`, `fork(2)`, and `waitpid(2)`.
- POSIX system interface documentation.
- GCC documentation.
