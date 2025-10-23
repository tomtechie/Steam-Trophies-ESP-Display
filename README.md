# Steam Trophy ESP Display


![IMG_5316](https://github.com/user-attachments/assets/56ce7daa-5c21-4405-b9ac-3e49ced594bb)


## [**3D model files can be found on MakerWorld**](https://makerworld.com/en/@tomtechie) 

### A Steam Wi-Fi trophy that shows your Steam stats on a 128×64 OLED. 

It fetches data from the Steam Web API, renders configurable screens, and hosts a built-in web app for setup. When your playtime increases, it automatically re-checks achievements so **Completed Games** stays accurate.

![Steam Trophy demo](https://github.com/tomtechie/Steam-Trophies-ESP-Display/blob/main/SteamTrophy.GIF?raw=true)


## 🎮 What It Does

💡 Displays detailed **Steam stats** on a 0.96" SSD1306 (128×64) OLED display.  
Customize which metrics to show and how they’re arranged.

| Metric | Description |
|:--|:--|
| 👤 **Username** | Your Steam display name |
| 🧱 **Steam Level** | Current Steam level |
| 🎮 **Total Games** | Total games owned |
| ▶️ **Played Games** | Games with playtime |
| 📦 **Backlog** | Games never played |
| ⏱️ **Total Playtime** | Total hours played |
| ⏳ **Average Playtime** | Average hours per played game |
| 🏆 **Completed Games** | Games with 100% achievements |
| 🎖️ **Badges** | Number of Steam badges earned |
| ✨ **Player XP** | Total XP from badges |
| 👥 **Friends** | Total number of friends |
| 🟢 **Friends Online** | Friends currently online |
| 🔝 **Most Played Games** | Top 4 games by playtime |

<br>

 ## ⚙️ Features & Configuration

 <img width="1020" height="1169" alt="WebUi" src="https://github.com/user-attachments/assets/c5c36b40-e710-4380-8857-e7bf26c62019" />


### 🖥️ Configurable Screens (up to 10)
- Enable or disable each **metric** individually  
- Assign metrics to specific **screens** *(1–10)*  
- Choose **placement** *(1–4 vertical slots)*  
- Select **text size** *(Default / Big)*  
- Customize **screen titles/names**  
- Set a global **rotation interval** *(minimum 8 seconds)*  

---

### 🌐 Configuration via Built-in Web UI
- Edit your **Steam API Key** and **SteamID64** directly from the browser  
- Manage screens, metrics, and rotation timing  
- View current **Wi-Fi SSID**, **IP address**, and **signal strength (RSSI)**  

---

### 🧠 Smart Achievement Scanning
- Automatically refreshes every **2 minutes**  
- Checks achievements in **batches of 25**  
- Uses **playtime-triggered rescans** — if a game’s playtime increases, it’s re-queued for an update  
- Stores cached achievement data in **EEPROM** to minimize Steam API requests  

---

### 📶 Dual-Mode Networking (AP + STA)
- If Wi-Fi isn’t configured, the ESP starts a **setup network** automatically  
- Connect to `SteamTrophy_Setup` and open **http://192.168.4.1/ui** to enter Wi-Fi credentials  
- After a successful connection, the access point shuts off and the device joins your main network  

---

### 💾 Persistent Storage
- All configuration saved in **LittleFS** (`/config.json`)  
- Achievement cache stored in **EEPROM (4 KB)** — supports up to **200 games**



---

  ## What You Need

| Item | Notes |
|------|-------|
| 3D Model | [Download](https://makerworld.com/de/models/1896099-steam-stats-trophy-display#profileId-2031495) |
| ESP32-C3 DevKit | Development board with USB-C |
| SSD1306 OLED 128x64 | I2C display |
| USB-C cable | For flashing & Power |
| Steam account / API Key | [For Steam connection](https://steamcommunity.com/dev/apikey) |
| Latest software version | [Releases](https://github.com/tomtechie/Steam-Trophies-ESP-Display/releases)  |


---


## Step 1: Wiring the OLED to ESP32-C3


| ESP32-C3  | → | SSD1306 OLED |
|------|-------|-------|
|GPIO 8|→|SDA|
|GPIO 9|→|SCL|
|GND|→|GND|
|3.3V|→|VCC|

<img width="767" height="546" alt="Schematic" src="https://github.com/user-attachments/assets/d0ffa1a1-ca50-4732-a0c4-b6b2e8f641da" />


---


## Step 2: Install/Flash ESP

1. Download the latest version of the software: [**Releases**](https://github.com/tomtechie/Steam-Trophies-ESP-Display/releases) 
2. Go to: https://esptool.spacehuhn.com
3. Plug in your ESP32-C3 via USB
4. Click on CONNECT and select the correct port (e.g. COM5)


> ⚙️ Tip:
>On certain ESP32-C3 models, flashing requires manual boot activation.
>Keep BOOT pressed during USB connection and release it once communication with the device is established.

 
5. Click on **SELECT**
    → Select your SteamStatsTrophy.bin
6. Click **Program** and the software will be installed
---

## Step 3: Configuration

1. **Power the device.**

  > If no Wi-Fi is configured, it starts a setup network:
  > * SSID: `SteamTrophy_Setup`
  > * PASS: `Steam1234`
  > * IP: `192.168.4.1`
>

2. **Connect from your phone or laptop** to `SteamTrophy_Setup`.

3. **Open** `http://192.168.4.1/ui` in your browser.
   Enter your home Wi-Fi **SSID** and **Password** → **Save & Connect**.

4. The device attempts to join your Wi-Fi:

   * On success, the setup AP shuts off.
   * The OLED briefly shows “Connected!” and the **local IP**.

5. On your normal Wi-Fi, open a browser to the device’s IP (shown on OLED or your router’s device list). (e.g., `http://192.168.1.42/`)  
You’ll see the full **Steam Trophy** configuration UI.



---

## Step 4: Getting your Steam credentials

* **Steam Web API Key**: create one at your Steam account’s  [developer API page](https://steamcommunity.com/dev/apikey).
* **SteamID64**: your 64-bit Steam ID (not your vanity name).
  You can find it in your account details or by using any [Steamid](http://steamid.io) and login with your steam account.
>It looks like `7656119xxxxxxxxxx`.
>



---

## Step 5: Configuring the display (on-device Web UI)

Open the device’s IP in a browser (e.g., `http://192.168.1.42/`).

1. **Connection panel**

   * Paste your **Steam API Key** and **SteamID64**
   * Set **Screen rotation interval** (8 seconds minimum)
   * Choose **Number of screens** (1–10)
   * Edit **Screen names** (titles)

2. **Metrics table**

   * For each metric:

     * **Enabled**: toggle on/off
     * **Screen**: which screen (1–10) it appears on
     * **Placement (1–4)**: vertical slot (top → bottom)
     * **Text Size**: Default or Big
   * The OLED draws a screen **title** + line, then the metrics in slot order.
     If text won’t fit, the UI trims intelligently.

3. Click **Save & Reboot**.
   The configuration is saved to `/config.json`, the device reboots, and your layout is live.

4. **Wi-Fi Info** section shows the current SSID/IP/RSSI.


---

## Troubleshooting

* **Web UI doesn’t open**

  * Make sure you’re on the **same network** as the device.
  * Try re-powering the device; watch the OLED for the **IP** splash.
  * If you can’t reach it, hold the device button/reset into **setup** state (or erase `/config.json`) so it starts the AP mode again and visit `http://192.168.4.1/ui`.

* **iOS - Can't access setup UI**

  * iOS often has Private IP-address activated which causes issues. When you have connected to the `SteamTrophy_Setup` Wi-Fi click on the `i` next to the Wi-Fi → Private Wi-Fi-adress → Off.  
  You should now be able to access `http://192.168.4.1/ui`

<br>

## FAQ

### Completed Games stays low

  * Keep the device powered; it scans **25 titles per cycle**.
  After a few cycles, the count should update.




<details>
  <summary> Technical information </summary>

## How/when data updates

* **Fetch cycle**: Every **2 minutes** (if online), the device calls:

  * `GetPlayerSummaries` → Username
  * `GetSteamLevel` → Level
  * `GetOwnedGames` (with app info) → Total/Played/Backlog, Total/Avg Playtime, Top 4 most played
  * `GetBadges` → Badges, Player XP
  * `GetFriendList` → Friend count

* **Achievements scanning**:

  * Runs in **batches of 25** games per cycle to avoid API abuse.
  * Results are cached (per app: `checked`, `completed`, `lastMinutes`).
  * **Playtime-triggered rescan**:
    When `GetOwnedGames` reports a higher `playtime_forever` than cached, that game is **marked for recheck**.
    On the next batch window, the device calls `GetPlayerAchievements` for that title and updates **Completed Games** automatically.

> Translation: If you finish a game later, the trophy catches it as soon as your playtime ticks up on that game.



---

## Networking behavior

* **Normal mode (STA)**:
  Joins your Wi-Fi and serves the full UI at `http://<device-ip>/`.
* **Setup mode (AP+STA)**:
  If no creds are saved or STA can’t connect, it also starts an AP:

  * Visit `http://192.168.4.1/ui` to enter Wi-Fi.
  * On success, AP turns off.

> There’s **no captive portal** (intentionally), because phone OS captive sheets can be flaky. You always browse to the **exact IP**.

---

## Files & persistence

* **Config**: `/config.json` (LittleFS)
  Contains Wi-Fi, API Key, SteamID64, screen count/names, all metric settings, rotation interval.
* **Achievement cache**: EEPROM (4 KB)
  Stores up to 200 titles with `appid`, `checked`, `completed`, and `lastMinutes`.
  Includes a version header; older cache is upgraded automatically.

## Security notes

* Your **Steam API key** and **SteamID64** are stored locally in `/config.json`.
* The device exposes an HTTP UI on your LAN. If you need extra safety:

  * Isolate it on an IoT VLAN.
  * Use MAC filtering or firewall rules on your router.



</details>

### Having issues?

Try the following:

- Check the Troubleshooting & FAQ
- Create a issue or send me a message on MakerWorld.

</details>





