# ☀️ KeepAwake

A lightweight Windows utility that keeps your computer awake, with a system tray icon, optional cursor movement, and persistent user settings.

## Features

- **Start / Stop** controls for keeping the system awake.
- **Minimize to the system tray** with a convenient right-click menu.
- **Optional cursor movement:** circle, random direction, horizontal, or vertical.
- **Return to the starting position** after each movement cycle, with position verification.
- **Adjustable movement interval, distance, and speed.**
- **Movement test** that runs a single cycle after a three-second countdown.
- **Saved preferences** restored the next time you launch the application.

Default settings: **60-second interval**, **15-pixel distance**, and **400 pixels per second**. For circular movement, the distance setting represents the circle’s diameter.

## How it prevents automatic sleep

When you click **Start**, KeepAwake calls the Windows API:

```text
SetThreadExecutionState(ES_CONTINUOUS | ES_SYSTEM_REQUIRED)
```

This tells Windows that the application currently requires the system to remain awake. The continuous request stays associated with the application’s thread until it is cleared or the thread ends.

Windows considers application power requests alongside its inactivity timers when deciding whether to enter automatic sleep. [Microsoft’s explanation of system sleep criteria](https://learn.microsoft.com/en-us/windows/win32/power/system-sleep-criteria).

### Why it can work with an administrator-configured sleep timeout

A setting such as “sleep after 30 minutes” specifies an inactivity timeout. It does not, by itself, mean that Windows must ignore requests from running applications.

If application power requests are allowed, KeepAwake’s active request can prevent automatic idle sleep even though the configured timeout remains 30 minutes. The application does not change that timeout, edit Group Policy, or modify the power plan.

Administrators can separately configure whether Windows honors system-required requests. If those requests are disabled or overridden, KeepAwake cannot guarantee that the computer will remain awake. [Microsoft’s documentation on allowing system-required requests](https://learn.microsoft.com/en-us/windows-hardware/customize/power-settings/sleep-settings-allow-system-required-requests).

Clicking **Stop** clears the request using `ES_CONTINUOUS`. Closing the application also releases it.

### Sleep, screen locking, and RDP are different

The power request targets **automatic system sleep**. KeepAwake does not request that the display stay on, and this API does not prevent screen savers or user-initiated sleep. [Windows API documentation](https://learn.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-setthreadexecutionstate).

Optional cursor movement is a separate feature. It moves the pointer without clicking, but should not be treated as a guaranteed way to prevent screen locking or RDP session timeouts.

For a remote computer, run KeepAwake **inside that computer’s session**. Running it only on the connecting laptop does not keep the remote host awake. Cursor control requires an accessible desktop; operation after locking or disconnecting an RDP session is not guaranteed.

## Getting started

1. Download the executable from **Releases**.
2. Launch the application on the computer you want to keep awake.
3. Optionally enable cursor movement and adjust its settings.
4. Click **Start**.

Minimizing the window hides it in the system tray. Double-click the sun icon to restore it.

- **Test movement:** runs one complete movement cycle after three seconds.
- **Esc:** disables cursor movement and cancels the test.
- **Stop:** stops cursor movement and clears the system power request.
- **Close / Exit:** closes the application and saves your preferences.

After restarting the application, your preferences are restored. Click **Start** to resume operation.

## Settings

The application remembers the movement interval, distance, speed, movement mode, and whether cursor movement is enabled.

Settings are stored per user in:

```text
%LOCALAPPDATA%\KeepAwake\settings.xml
```

## Requirements

- Windows with **.NET Framework 4.x**, preferably **4.8**.
- No installation required.

## Distribution

This repository provides the compiled application. Source code is not published.
