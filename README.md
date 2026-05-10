# Medicore - Hospital Management System

Medicore is a fullscreen, GUI-based Hospital Management System developed in C++ using the SFML graphics library. The system provides three role-based portals — Patient, Doctor, and Administrator — and persists all data to local text files which are loaded automatically on startup.

---

## Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Compilation](#compilation)
- [Running the Application](#running-the-application)
- [Data Files](#data-files)
- [User Roles](#user-roles)
- [Limitations](#limitations)

---

## Features

- Fullscreen GUI rendered at 1920x1080 via SFML
- Role-based authentication for Patients, Doctors, and Administrators
- Three-step appointment booking (doctor selection, date entry, time slot selection)
- Wallet-based billing system with top-up and payment support
- Prescription writing and medical records access
- Daily reporting and timestamped security audit log for administrators
- File-based persistence with no external database dependency

---

## Prerequisites

| Requirement | Details |
|---|---|
| Operating System | Windows 10 / 11 (64-bit) |
| Compiler | MSVC via Visual Studio 2019 or later, or MinGW-w64 with g++ 9 or higher |
| SFML | Version 2.6.x, available at [sfml-dev.org](https://www.sfml-dev.org/download.php) |

> The codebase uses `localtime_s`, a Windows-specific CRT function. Building on Linux or macOS requires replacing `localtime_s(&now, &t)` with `localtime_r(&t, &now)` in the utility functions at the top of `final.cpp`.

---

## Project Structure

**Source Files**
- `final.cpp` — application entry point and main loop
- `Person.h / .cpp` — base class for all user types
- `Patient.h / .cpp`, `Doctor.h / .cpp`, `Admin.h / .cpp` — role-specific classes
- `Appointments.h / .cpp`, `Bill.h / .cpp`, `Prescription.h / .cpp` — core domain models

**Infrastructure**
- `Storage.h` — generic templated in-memory data store
- `FileHandler.h / .cpp` — handles all file read and write operations
- `Validator.h / .cpp` — input validation utilities

**Exceptions**
- `HospitalException.h` — base exception class
- `InsufficientFundsException.h`, `InvalidInputException.h`, `SlotUnavailableException.h`, `FileNotFoundException.h`

**Assets**
- `assets/` — contains fonts, background image, and button sprites required at runtime

---

## Compilation

### Visual Studio (Recommended)

1. Download the SFML 2.6.x Visual C++ package from [sfml-dev.org](https://www.sfml-dev.org/download.php) and extract it, for example to `C:\SFML-2.6.x`.

2. Create a new Empty C++ Project in Visual Studio and add all `.cpp` and `.h` files to it.

3. Open **Project → Properties** and configure the following:

   - **C/C++ → General → Additional Include Directories**
     ```
     C:\SFML-2.6.x\include
     ```
   - **Linker → General → Additional Library Directories**
     ```
     C:\SFML-2.6.x\lib
     ```
   - **Linker → Input → Additional Dependencies**

     Debug build:
     ```
     sfml-graphics-d.lib
     sfml-window-d.lib
     sfml-system-d.lib
     ```
     Release build:
     ```
     sfml-graphics.lib
     sfml-window.lib
     sfml-system.lib
     ```

4. Build the solution with **Ctrl+Shift+B**.

### g++ (Command Line)

Requires MinGW-w64 and the MinGW build of SFML 2.6.x.

```bash
g++ -std=c++17 -o medicore.exe final.cpp Person.cpp Patient.cpp Doctor.cpp Admin.cpp \
    Appointments.cpp Bill.cpp Prescription.cpp FileHandler.cpp Validator.cpp \
    -I"C:/SFML-2.6.x/include" \
    -L"C:/SFML-2.6.x/lib" \
    -lsfml-graphics -lsfml-window -lsfml-system
```

---

## Running the Application

Prior to launching, the following must be present in the same directory as the executable:

- SFML runtime DLLs from `C:\SFML-2.6.x\bin`:
  ```
  sfml-graphics-2.dll
  sfml-window-2.dll
  sfml-system-2.dll
  sfml-graphics-d-2.dll   (Debug only)
  sfml-window-d-2.dll     (Debug only)
  sfml-system-d-2.dll     (Debug only)
  ```
- The `assets/` folder containing all fonts and images

**Visual Studio builds** output the executable to:
```
project-root\x64\Debug\medicore.exe
project-root\x64\Release\medicore.exe
```

Place the DLLs and `assets/` folder in the appropriate output directory, then run:
```bash
cd project-root\x64\Debug
medicore.exe
```

**g++ builds** output the executable to the directory in which the compile command was executed. Place the DLLs and `assets/` folder there, then run:
```bash
./medicore.exe
```

The application launches in fullscreen mode. Navigation is handled via the Back button in the top-left corner. All data is saved automatically when the window is closed.

---

## Data Files

The following files are created automatically in the executable's directory on first launch and updated on every clean exit.

| File | Contents |
|---|---|
| `patients.txt` | Patient records |
| `doctors.txt` | Doctor profiles and consultation fees |
| `appointments.txt` | All appointment records |
| `bills.txt` | Billing and payment history |
| `prescriptions.txt` | Prescriptions and clinical notes |
| `security_log.txt` | Timestamped login audit entries |

These files should not be edited manually while the application is running, as they are overwritten on exit.

---

## User Roles

**Patient**
- Book appointments via a three-step flow: doctor selection, date entry, and time slot selection
- Cancel existing appointments
- View medical records and prescriptions
- View bills and make payments
- Top up wallet balance

**Doctor**
- View the day's appointment schedule
- Mark appointments as completed or no-show
- Write prescriptions for patients
- Access patient history, restricted to patients with at least one completed appointment

**Administrator**
- Add and remove doctor and patient accounts
- View all patients, doctors, and appointments across the system
- View outstanding unpaid bills
- Discharge patients
- Generate daily summary reports
- Review the security audit log

---

## Limitations

- **Windows only** — the use of `localtime_s` makes this non-portable without source modification.
- **Single-instance** — no support for concurrent users or networked access.
- **Unencrypted storage** — all data is written as plain text and is not suitable for handling real medical records.
- **Fixed layout** — the interface is designed for 1920x1080 and may not render correctly at lower resolutions.
