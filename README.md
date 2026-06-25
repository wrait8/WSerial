# WSerial

A feature-rich serial terminal application with built-in logging, encryption, and an interactive port selection system.

## Features

- **Interactive Port Selection** - Auto-detect and select from available serial ports
- **Encrypted Logging** - Secure session logging with Fernet encryption
- **System Command Execution** - Run system commands directly from the terminal
- **Session Management** - Unique session IDs for each connection
- **Auto-reconnection** - Automatically attempts to reconnect on disconnection
- **Color-coded Output** - Enhanced readability with colorized terminal output
- **Command History** - Persistent command history across sessions
- **Multiple Port Selection Methods**:
  - Command-line arguments (`-p`)
  - Environment variables (`SERIAL_PORT`)
  - Interactive menu with auto-detection
  - Runtime port switching

## Installation

### Prerequisites
- Python 3.6 or higher
- pip (Python package manager)

### Install Dependencies
```bash
pip install pyserial colorama cryptography
```

## Usage

### Basic Usage
```bash
python WSerial.py
```

### Command Line Options
```bash
python WSerial.py [OPTIONS]
```

| Option | Description |
|--------|-------------|
| `-p, --port PORT` | Specify serial port (default: COM3) |
| `-b, --baud BAUD` | Specify baud rate (default: 115200) |
| `-o, --output [FILE]` | Save encrypted log. Use 'auto' for auto-generated filename |
| `-k, --output-key KEYFILE` | Custom key file for encryption |

### Examples
```bash
# Use specific port and baud rate
python WSerial.py -p COM5 -b 9600

# Save encrypted log with auto-generated filename
python WSerial.py -o auto

# Save encrypted log with custom filename
python WSerial.py -o my_session_log

# Use custom encryption key file
python WSerial.py -k my_secret_key

# All options combined
python WSerial.py -p /dev/ttyUSB0 -b 115200 -o session_log -k custom_key
```

## Terminal Commands

While connected to a serial device, you can use these special commands:

| Command | Description |
|---------|-------------|
| `clear` | Clear the terminal screen |
| `banner` | Display the banner |
| `quit` | Exit the program |
| `session` | Show current session ID |
| `<system command>` | Execute system commands (e.g., `ls`, `dir`, `ping`) |
| Any other text | Sent to the serial device |

### System Command Detection
The terminal automatically detects if a command is a system command and executes it locally:
```bash
# These will execute on your system
ls -la
dir
ping google.com
python --version

# These will be sent to the serial device
Hello World
12345
```

## Logging & Encryption

### How It Works
1. All serial communication is logged during the session
2. On exit, logs are encrypted using Fernet symmetric encryption
3. A unique key file is generated or loaded
4. Logs are saved with `.enc` extension

### Log Files
- **Encrypted Log**: `serial_log_YYYYMMDD_HHMMSS_SESSIONID.enc`
- **Key File**: `serial_log.key` (or custom name with `-k`)

### Decrypting Logs
To decrypt a log file:
```python
from cryptography.fernet import Fernet

with open('serial_log.key', 'rb') as f:
    key = f.read()
    
cipher = Fernet(key)

with open('serial_log.enc', 'rb') as f:
    encrypted_data = f.read()
    
decrypted_data = cipher.decrypt(encrypted_data)
print(decrypted_data.decode())
```

## Features in Detail

### Auto-Reconnection
- Automatically detects connection loss
- Attempts to reconnect for 15 seconds
- Shows countdown timer
- Press Enter during reconnection to exit

### Command History
- History saved to `~/.serial_terminal_history`
- Use arrow keys to navigate previous commands
- Persists across sessions


## Troubleshooting

### Common Issues

**Port Not Found**
```
[!] Port COM3 hasn't been found.
```
- Check if the device is connected
- Verify the port name (COM3, /dev/ttyUSB0, etc.)
- Use interactive mode to see available ports

**Permission Denied**
- On Linux: `sudo usermod -a -G dialout $USER` (reboot required)
- On Windows: Run as Administrator
- On macOS: Check system permissions

**Connection Lost**
```
[^] Serial connection lost.
[?] Reconnection timeout 15 [PRESS ENTER TO EXIT] . . .
```
- The program will automatically try to reconnect
- Press Enter to exit the reconnection attempt

### Verifying Serial Ports
```bash
# Windows
mode

# Linux/macOS
ls /dev/tty*
dmesg | grep tty
```

## Contributing

Feel free to submit issues and enhancement requests!

## Credits

Developed by @wrait8

---

**Quick Start:**
```bash
# Install dependencies
pip install pyserial colorama cryptography

# Run with interactive port selection
python WSerial.py

# Or specify port directly
python serial_terminal.py -p COM5 -b 115200 -o auto
```
