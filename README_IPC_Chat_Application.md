# Inter-Process Chat Application Using IPC

A terminal-based, two-user chat application in C for Ubuntu Linux, designed to demonstrate Inter-Process Communication (IPC) using POSIX named pipes (FIFOs).

> **Project status:** This README describes the intended design and usage. Confirm the exact command-line interface and update test results after building and running the implementation in your environment.

## Abstract

Inter-Process Communication (IPC) allows independent processes to exchange data and coordinate their activities. This project applies IPC to a simple chat application in which two participants exchange text messages through two named pipes. One FIFO carries messages from Alice to Bob, while the other carries messages from Bob to Alice.

The project is intended for Operating Systems coursework and demonstrates FIFO creation, file descriptors, reading and writing, I/O multiplexing, process lifecycle management, error handling, and resource cleanup.

## Objectives

- Understand the role of IPC in Linux.
- Use POSIX named pipes to exchange messages between independent processes.
- Support bidirectional messaging using two FIFO channels.
- Learn how file descriptors and I/O multiplexing work.
- Handle graceful exit, peer disconnection, and common system-call errors.
- Practice compiling and testing C programs with GCC.

## Features

The implementation should provide the following features:

- Two participants running in separate terminal sessions.
- Bidirectional message exchange using two named pipes.
- Sender names displayed with incoming messages.
- Incoming messages displayed promptly while the receiver is idle.
- `/exit` command for graceful termination.
- Error handling for FIFO setup, reading, writing, and shutdown.
- Cleanup of session-owned FIFO resources.
- Safe handling of message sizes and partial I/O.

Only mark a feature as complete after verifying it in the actual implementation.

## Technology Stack

- **Language:** C
- **Operating system:** Ubuntu Linux
- **Compiler:** GCC
- **Build tool:** GNU Make (optional)
- **IPC:** POSIX named pipes (FIFOs)
- **I/O multiplexing:** `select()` or `poll()`, depending on the implementation

## Architecture

Two FIFO channels are used:

- **FIFO A → B:** carries messages from Alice to Bob.
- **FIFO B → A:** carries messages from Bob to Alice.

Each participant writes to one FIFO and reads from the other. The Linux kernel manages the FIFO buffers and makes the data available to the receiving process.

```text
+------------------+        FIFO A -> B        +------------------+
| Alice process    | ------------------------> | Bob process      |
| Terminal 1       |                           | Terminal 2       |
|                  | <------------------------ |                  |
+------------------+        FIFO B -> A        +------------------+
```

A FIFO is a **byte stream**, not a message queue. The implementation must define how messages are separated and handle partial reads and writes correctly.

## Project Structure

A suggested layout is:

```text
ipc-chat/
├── chat.c
├── Makefile
└── README.md
```

Your actual filenames may differ; adjust the commands below to match the files created by your implementation.

## Prerequisites

On Ubuntu, install GCC and Make if they are not already installed:

```bash
sudo apt update
sudo apt install build-essential
```

Check the installed tools:

```bash
gcc --version
make --version
```

## Build

Move into the project directory:

```bash
cd ~/ipc-chat
```

If the project contains a `Makefile`, build with:

```bash
make
```

Alternatively, if the source is a single file named `chat.c`, compile it with:

```bash
gcc -Wall -Wextra -Wpedantic -std=c11 -o ipc-chat chat.c
```

Review and resolve compiler warnings as well as errors. If the implementation uses additional source files, include them in the build command or Makefile.

## Run

Open **two Ubuntu terminals**. Both participants must use the same session identifier and different usernames.

For an implementation that accepts the arguments `USERNAME SESSION_ID`, the example commands are:

**Terminal 1 — Alice**

```bash
./ipc-chat Alice demo-room
```

**Terminal 2 — Bob**

```bash
./ipc-chat Bob demo-room
```

These commands are examples of the intended interface. Use the exact syntax implemented by your program; check its help or usage output if the arguments differ.

Once connected, type a message and press Enter. The other participant should see it. To leave the chat, enter:

```text
/exit
```

## Example Interaction

An illustrative session could look like this:

```text
INTER-PROCESS CHAT
User: Alice
Type /exit to leave

Alice: Hello Bob!
Bob: Hi Alice!
Alice: This message uses IPC.
```

This is sample output for documentation, not a claim that a test has already been run. Actual output depends on the implementation.

## IPC APIs and Concepts

| API / Concept | Purpose |
|---|---|
| `mkfifo()` | Creates a named pipe. |
| `open()` | Opens a FIFO and returns a file descriptor. |
| `read()` | Reads bytes from a FIFO. |
| `write()` | Writes bytes to a FIFO. |
| `close()` | Closes a file descriptor. |
| `unlink()` | Removes a FIFO pathname during appropriate cleanup. |
| `select()` / `poll()` | Waits for input readiness on multiple file descriptors. |
| `fork()` | Creates a child process, if used by the design. |
| `waitpid()` | Waits for a child process and collects its termination status, if applicable. |
| Signals | Allow the program to respond to events such as Ctrl+C. |

Not every listed API must be used in every implementation. The source code determines which concepts are actually demonstrated.

## Testing Checklist

Run these tests and record the observed results:

- [ ] Program compiles with the selected warning flags.
- [ ] Alice can send a message to Bob.
- [ ] Bob can send a message to Alice.
- [ ] Multiple consecutive messages arrive correctly.
- [ ] Messages containing spaces and punctuation are preserved.
- [ ] Incoming messages appear without the receiver pressing Enter.
- [ ] `/exit` terminates the participant cleanly.
- [ ] Ctrl+C does not leave the application in a broken state.
- [ ] Unexpected peer termination is handled without an indefinite hang.
- [ ] Invalid command-line arguments produce helpful usage information.
- [ ] Long messages are handled according to the documented size limit.
- [ ] FIFO resources are cleaned up safely after termination.
- [ ] Restarting a session does not delete unrelated resources.

**Test results:** Do not mark tests as passed until they have been executed and observed.

## Troubleshooting

### `gcc: command not found`

Install the build tools:

```bash
sudo apt update
sudo apt install build-essential
```

### `No such file or directory`

Confirm that you are in the project directory and that the source file and executable have the expected names:

```bash
pwd
ls -la
```

### FIFO startup appears to hang

FIFO `open()` behavior can depend on whether the other endpoint is open. Start both participants using the same session identifier and review the implementation's connection and timeout logic.

### `Permission denied`

Check the permissions of the project directory and FIFO paths. Avoid running the chat with `sudo` unless there is a specific, justified requirement.

### Messages are truncated or merged

FIFOs are byte streams. Review the framing protocol, buffer-size checks, and partial-read/write handling.

### Broken pipe or peer disconnected

The program should handle a missing reader and peer termination safely, close its descriptors, and clean up only the resources belonging to its own session.

## Limitations

- The baseline project is intended for two users on the same machine.
- Messages are not encrypted by default.
- The project does not provide user authentication.
- Chat history is not persistent unless explicitly implemented.
- A terminal interface offers fewer features than a graphical chat application.
- FIFO session setup and cleanup require careful handling of crashes and stale resources.

## Future Enhancements

- Support multiple participants.
- Add timestamps and optional local chat history.
- Add session authentication and access controls.
- Build a terminal UI using `ncurses`.
- Add automated integration tests for process failures and message boundaries.
- Compare FIFOs with POSIX message queues, shared memory, and Unix domain sockets.

## Conclusion

The Inter-Process Chat Application demonstrates how independent Linux processes can communicate using POSIX named pipes. It provides a practical way to study IPC, file descriptors, message framing, I/O multiplexing, process lifecycle management, and safe resource cleanup.

## References

- Linux manual pages: `mkfifo(3)`, `fifo(7)`, `pipe(7)`, `read(2)`, `write(2)`, `select(2)`, `poll(2)`, `fork(2)`, `waitpid(2)`, and `signal(7)`.
- The Open Group Base Specifications (POSIX).
- GNU Compiler Collection (GCC) documentation.
