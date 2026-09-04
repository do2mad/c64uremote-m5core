% C64uRemote for M5Stack Core
% User Manual
% Version 1.0

# Welcome

C64uRemote turns an **M5Stack Core** into a convenient remote control for the
**Commodore 64 Ultimate (c64u)** and the **Ultimate64 Elite-II**. Over Wi-Fi you
control the machine remotely: reset, reboot, power off, open the Ultimate menu
and change the CPU speed.

With the optional **RFID2 reader** and a **microSD card** it becomes a
tap-to-launch console: you hold an NFC card to the device and the matching game
starts on the C64. The cards are compatible with the TeensyROM and Zaparoo
format – the same card works on both systems.

This project builds on the original idea by Karl Prosser (@klumsy) and was
extended for the M5Stack Core by Martin Oswald (@mad, 1MHz.de).

# What you need

**Required:**

- An M5Stack Core (Basic, Gray or Fire)
- A Commodore 64 Ultimate or Ultimate64 Elite-II on the same Wi-Fi

**Optional, for the NFC features:**

- M5Stack Unit RFID2 (WS1850S), connected to Port A
- A microSD card (formatted FAT32) in the Core
- NFC cards: NTAG215 recommended, NTAG213/216 and MIFARE Classic also work

Without the reader or SD card, all other functions keep working normally.

# First-time setup

For the Core to find your C64, the Wi-Fi credentials and the C64's address must
be stored once. You can do that **right on the device** under *SETUP → WiFi* –
the source code no longer has to be touched. The credentials go into internal
memory and survive every restart. The *Setting up Wi-Fi* chapter walks through
the details.

If you would rather supply the credentials while programming the device, put
them into `build_env.h` as before (see the technical documentation). They then
serve as the initial values for the very first start.

The connection status is always shown in the top left of the display:

| Display | Meaning |
|---|---|
| **C64U OK** (green dot) | Everything connected, ready |
| **NO C64U** (blue) | Wi-Fi present, but the C64 does not respond |
| **AUTH?** (yellow) | C64 reachable, but the password is wrong |
| **NO WIFI** (red) | No Wi-Fi connection |

To the right you see the IP address, the current CPU speed and whether the RFID
reader and SD card were detected.

# The three buttons

The Core has three buttons below the display: **A**, **B** and **C**. The bottom
line of the screen always shows what they currently do – for A and C as an arrow
with the button letter, for B as text.

| Button | Short press | Long press (from 0.6 s) |
|---|---|---|
| **A** | move selection left / up | back, or to the home screen |
| **B** | select / confirm | special function (configurable) |
| **C** | move selection right / down | special function (configurable) |

# The home screen

At the top the status bar, in the middle the animated logo, below it all
commands as tiles:

```
RESET   REBOOT   MENU    POWER   CPU
JOY     RFID     SD      STATUS  SETUP
```

Use **A** and **C** to select a tile (it gets a bright outline), and **B** to
trigger it.

| Tile | What happens |
|---|---|
| **RESET** | The C64 is reset (like the reset button) |
| **REBOOT** | The C64 restarts completely |
| **MENU** | Opens or closes the Ultimate menu on the C64 |
| **POWER** | Powers off the C64 – press twice for safety |
| **CPU** | View and change the CPU speed |
| **JOY** | Swap the joystick ports on the C64 (Normal ↔ Swapped) |
| **RFID** | Tap a card and start the game stored on it |
| **SD** | Pick a game directly from the SD card and start it |
| **STATUS** | Detailed connection info, connection test with B |
| **SETUP** | Settings and NFC tools |

**Powering off (POWER):** Turning off by accident would be annoying, so the
prompt *POWER OFF? NOCHMAL!* ("again!") appears first. Only a second press of
**B** within the time window actually powers off.

# The battery indicator

The Core has a built-in battery. How full it is can be seen in two places in the
status bar.

**The line below the status bar** doubles as the level gauge: the filled part is
slightly thicker and coloured, the rest stays the dimmed line.

**At the right-hand end** two displays take turns every 15 seconds – `RFID` and
`SD` as before, and a battery symbol with the percentage inside.

The colour means the same in both places:

| Colour | Level |
|---|---|
| green | from 50 % |
| yellow | from 25 % |
| red, blinking | below that |
| turquoise | on the charger |

While the battery is charging a small **bolt** sits in front of the battery
symbol. Once the battery is full the Core's charge controller no longer reports
charging and the bolt disappears, even with the cable still plugged in – the
chip cannot tell more.

The Core's charge controller reports the level only in quarters: 0, 25, 50, 75
or 100 %. There are no values in between, and finer colour thresholds than
25 % are therefore not possible either.

The **status page** shows the level as a number as well.

# Changing the CPU speed

Select the **CPU** tile and open it with **B**. The current speed is shown at the
top, the list of options below. Use **A**/**C** to pick the desired speed, and
**B** to set it. The Core reads the available steps directly from the C64, so you
get exactly the values your device supports.

# Swapping the joystick ports

Some games expect the joystick in port 1, others in port 2. Instead of moving the
cable, the mapping can be swapped inside the C64.

Select the **JOY** tile and trigger it with **B** — every trigger toggles between *Normal* and
*Swapped*, and *JOY Swapped* or *JOY Normal* appears briefly.

*SETUP → Joystick* shows the current state and steps through every value your C64
offers: besides *Normal* and *Swapped* there may be *WASD P1* and *WASD P2*,
depending on the firmware — the keyboard then drives that port.

The Ultimate firmware has no dedicated remote command for this. The Core sets the
*Joystick Swapper* item in the C64 configuration, exactly like the CPU speed. The
state therefore survives until it is changed again, a reset included.

# Launching games by card (RFID)

Requirements: RFID2 reader connected, a microSD with your games inserted, and the
card written beforehand (see below).

## Just present the card (automatic)

Normally you do not have to operate anything at all: while the main screen is
showing, the Core checks in the background whether a card is present. As soon as
it finds one it switches to read mode on its own and launches the stored program.

The reading screen then stays up for about 20 seconds, so you can present the
next card right away. After that — or as soon as you press a button — it returns
to the main screen.

How often it looks is set under *SETUP → Auto-NFC*:

| Setting | Meaning |
|---|---|
| **Off** | no background polling, cards only through the RFID tile |
| **1.5s** | very frugal |
| **0.7s** | factory default, a good compromise |
| **0.3s** | quickest to react |

The probe is short enough that even at *0.3s* it slows neither the animation nor
the controls noticeably.

## Through the RFID tile

If you want to read a card deliberately — or if *Auto-NFC* is switched off — the
manual route still works:

1. Select the **RFID** tile and open it with **B**.
2. Place the NFC card on the reader.
3. The Core reads the stored path, fetches the file from the SD card and sends it
   to the C64. A progress bar shows the upload.
4. After *GESTARTET: …* ("started") the game runs.

Remove the card, tap the next one – the screen stays in read mode, so you can
play several cards one after another.

## Which files work

| Extension | What it is |
|---|---|
| `.prg` | Program (loaded and started) |
| `.crt` | Cartridge |
| `.sid` | SID music |
| `.mod` | Amiga MOD music |
| `.d64 .d71 .d81 .g64 .g71` | Disk images |

For disk images the image is mounted as drive 8. What happens next is set under
*Disk Action* in the setup: mount only, mount and reset, or mount and
automatically start the first program.

# Command cards

A card does not have to point at a game – it can also carry a **command**.
Present it and the command runs immediately, with no menu and no SD card needed.

| Card | Effect |
|---|---|
| **Reset** | Resets the C64 |
| **Reboot** | Restarts the C64 completely |
| **Ultimate Menu** | Opens or closes the Ultimate menu |
| **PowerOff direct** | Powers off immediately |
| **PowerOff with prompt** | Asks first – present the same card a second time within the time window to confirm |
| **CPU x MHz** | Sets the CPU to the value stored on the card |
| **Swap Joystick** | Swaps the joystick ports (Normal ↔ Swapped) |
| **Joystick Normal / Swapped / WASD P1 / WASD P2** | Sets the port mapping to that fixed value |

## Creating a command card

1. Choose **NFC-Cmd** in the settings.
2. Pick the command from the list. After the fixed entries come the joystick
   mappings and then all CPU steps your C64 offers, so a "CPU 10 MHz" card is
   a single click. *JOY* or *CPU* on the right tells the two blocks apart.
3. Present the card; *KARTE OK* means written and verified.

## PowerOff with prompt

The waiting time lives **on the card**, not in the device. When writing, the
value comes from *NFC-Cmd PowOff* (3, 5, 8 or 15 seconds, 8 s by default).
Presenting such a card shows *POWER OFF? NOCHMAL!* with a countdown. To power
off:

- present the **same card** again, or
- press the **button**

If the countdown expires or a different card appears, nothing happens. A card
with a time of **0** powers off immediately without asking.

## What is stored on the card

The command is plain text in an NDEF record – you can inspect or write it with
any NFC app on your phone:

```
CMD:RESET
CMD:REBOOT
CMD:MENU
CMD:POWEROFF=0      power off immediately
CMD:POWEROFF=8      ask first, 8 seconds to confirm
CMD:CPU=10          set the CPU to 10 MHz
CMD:JOY             toggle the joystick ports
CMD:JOY=SWAPPED     set the ports fixed; also NORMAL, WASD1, WASD2
```

Case does not matter. The M5Dial and M5Stack Core editions understand the same
format, so one card works on both.

# Launching games directly from SD (no card)

Open the **SD** tile. You see the files and folders on your SD card. This lets
you start something without an NFC card – handy for trying things out.

In the SD menu the buttons are mapped specially, because you often scroll a lot
here:

| Button | Function |
|---|---|
| **A / C short** | one entry up / down |
| **A long** | one folder back |
| **C hold** | keeps scrolling automatically |
| **B** | start file (or open folder) |
| **B long** | back to the home screen |

The title bar shows these special functions as a reminder.

# Writing and managing NFC cards

All card tools are found under **SETUP** at the very top. Open the setup with the
SETUP tile; the first four entries are the NFC actions.

## NFC-Write: assign a game to a card

1. Open the setup; **NFC-Write** is already selected → **B**.
2. Select the desired game file on the SD card → **B**.
3. Place the NFC card on the reader.
4. The path is written and immediately read back for verification. *KARTE OK*
   means success.

The format is compatible with the TeensyROM/Zaparoo system. A card written this
way works on both devices, as long as the folder structure on both SD cards is
the same. You can also write cards with your phone (the *NFC Tools* app, text
record).

## NFC-Info: read out a card

Shows everything on the card: UID, card type, memory size, the stored path and
whether the file actually exists on the SD card. Use **A** or **C** to page to
**page 2**, which shows technical raw data, lock bits, access protection and the
read counter.

Handy: this is how you find cards whose game you renamed or moved – the *Datei*
("file") line then reports "NICHT auf SD" ("not on SD").

## NFC-Dump and NFC-Restore: copying cards

- **NFC-Dump** saves the complete card content as a text file in the folder
  `/NFC-DUMPS/` on the SD card. Works with any card, including foreign MIFARE
  cards without text.
- **NFC-Restore** writes such a dump back onto a (blank) card.

This lets you clone cards. One note: a card's serial number (UID) is fixed in the
chip and cannot be copied – but the content is copied completely. For game cards
that is enough, because what matters there is the stored path.

# Setting up Wi-Fi

Everything for this sits under **SETUP → WiFi**. Pick an entry with **A** and
**C**, trigger it with **B**. The Core remembers up to **four networks** and
tries them one after another when connecting – handy if you move between home
and a phone hotspot.

| Entry | Effect |
|---|---|
| **Scan networks** | Search the area and pick a network from the list |
| **Load from SD** | Read `wifi.txt` from the microSD |
| **Setup portal** | Access point of its own with a web interface |
| **Saved** | Pick a stored network and connect |
| **To NFC card** | Write a stored network to an NFC card |
| **Save to SD** | Write all stored networks to the microSD as `wifi.txt` |
| **Delete network** | Remove a single entry |
| **Delete all** | Discard all credentials |

## Route 1: scan, then hand over the password on a card

*Scan networks* shows the networks found after a few seconds, along with their
signal strength. A network that is already stored is marked *known* and connects
straight away; an *open* network needs no password.

For everything else the Core asks for the password and waits for an NFC card.
The card carries an ordinary text record in the same scheme that Wi-Fi QR codes
use:

```
WIFI:S:MyWiFi;T:WPA;P:MyPassword;;
```

Write the card with a phone, for example with the **NFC Tools** app
(*Write → Add a record → Text*). If the card holds only the password without the
`WIFI:` prefix, it is assigned to the network you picked beforehand.

> The password sits unencrypted on the card. Once you are set up, overwrite the
> card – the device keeps the password in internal memory anyway.

## Presenting a card is enough

A complete `WIFI:` card also works **outside** the setup: present it on the home
screen and the Core stores the network and connects to it right away. It waits
up to eight seconds and then reports *WIFI ACTIVE* with the IP address, or
*NETWORK NOT THERE* if the network is out of range. If that network is already
connected, nothing happens (*ALREADY CONNECTED*) – so the card may simply stay
on the reader.

That gives a second device its credentials in a matter of seconds.

## Route 2: a file on the SD card

Put a text file `wifi.txt` in the root directory of the microSD:

```
ssid = MyWiFi
pass = MyWiFiPassword

ssid = Hotspot
pass = secret123

host     = 192.168.0.64
hostpass =
```

Every new `ssid` line begins a new entry; lines starting with `#` are comments.
The file is read at start-up (as long as no network is stored yet) and at any
time through *Load from SD*.

The other way round, **Save to SD** writes all stored networks together with
`host` and `hostpass` back out in exactly this format. An existing `wifi.txt` is
renamed to `wifi.bak` first, so nothing is lost. That makes setting up a second
device a no-typing affair – bear in mind that the passwords sit on the card in
plain text.

## Route 3: the setup portal

*Setup portal* turns the Core into an access point of its own for five minutes.
The display shows the network name, the password and the address you open in a
browser. There you enter SSID and password – either from the list of networks
found or by hand – and optionally the address and password of the C64.

After saving, the Core shuts the access point down and connects to the new
network. The browser connection breaking off in the process is normal.

# Settings overview

In **SETUP**, after the NFC actions, you find all settings. They are stored
permanently and reloaded after power-cycling.

## NFC and Wi-Fi

| Setting | Options |
|---|---|
| **NFC-Random** | Create a random-pick card for a directory |
| **NFC-Cmd** | Create a command card (see the *Command cards* chapter) |
| **NFC-Cmd PowOff** | Default prompt time for a PowerOff command card: 3, 5, 8, 15 s |
| **WiFi** | Wi-Fi setup submenu (see the *Setting up Wi-Fi* chapter) |
| **Auto-NFC** | Background polling interval: *Off*, *1.5s*, *0.7s*, *0.3s* |

## Button mapping

| Setting | What it does |
|---|---|
| **Taste B lang** | Action for a long press on B |
| **Taste C lang** | Action for a long press on C |
| **B lang + C** | Action for the sequence B long, then C |
| **B lang + A** | Action for the sequence B long, then A |

Each of these four can be set to **Off, Reset, Reboot, Menu** or **PowerOff**.
Factory default: B long = Reset, C long = Menu, B+C = Reboot, B+A = off.

**How the sequences work (one-handed):** hold B until *LOSLASSEN, DANN A / C*
("release, then A / C") appears, then release and briefly tap A or C. If you do
nothing, the "B long" action runs after a short delay.

| Setting | Options |
|---|---|
| **PowerOff Kombi** | Whether PowerOff via a key sequence prompts first (confirm with A) or powers off directly |
| **PowerOff Zeit** | How long the power-off prompt stays valid (0.5 to 3.0 s) |
| **Kombi Zeit** | How long the device waits for the second key after "B long" (0.5 to 3.0 s) |

## Display and effects

| Setting | Options |
|---|---|
| **Animations** | Logo animations on/off |
| **Effect** | Which effect: Auto (cycle), Static, Water, RotoZoom, SineWave, Ripple, Raster |
| **FX Detail** | Half or Full – Half computes more coarsely but runs smoother (recommended on the Core Basic) |
| **Anim Speed** | Animation speed: Slow / Normal / Fast |
| **Effect Time** | How long an effect runs: Short / Normal / Long |
| **Static Time** | How long the calm logo is shown in between |
| **Brightness** | Display brightness (32 to 255) |

## C64 options

| Setting | Options |
|---|---|
| **Disk Action** | After mounting a disk image: mount only, mount + reset, or mount + start the first program |
| **Disk Drive** | Target drive: Auto (bus 8), or fixed A / B |
| **Joystick** | Port mapping in the C64: *Normal*, *Swapped*, and depending on firmware *WASD P1* / *WASD P2* |

## Miscellaneous

| Setting | Options |
|---|---|
| **Beep** | Key click on/off |
| **Factory Reset** | Reset all settings to factory defaults |

# Frequently asked questions

**The Core shows "NO WIFI".** Check that the Wi-Fi name and password are stored
correctly and the router is in range. The Core retries the connection every few
seconds.

**"AUTH?" in the status bar.** A network password is set on the C64 that is not
stored on the Core. Check the password in the C64 menu or enter it in
`build_env.h`.

**A card starts nothing / "TYP UNBEKANNT" (unknown type).** The Ultimate can only
start the file types listed above. Use **NFC-Info** to check which path and
extension are on the card and whether the file is on the SD.

**The SD card is not detected.** Format as FAT32, 32 GB maximum, and if needed
try a different card. The Core automatically tries two speeds.

**The PowerOff command reports an error.** That is normal – when powering off the
C64 often no longer responds cleanly.

**Can I use the same cards on the TeensyROM?** Yes. The card format is identical.
The only important thing is that the folders on both SD cards have the same
names.

# License

C64uRemote is released under the **MIT License**. The full text is in the file
`LICENSE` in the project root.

It originates from **C64uRemote by Karl Prosser (@klumsy)**,
<https://github.com/ReadyOS-C64/C64uRemote>, which he published under the MIT
License. This version is a derivative work and is released under the same terms:

* Copyright (c) 2026 Karl Prosser – original project
* Copyright (c) 2026 Martin Oswald (@mad, <https://1MHz.de>) – port and extensions

The MIT License allows you to use, modify and redistribute the software,
including commercially. The only condition: **the copyright notice and the
license text must be kept** and included with every copy. There is no warranty
and no liability.

The libraries used carry their own licenses: M5Unified and M5GFX (MIT,
© M5Stack), ArduinoJson (MIT, © Benoit Blanchon) and MFRC522_I2C
(<https://github.com/kkloesener/MFRC522_I2C>). PlatformIO fetches them at build
time.

# How this was made

The port, the extensions and these manuals were written with the help of
Claude (Anthropic). Concept, idea, hardware decisions and every test on real
devices: Martin Oswald (@mad).
