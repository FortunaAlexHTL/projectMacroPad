# MacroPad

**A custom Arduino-powered macro keyboard with a C# companion application for macOS.**

MacroPad is a personal hardware and software project designed to turn physical button presses into configurable actions on a Mac.

The project combines Arduino firmware, USB serial communication, automatic device identification, and a .NET application that manages connected devices and executes macros.

> **Project status:** Active development — not yet a production-ready release.

## Features

- **Automatic device discovery** — Detects compatible MacroPad devices connected through USB serial.
- **Persistent device identification** — Assigns unique device IDs and stores them in Arduino EEPROM.
- **Multiple-device support** — Manages multiple MacroPads with separate macro mappings.
- **Configurable actions** — Supports keyboard shortcuts and launching macOS applications.
- **Automatic configuration** — Creates default macro mappings when a new device is detected.
- **JSON configuration** — Stores device settings and macro assignments in local files.
- **Backup and recovery** — Creates timestamped configuration backups and supports recovery from damaged files.
- **Logging** — Records application activity, warnings, and errors.
- **Graceful shutdown** — Handles Ctrl+C and releases active serial connections and discovery resources.

## How It Works

```text
┌──────────────────────┐
│    Arduino MacroPad  │
│   Physical Buttons   │
└──────────┬───────────┘
           │
           │ USB Serial
           ▼
┌──────────────────────┐
│   ArduinoController  │
│ Discovery & Identity │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  MacroPadController  │
│    Macro Matching    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│    MacOSController   │
│    Action Execution  │
└──────────────────────┘
```

When a MacroPad is connected, the application discovers the device, identifies it through a serial handshake, and loads its configured macros.

When a button is pressed, the Arduino sends a button code to the application. The application matches the code and device ID against the saved configuration and executes the corresponding action.

## Technology Stack

| Component | Technology |
|---|---|
| Desktop application | C# / .NET |
| Target operating system | macOS |
| Firmware | Arduino / C++ |
| Communication | USB Serial |
| Configuration | JSON |
| Device identification | EEPROM |
| Development environment | JetBrains Rider, Arduino IDE |

## Getting Started

### Requirements

- macOS
- .NET SDK compatible with the application project
- Arduino IDE
- Compatible Arduino board (developed using Arduino Mega 2560)
- USB connection

### Build the application

Clone the repository:

```bash
git clone https://github.com/FortunaAlexHTL/MacroPad_Project.git
cd MacroPad_Project
```

Build the .NET application:

```bash
dotnet build MacroPad_application/MacroPad_application.csproj
```

Run it:

```bash
dotnet run --project MacroPad_application/MacroPad_application.csproj
```

### Upload the firmware

1. Open `Firmware/MacroPad/MacroPad.ino` in Arduino IDE.
2. Select the appropriate Arduino board and serial port.
3. Upload the firmware.
4. Connect the Arduino to your Mac.
5. Start the MacroPad application.

The application should attempt to discover and identify the connected device.

### Configure macros

MacroPad stores configuration under:

```text
~/Library/Application Support/MacroPad/
├── config.json
├── macros.json
└── Backups/
```

Macros are associated with an Arduino device ID and a button code.

The current development version uses JSON files for configuration. A dedicated graphical settings interface is planned.

## Serial Communication

The application and firmware communicate using a simple text-based serial protocol.

Example device identification:

```text
Application → WHO_ARE_YOU?
Arduino     → MACROPAD:<device-id>
```

An unconfigured device can receive a new ID:

```text
Application → SET_ID:<new-id>
Arduino     → OK:<new-id>
```

The Arduino stores its assigned ID in EEPROM, allowing it to retain its identity after disconnection or restart.

Physical button presses are sent as numeric codes, currently:

`10`, `20`, `30`, `40`, `50`

See `Firmware/SerialProtocol.md` for the protocol documentation.

## Project Structure

| Path | Responsibility |
|---|---|
| `MacroPad_application/Program.cs` | Application startup and main execution loop |
| `MacroPad_application/Controllers/` | Device discovery, macro matching, and macOS actions |
| `MacroPad_application/Connections/` | Individual serial connections |
| `MacroPad_application/Models/` | Application data models |
| `MacroPad_application/ConfigurationManager.cs` | Configuration loading, saving, and recovery coordination |
| `MacroPad_application/Backup/` | Configuration backup and restoration |
| `MacroPad_application/Logger.cs` | Application logging |
| `Firmware/` | Arduino firmware and serial protocol documentation |

## Development Roadmap

### Implemented

- [x] Arduino button input
- [x] USB serial communication
- [x] Automatic device discovery
- [x] Persistent device IDs
- [x] Multiple-device management
- [x] Keyboard and application-launch macros
- [x] JSON configuration
- [x] Automatic default macro creation
- [x] Backup and recovery system
- [x] Application logging
- [x] Graceful shutdown implementation

### Planned / In Progress

- [ ] Graphical settings application
- [ ] Improved configuration validation and safe file writing
- [ ] Complete hardware and shutdown testing
- [ ] macOS Accessibility permission handling
- [ ] Remove development-only simulator behavior from release builds
- [ ] Package the application for macOS
- [ ] Complete V1.0 release testing

## Current Limitations

MacroPad is still under development.

- Configuration currently requires editing JSON files.
- The application targets macOS.
- A packaged installer is not yet available.
- Some development-only functionality remains in the codebase.
- Hardware compatibility and release reliability are not yet fully validated.

## About the Project

MacroPad started as a personal learning project combining software development with physical electronics.

Its goal is to explore serial communication, embedded programming, C# application architecture, configuration management, and the interaction between software and real-world hardware.

The project is developed independently as part of an ongoing programming and embedded-systems learning journey.

**Developed by Alexandru-Cristian Fortuna.**
