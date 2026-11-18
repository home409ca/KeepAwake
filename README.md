# ☀️ KeepAwake

Lekka aplikacja Windows do utrzymywania komputera w gotowości. Działa w tle, posiada ikonę przy zegarku i oferuje opcjonalny ruch kursora oraz zapamiętywanie ustawień.

## Funkcje

- **Start / Stop** — włączanie i wyłączanie utrzymywania systemu w gotowości.
- **Minimalizacja do zasobnika systemowego** — dwuklik ikony przywraca okno.
- **Opcjonalny ruch kursora** — kółko, kierunek losowy, poziomy lub pionowy.
- **Powrót do pozycji początkowej** po każdym cyklu, z weryfikacją położenia.
- **Regulacja przerwy, dystansu i prędkości ruchu.**
- **Test ruchu** — pojedynczy cykl po trzysekundowym odliczaniu.
- **Zapisywanie ustawień** i przywracanie ich przy kolejnym uruchomieniu.

Domyślne ustawienia: przerwa **60 sekund**, dystans **15 pikseli**, prędkość **400 px/s**. W wariancie „Kółko” dystans oznacza średnicę okręgu.

## Jak aplikacja zapobiega automatycznemu uśpieniu?

Po kliknięciu **Start** aplikacja wywołuje funkcję Windows:

```text
SetThreadExecutionState(ES_CONTINUOUS | ES_SYSTEM_REQUIRED)
```

Informuje w ten sposób system, że aplikacja potrzebuje działającego komputera. Żądanie pozostaje aktywne do jego usunięcia lub zakończenia wątku aplikacji.

Windows uwzględnia żądania aplikacji podczas podejmowania decyzji o automatycznym uśpieniu. Sam brak ruchu myszy lub aktywności klawiatury nie musi więc oznaczać, że komputer może przejść w stan uśpienia. [Dokumentacja Microsoft dotycząca warunków uśpienia](https://learn.microsoft.com/en-us/windows/win32/power/system-sleep-criteria).

### Dlaczego może działać mimo limitu ustawionego przez administratora?

Ustawienie „uśpij po 30 minutach” określa czas bezczynności. Samo w sobie nie oznacza, że Windows ma ignorować żądania działających aplikacji.

Jeżeli system zezwala na honorowanie takich żądań, KeepAwake może zapobiec automatycznemu uśpieniu, mimo że limit nadal wynosi 30 minut. Aplikacja nie zmienia tego limitu, zasad grupy ani planu zasilania.

Administrator może jednak osobno wyłączyć honorowanie żądań utrzymania systemu w gotowości. W takim przypadku aplikacja nie gwarantuje zapobiegania uśpieniu. [Dokumentacja Microsoft dotycząca żądań utrzymania systemu w gotowości](https://learn.microsoft.com/en-us/windows-hardware/customize/power-settings/sleep-settings-allow-system-required-requests).

Kliknięcie **Stop** usuwa żądanie przez wywołanie `ES_CONTINUOUS`. Zamknięcie aplikacji również je zwalnia.

### Uśpienie, blokada ekranu i RDP

Żądanie dotyczy **automatycznego uśpienia systemu**. Aplikacja nie wymaga pozostawienia włączonego monitora. Używana funkcja nie blokuje wygaszacza ani ręcznego uśpienia komputera. [Dokumentacja funkcji Windows](https://learn.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-setthreadexecutionstate).

Opcjonalny ruch kursora jest oddzielną funkcją. Przesuwa wskaźnik bez klikania, ale nie gwarantuje zapobiegania blokadzie ekranu ani przekroczeniu limitów sesji RDP.

W przypadku pulpitu zdalnego uruchom KeepAwake **na komputerze, który udostępnia RDP**. Uruchomienie aplikacji wyłącznie na laptopie łączącym się z tym komputerem nie utrzyma zdalnego komputera w gotowości.

Sterowanie kursorem wymaga dostępnego pulpitu. Jego działanie po zablokowaniu ekranu lub rozłączeniu RDP nie jest gwarantowane.

## Uruchomienie

1. Pobierz plik EXE z sekcji **Releases**.
2. Uruchom aplikację na komputerze, który ma pozostać w gotowości.
3. Opcjonalnie włącz ruch myszy i dostosuj parametry.
4. Kliknij **Start**.

Minimalizacja ukrywa okno przy zegarku. Dwuklik ikony słoneczka przywraca okno.

- **Test ruchu** — wykonuje jeden pełny cykl po trzech sekundach.
- **Esc** — wyłącza ruch kursora i anuluje test.
- **Stop** — zatrzymuje ruch i usuwa żądanie utrzymania systemu w gotowości.
- **Krzyżyk / Zakończ** — zamyka aplikację i zapisuje ustawienia.

Po ponownym uruchomieniu ustawienia są przywracane. Rozpoczęcie działania wymaga kliknięcia **Start**.

## Ustawienia

Aplikacja zapamiętuje przerwę między ruchami, dystans, prędkość, wariant ruchu i zaznaczenie opcji ruchu kursora.

Ustawienia są przechowywane osobno dla każdego użytkownika w pliku:

```text
%LOCALAPPDATA%\KeepAwake\settings.xml
```

## Wymagania

- Windows z **.NET Framework 4.x** — zalecany **4.8**.
- Bez instalacji.

## Dystrybucja

Repozytorium udostępnia gotową aplikację. Kod źródłowy nie jest publikowany.

________________
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
