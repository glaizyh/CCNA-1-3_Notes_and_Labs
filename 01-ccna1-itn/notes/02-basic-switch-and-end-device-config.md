# Module 2: Basic Switch and End Device Configuration

## 1. Cisco IOS Access

### Operating System Components
- **Shell:** the user interface that lets users request tasks from the computer through a CLI or GUI.
- **Kernel:** communicates between hardware and software and manages how hardware resources meet software requirements.
- **Hardware:** the physical parts of a computer, including the underlying electronics.

### User Interfaces
- **GUI (Graphical User Interface):** interaction through icons, menus, and windows (Windows, macOS, Android). User-friendly, but a GUI can fail or crash, so network devices are usually accessed through the CLI.
- **CLI (Command Line Interface):** uses a keyboard to run network programs, enter text-based commands, and view terminal output.

### Access Methods

| Method | Description |
|--------|-------------|
| Console | Physical management port for direct access, such as the initial device configuration |
| SSH (Secure Shell) | Secure remote CLI connection over the network through a virtual interface (**recommended**) |
| Telnet | Insecure remote CLI connection; authentication, passwords, and commands are sent in **plaintext** |

- **Terminal emulation programs:** software used to connect to devices through console or SSH/Telnet (PuTTY, Tera Term, SecureCRT).

## 2. IOS Navigation

### Primary Command Modes

| Mode | Prompt | Description |
|------|--------|-------------|
| User EXEC | `Switch>` | Limited basic monitoring commands |
| Privileged EXEC | `Switch#` | Access to all commands and features |

### Configuration Modes

| Mode | Prompt | Used to configure |
|------|--------|-------------------|
| Global configuration | `Switch(config)#` | Global device options |
| Line configuration | `Switch(config-line)#` | Console, SSH, Telnet, or AUX access |
| Interface configuration | `Switch(config-if)#` | A switch port or router interface |

### Mode Navigation Commands

| Task | Command |
|------|---------|
| User EXEC to Privileged EXEC | `enable` |
| Privileged EXEC to Global Config | `configure terminal` |
| Enter Line Config | `line console 0` |
| Enter Interface Config | `interface fastEthernet 0/1` |
| Return to previous mode | `exit` |
| Return directly to Privileged EXEC | `end` or `Ctrl+Z` |

## 3. The Command Structure

### Command Syntax and Elements
- **Command format:** prompt, command, space, then a keyword or argument.
  - `Switch> show ip protocols`
  - `Switch> ping 192.168.10.5`
- **Keyword:** a specific parameter defined in the operating system.
- **Argument:** a value or variable supplied by the user (not predefined).

### Command Conventions

| Convention | Meaning |
|------------|---------|
| **Boldface** | Commands and keywords entered literally as shown |
| *Italics* | Arguments for which the user supplies a value |
| `[x]` | Optional element |
| `{x}` | Required element |

### Editing Shortcuts and Hotkeys

| Key | Action |
|-----|--------|
| `Tab` | Completes a partially typed command |
| `Up Arrow` / `Ctrl+P` | Recalls recent commands from the history buffer |
| `Enter` | Shows the next line at a `--More--` prompt |
| `Space bar` | Shows the next screen at a `--More--` prompt |
| `Ctrl+C` / `Ctrl+Z` | Exits configuration mode and returns to Privileged EXEC |
| `Ctrl+Shift+6` | All-purpose break sequence to abort DNS lookups, traceroutes, or pings |

## 4. Basic Device Configuration

### Hostname Guidelines
- Start with a letter.
- No spaces.
- End with a letter or digit.
- Use only letters, digits, and dashes.
- Fewer than 64 characters.

```
Switch(config)# hostname Sw-Floor-1
```

### Password Configuration

**User EXEC (console) password**
```
Sw-Floor-1(config)# line console 0
Sw-Floor-1(config-line)# password cisco
Sw-Floor-1(config-line)# login
```

**Privileged EXEC password**
```
Sw-Floor-1(config)# enable secret class
```

**VTY lines (remote Telnet/SSH access)**
```
Sw-Floor-1(config)# line vty 0 15
Sw-Floor-1(config-line)# password cisco
Sw-Floor-1(config-line)# login
```

**Encrypt plaintext passwords in the config file**
```
Sw-Floor-1(config)# service password-encryption
```

### Banner Messages
Used to warn unauthorized people against accessing the device. The `#` is the delimiting character.

```
Sw-Floor-1(config)# banner motd # Authorized Access Only! #
```

## 5. Save Configurations

### Configuration Files

| File | Stored in | Notes |
|------|-----------|-------|
| `startup-config` | NVRAM | Saved configuration; persists when powered off |
| `running-config` | RAM (volatile) | Current active configuration; lost when power cycles |

**Save running config to startup config**
```
Switch# copy running-config startup-config
```

### Managing Configurations
- **Restore the unsaved state:** run `reload` in Privileged EXEC mode.
- **Erase the saved configuration:** run `erase startup-config`, then `reload` to clear RAM.

```
Switch# erase startup-config
Switch# reload
```

## 6. Ports, Addresses, and IP Configuration

### Addressing Fundamentals
- **IPv4 address:** 32-bit dotted-decimal value of four numbers from 0 to 255.
- **Subnet mask:** 32-bit value that separates the network portion from the host portion.
- **Default gateway:** IP address of the local router, used to reach external networks.
- **IPv6 address:** 128-bit value written as 32 hexadecimal digits in groups of 4, separated by colons (e.g., `2001:db8:acad:10::10`).

### Static vs. Dynamic Addressing
- **Manual (static):** IP parameters entered by hand in the network properties.
- **Automatic (dynamic):** assigned by DHCP for IPv4, or DHCPv6/SLAAC for IPv6.

### Switch Virtual Interface (SVI)
To manage a Cisco switch remotely, assign an IP address to its SVI:

```
Switch# configure terminal
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.1.20 255.255.255.0
Switch(config-if)# no shutdown
```
