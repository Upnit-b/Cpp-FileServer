# C++ FileServer

A high-performance, lightweight, system-level file server implementation built entirely with **C++**, leveraging POSIX socket APIs to handle client-server communication for efficient file transfer operations.

This project demonstrates:

- **System Programming**: Direct use of POSIX socket APIs
- **Network Programming**: TCP client-server communication
- **Concurrent Programming**: Multi-threaded server design
- **File I/O**: Binary file handling and chunked transfers
- **Modern C++**: Integration of system-level and high-level programming

## 🔧 Technical Overview

This project leverages **C++** with low-level system networking libraries for optimal performance and direct system access:

### Core System Libraries

- **`sys/socket.h`** - Core socket programming interface for network communication
- **`arpa/inet.h`** - Internet address manipulation and conversion functions
- **`netinet/in.h`** - Internet protocol definitions and network structures

### Modern C++ Features

- **Object-Oriented Design** - Clean class-based architecture for Client and Server
- **STL Containers** - Efficient use of `std::vector`, `std::string` for data handling
- **File System API** - Modern `std::filesystem` for cross-platform file operations
- **Multi-threading** - Concurrent client handling using `std::thread`
- **RAII Principles** - Automatic resource management for files and sockets

The combination of system-level networking with modern C++ provides both performance and maintainability.

## 🚀 Features

- **Concurrent Client Support**: Multi-threaded server handles multiple clients simultaneously
- **Binary File Transfer**: Supports any file type with chunked transfer protocol
- **System-Level Performance**: Direct socket system calls for maximum efficiency
- **Automatic Directory Management**: Server creates storage directory automatically
- **Robust Error Handling**: Comprehensive error checking for network and file operations
- **Cross-Platform Networking**: POSIX-compliant socket implementation

## 📁 Project Structure

```
C-FileServer/
├── client_side.cpp     # Client implementation with upload/download functionality
├── server_side.cpp     # Multi-threaded server with file management
├── client_side         # Compiled client executable
├── server_side         # Compiled server executable
├── server_files/       # Server storage directory (auto-created)
└── structure.txt       # Project structure documentation
```

## 📋 Prerequisites

### System Requirements

- **Operating System**: Linux, macOS, or any POSIX-compliant system
- **Compiler**: G++ or Clang++ with C++17 support (for `std::filesystem`)
- **Development Tools**: Make (optional)

## 🛠️ Installation & Compilation

### Clone the Repository

```bash
git clone https://github.com/Upnit-b/C-FileServer.git
cd C-FileServer
```

### Compile the Server

```bash
g++ -o server_side server_side.cpp -std=c++17 -pthread
```

### Compile the Client

```bash
g++ -o client_side client_side.cpp -std=c++17 -pthread
```

### Alternative: Compile Both

```bash
g++ -o server_side server_side.cpp -std=c++17 -pthread
g++ -o client_side client_side.cpp -std=c++17 -pthread
```

## 🎯 Usage

### Starting the Server

```bash
./server_side
```

The server will:

- Start on port **8080** (127.0.0.1)
- Create `./server_files/` directory automatically
- Listen for incoming client connections
- Handle multiple clients concurrently

### Running the Client

```bash
./client_side
```

### Client Commands

Once connected, the client supports these commands:

- **`Upload`** - Upload a file to the server

  ```
  Enter the Command (Upload/Download/Quit): Upload
  Enter filename: example.txt
  ```

- **`Download`** - Download a file from the server

  ```
  Enter the Command (Upload/Download/Quit): Download
  Enter filename: example.txt
  ```

- **`Quit`** - Disconnect from the server
  ```
  Enter the Command (Upload/Download/Quit): Quit
  ```

## 🏗️ Architecture & Protocol

### Server Architecture

- **Multi-threaded Design**: Each client connection runs in a separate thread
- **Persistent Storage**: Files stored in `./server_files/` directory
- **Connection Management**: Automatic socket creation, binding, and listening
- **Concurrent Handling**: Multiple clients can upload/download simultaneously

### Client Architecture

- **Single Connection**: Maintains persistent connection to server
- **Command Interface**: Interactive command-line interface
- **Binary Transfer**: Supports any file type with chunked data transfer

### Transfer Protocol

#### Upload Process

1. Client sends "Upload" command
2. Client sends filename
3. Client sends file size (4 bytes)
4. Client sends file data in 4KB chunks
5. Server receives and stores file in `server_files/`

#### Download Process

1. Client sends "Download" command
2. Client sends requested filename
3. Server sends file size (4 bytes)
4. Server sends file data in 4KB chunks
5. Client receives and saves file locally

### Network Configuration

- **Protocol**: TCP (reliable connection)
- **Port**: 8080 (hardcoded in `#define PORT 8080`)
- **Address**: 127.0.0.1 (localhost)
- **Buffer Size**: 4096 bytes (`#define BUFFER_SIZE 4096`)

## 🔍 System-Level Implementation Details

### Socket Programming

```cpp
// Server socket creation
server_socket = socket(AF_INET, SOCK_STREAM, 0);

// Address binding
sockaddr_in server_address{};
server_address.sin_family = AF_INET;
inet_pton(AF_INET, "127.0.0.1", &server_address.sin_addr.s_addr);
server_address.sin_port = htons(PORT);
```

### File Transfer Mechanics

- **Chunked Transfer**: Files transferred in 4KB chunks for memory efficiency
- **Size Prefixing**: File size sent before data for proper reception
- **Binary Safety**: Handles any file type including executables and media
- **Error Recovery**: Robust error handling for network interruptions

### Multi-threading Model

```cpp
// Server spawns new thread for each client
std::thread(std::bind(&Server::handle_client, this, accept_socket)).detach();
```

## 🧪 Testing

### Basic Functionality Test

```bash
# Terminal 1: Start server
./server_side

# Terminal 2: Start client and test upload
./client_side
# Follow prompts to upload a test file

# Terminal 3: Start another client and test download
./client_side
# Download the previously uploaded file
```

### Performance Testing

```bash
# Create a large test file
dd if=/dev/zero of=large_test.bin bs=1M count=100

# Upload via client and measure transfer time
time ./client_side
# Use Upload command with large_test.bin
```

## 🐛 Troubleshooting

### Common Issues

**Compilation Errors**

```bash
# Ensure C++17 support
g++ --version

# Install build tools if missing
sudo apt-get install build-essential g++  # Ubuntu/Debian
brew install gcc                          # macOS
```

**Port Already in Use**

```bash
# Check what's using port 8080
netstat -tlnp | grep :8080
sudo lsof -i :8080

# Kill process if needed
sudo kill -9 <PID>
```

**Permission Issues**

```bash
# Ensure executables have permission
chmod +x server_side client_side

# Check write permissions for server_files/
ls -la server_files/
```

**Connection Refused**

- Ensure server is running before starting client
- Check firewall settings for port 8080
- Verify both client and server use same IP/port

## 📊 Performance Characteristics

- **Memory Usage**: ~4KB buffer per active transfer + thread overhead
- **Transfer Speed**: Limited by disk I/O and network interface, not application
- **Concurrent Clients**: Limited by system thread and file descriptor limits
- **File Size Support**: No artificial limits (system memory/disk dependent)
- **Protocol Overhead**: Minimal - just 4-byte size prefix per file

## 🔒 Security Considerations

**Current Limitations** (for development purposes, not to be deployed):

- No authentication mechanism
- No encryption of data in transit
- Server accepts any filename (path traversal possible)
- No access control or user permissions

**For Production Use**, following needs to be added:

- User authentication
- TLS/SSL encryption
- Input validation and sanitization
- Access control lists
- Rate limiting

---

**Note**: This file server is designed for educational purposes and local network use. For production deployment, implement proper security measures including authentication, encryption, and input validation.
