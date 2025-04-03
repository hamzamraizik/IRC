# IRC Server

A feature-rich Internet Relay Chat (IRC) server implementation in C++, providing real-time text messaging, channel management, and direct client-to-client communication capabilities.

## 📚 Table of Contents

- [Project Overview](#project-overview)
- [Core Concepts](#core-concepts)
- [Features](#features)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Usage Guide](#usage-guide)
- [IRC Commands](#irc-commands)
- [Technical Implementation](#technical-implementation)
- [Contributing](#contributing)
- [License](#license)

## 📋 Project Overview

This project implements an IRC (Internet Relay Chat) server that allows multiple users to connect, join channels, and communicate with each other in real-time. It follows the IRC protocol specifications while providing a lightweight and efficient server implementation. This server serves as both a practical IRC service and an educational tool for understanding networking concepts, socket programming, and protocol implementation.

## 🧠 Core Concepts

For beginners, here are the key concepts used in this project:

### Socket Programming
Sockets provide an endpoint for sending and receiving data across a computer network. This project uses TCP/IP sockets to establish reliable connections between the server and clients.

### Polling
Instead of creating a new thread for each client (which would be resource-intensive), this server uses `poll()` to efficiently monitor multiple file descriptors. It allows the server to handle many client connections concurrently with a single thread.

### Non-Blocking I/O
The server operates in non-blocking mode, allowing it to handle multiple connections without waiting for operations to complete on any single connection.

### IRC Protocol
The IRC protocol defines how clients and servers communicate. This implementation supports core IRC commands like `JOIN`, `PRIVMSG`, `NICK`, `USER`, and others.

### Client-Server Architecture
The server maintains connections with multiple clients and forwards messages between them based on the IRC protocol rules.

### Channel Management
Users can create and join channels (chat rooms) where multiple users can send messages that are seen by all members of that channel.

## ✨ Features

- **Authentication**: Users must provide the correct password to access the server
- **Nickname Management**: Users can select unique nicknames
- **Channel Operations**:
  - Create and join channels
  - Set and view channel topics
  - Invite users to channels
  - Kick users from channels
- **Channel Modes**:
  - `i` (invite-only)
  - `t` (topic restriction)
  - `k` (password protection)
  - `o` (operator privileges)
  - `l` (user limit)
- **Private Messaging**: Direct messages between users
- **DCC Support**: Direct Client-to-Client file transfers
- **Bot Integration**: Simple IRC bot for automated responses
- **Signal Handling**: Graceful shutdown on system signals
- **Buffer Management**: Efficient handling of message fragments

## 📁 Project Structure

```
IRC/
├── src/
│   ├── main.cpp            # Program entry point
│   ├── Server.cpp          # Server implementation
│   ├── Client.cpp          # Client representation
│   ├── Channel.cpp         # Channel management
│   ├── parse_msj.cpp       # Message parsing
│   ├── Auth.cpp            # Authentication handling
│   ├── join.cpp            # JOIN command implementation
│   ├── topic.cpp           # TOPIC command implementation
│   ├── privmsg.cpp         # PRIVMSG command implementation
│   ├── Invite.cpp          # INVITE command implementation
│   ├── Mode.cpp            # MODE command implementation
│   ├── Kick.cpp            # KICK command implementation
│   └── Tools.cpp           # Utility functions
├── include/
│   ├── Server.hpp          # Server class definition
│   ├── Client.hpp          # Client class definition
│   ├── Channel.hpp         # Channel class definition
│   ├── Msj.hpp             # Message handling definitions
│   ├── Reply.hpp           # IRC reply codes
│   └── header.hpp          # Common includes
├── ircbot/
│   ├── bot.cpp             # Bot implementation
│   ├── main.cpp            # Bot entry point
│   └── bot.hpp             # Bot class definition
└── Makefile                # Compilation instructions
```

## 🚀 Getting Started

### Prerequisites

- C++ compiler with C++98 standard support
- Linux operating system
- Make utility

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/hamzamraizik/IRC.git
   cd irc-server
   ```

2. Compile the server:
   ```bash
   make
   ```

3. Compile the bot (optional):
   ```bash
   cd ircbot
   make
   cd ..
   ```

## 📝 Usage Guide

### Starting the Server

Start the IRC server by specifying a port number and password:

```bash
./ircserv <port> <password>
```

Example:
```bash
./ircserv 6667 serverpass123
```

### Starting the Bot (Optional)

The bot can be started with:

```bash
cd ircbot
./ircbot <server_ip> <server_port> <server_password>
```

Example:
```bash
./ircbot 127.0.0.1 6667 serverpass123
```

### Connecting to the Server

You can connect to the server using any IRC client (like HexChat, mIRC, LimeChat, etc.). Here's how to connect:

1. Open your IRC client
2. Enter the server details:
   - Server: Your server's IP (or localhost if running locally)
   - Port: The port you specified when starting the server
3. Set your nickname and username
4. When prompted, enter the server password

### Basic User Flow

1. Connect to the server using an IRC client
2. Authenticate with the server password
3. Set your nickname and username
4. Join a channel (create one if it doesn't exist)
5. Start chatting!

## 🔤 IRC Commands

Here are the main IRC commands supported by this server:

- `PASS <password>`: Authenticate with the server
- `NICK <nickname>`: Set or change your nickname
- `USER <username> <hostname> <servername> <realname>`: Set your user information
- `JOIN <channel> [<key>]`: Join a channel, optionally with a password
- `PRIVMSG <target> <message>`: Send a message to a user or channel
- `TOPIC <channel> [<topic>]`: View or set a channel's topic
- `MODE <channel> <modes> [<parameters>]`: Set channel modes
- `INVITE <nickname> <channel>`: Invite a user to a channel
- `KICK <channel> <user> [<reason>]`: Remove a user from a channel
- `QUIT [<message>]`: Disconnect from the server

### Channel Modes

- `+i`: Set channel as invite-only
- `+t`: Only operators can change the topic
- `+k <password>`: Set a channel password
- `+o <nickname>`: Give operator status to a user
- `+l <limit>`: Set a user limit for the channel
- `-[mode]`: Remove any of the above modes

## 🔧 Technical Implementation

### Server Architecture

The server uses a single-threaded, event-driven architecture based on the `poll()` system call to handle multiple client connections efficiently. When a new client connects, the server adds it to a list of file descriptors to monitor. The server then continuously polls these file descriptors for activity.

### Client Connection Process

1. The server listens on the specified port
2. When a client connects, a new socket is created
3. The client socket is added to the poll list
4. A Client object is created to track the client's state
5. The server handles client authentication
6. After authentication, the client can join channels and send messages

### Message Processing

1. Incoming data is read from the client socket
2. Data is added to the client's buffer
3. Complete messages (ending with '\n') are extracted and processed
4. Remaining incomplete data stays in the buffer for the next read operation
5. Messages are parsed and appropriate handlers are called

### Signal Handling

The server gracefully handles signals like SIGINT (Ctrl+C) by:
1. Setting a flag when a signal is received
2. Checking the flag in the main loop
3. Closing all connections and freeing resources
4. Exiting cleanly

### DCC File Transfer

For file transfers, the server:
1. Facilitates the exchange of IP addresses and ports between clients
2. Allows clients to establish direct connections for file transfers
3. Does not participate in the actual data transfer

## 👥 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

Created with ❤️ by [Hamza Mraizik, Khadija Sellami, Khadija Ahmada]
