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
| 3D Model (new base) | [Download](https://makerworld.com/de/models/1896099-steam-stats-trophy-display#profileId-2031495) - new 3D-printed base supports both C3 and S3 |
| ESP32-C3 Mini or ESP32-S3 Nano |  [C3](https://a.co/d/07SdfnQ3) and [S3](https://a.co/d/02UtBFj2) are both supported (use matching firmware binary) |
| SSD1306 OLED 128x64 | [I2C display](https://amzn.to/4mPmWiW) |
| USB-C cable | For flashing & Power |
| Steam account / API Key | [For Steam connection](https://steamcommunity.com/dev/apikey) |
| Latest software version | [Releases](https://github.com/tomtechie/Steam-Trophies-ESP-Display/releases)  |



---


## Step 1: Wiring the OLED (ESP32-C3 Mini vs ESP32-S3 Nano)

> The new 3D-printed base supports both board versions.
>
> Main difference: **ESP32-S3 Nano supports larger game libraries**.

### Side-by-side wiring

| ESP32-C3 Mini | | SSD1306 OLED | | ESP32-S3 Nano || SSD1306 OLED |
|------|------|------|------|------|------|------|
| GPIO 8 |→| SDA || D8 |→| SDA |
| GPIO 9 |→| SCL || D9 |→| SCL |
| GND |→| GND || GND |→| GND |
| 3.3V |→| VDD/VCC || 3.3V |→| VDD/VCC |


<img width="749" height="394" alt="Screenshot 2026-03-05 at 18 55 07" src="https://github.com/user-attachments/assets/83b1d3c5-92ed-4d88-9986-a1e1011e3607" />


Either solder the cables in place, or preferably use female jumper wires that can be easily connected to the pins on each component.
[**Recommended cables**](https://amzn.to/4qunIVA) 


---


## Step 2: Install/Flash ESP (separate steps for C3 and S3)

1. Download the latest version of the software: [**Releases**](https://github.com/tomtechie/Steam-Trophies-ESP-Display/releases) 
2. Go to: https://esptool.spacehuhn.com


### ESP32-C3 Mini flashing

1. Hold the **BOOT** button while connecting the USB to your computer.
2. Click **Connect** and choose the ESP32-C3 board.
3. Release **BOOT** once communication is established.
4. Click on **SELECT** and choose the **C3** firmware `.bin`.
5. Make sure the 0x is set to **0**
6. Click **Program**.



### ESP32-S3 Nano flashing

1. Connect a cable between **B1** & **GND** in order to set the board to flash-mode.
> The green cable shows how you should connect the cable between **B1** & **GND**
<img width="463" height="634" alt="Screenshot 2026-03-05 at 18 58 31" src="https://github.com/user-attachments/assets/56f3f2a4-5584-413b-a2b3-a68f5907462f" />

3. Connect the USB to your computer.
4. Click **Connect** and choose the ESP32-S3 board.
5. Click on **SELECT** and choose the **S3** firmware `.bin`.
6. Make sure the 0x is set to **0**
7. Click **Program**.





>
> If you are seeing multiple "locations" to put the `.bin` file on, the board likely has an existing program. Remove all locations, click erase, then add your firmware file again.
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

* **Most played games not showing**
  * Due to the limitation of the ESP32-C3 there seems to be a issue with loading Most Played games for some users, this seems to be due to the amount of games. This is solved with the **ESP32-S3 Version**. 

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





<br>



## Non API Version
<details>
  <summary>Non API Version</summary>














---

## Arduino IDE Installation

- Download and install [**Arduino IDE**](https://www.arduino.cc/en/software/)


- In Arduino IDE, go to File → Preferences → add this URL to Additional Board Manager URLs:
```url
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
```
  Then open Tools → Board → Board Manager, search for ESP32, and click Install.


- Plug your ESP32-C3 into your computer with a USB cable.
- Then in Arduino IDE, go to Tools → Board and select ESP32C3 Dev Module.
- Copy & paste the following code into a new sketch in Arduino IDE:


```ino

#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <HTTPClient.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

// === CONFIG ===
const char* WIFI_SSID = "YOUR_WIFI";                     // CHange to your Wi-Fi name
const char* WIFI_PASS = "YOUR_PASSWORD";                 // Change to your Wi-Fi password
const char* STEAM_ID = "YOUR_STEAMID";                   // Your SteamID
const unsigned long FETCH_INTERVAL_MS = 10UL * 60UL * 1000UL; // 10 minutes

#define I2C_SDA_PIN 8
#define I2C_SCL_PIN 9

// === HTML PARSING HELPERS ===

String extractBetween(String data, String startTag, String endTag) {
  int start = data.indexOf(startTag);
  if (start == -1) return "--";
  start += startTag.length();
  int end = data.indexOf(endTag, start);
  if (end == -1) return "--";
  String result = data.substring(start, end);
  result.trim();
  return result;
}

String fetchProfileHtml(const char* steamid) {
  if (WiFi.status() != WL_CONNECTED) {
    Serial.println("No WiFi, skipping fetch");
    return String();
  }

  String host = "steamladder.com";
  String url = String("/profile/") + steamid + "/";
  WiFiClientSecure client;
  client.setInsecure();
  HTTPClient https;
  String full = "https://" + host + url;

  if (!https.begin(client, full)) {
    Serial.println("HTTPS begin failed");
    return String();
  }

  https.setUserAgent("esp-steam-display/1.0");
  int code = https.GET();
  String payload = "";
  if (code == HTTP_CODE_OK) {
    payload = https.getString();
  } else {
    Serial.printf("HTTP error: %d\n", code);
  }
  https.end();
  return payload;
}

// === DATA EXTRACTION ===

String extractUsername(String html) {
  return extractBetween(html, "<div class=\"long-name\" itemprop=\"name\">", "</div>");
}


String extractLevel(String html) {
  String startTag = "<span class=\"friendPlayerLevelNum\">";
  String endTag = "</span>";
  int searchFrom = 0;
  int count = 0;
  int starts[10];  // up to 10 level spans

  // Find all occurrences of level spans
  while (true) {
    int start = html.indexOf(startTag, searchFrom);
    if (start == -1) break;
    start += startTag.length();
    if (count < 10) starts[count++] = start;
    int end = html.indexOf(endTag, start);
    if (end == -1) break;
    searchFrom = end + endTag.length();
  }

  if (count == 0) return "--";

  // If 5 numbers (rolling display), pick the 3rd (center)
  int middleIndex;
  if (count == 5) {
    middleIndex = 2; // 0,1,[2],3,4
  } else {
    // otherwise fallback to center
    middleIndex = (count - 1) / 2;
  }

  int start = starts[middleIndex];
  int end = html.indexOf(endTag, start);
  String result = html.substring(start, end);
  result.trim();
  return result;
}
// ------------------------------

String extractPlaytime(String html) {
  String section = extractBetween(html, "<th scope=\"row\">Total Playtime</th>", "</tr>");
  return extractBetween(section, "<td>", "hours");
}

String extractGames(String html) {
  String text = extractBetween(html, "<div class=\"game-text-stats\">", "</div>");
  text.replace("Games", "");
  text.trim();
  return text;
}

// === DISPLAY ===

void drawDisplay(String username, String level, String playtime, String games, bool wifiOK) {
  display.clearDisplay();
  display.setTextColor(SSD1306_WHITE);

  display.setTextSize(1);

  // Center username
  int16_t x1, y1;
  uint16_t w, h;
  display.getTextBounds(username, 0, 0, &x1, &y1, &w, &h);
  display.setCursor((SCREEN_WIDTH - w) / 2, 0);
  display.println(username);

  if (!wifiOK) {
    display.setCursor(SCREEN_WIDTH - 30, 0);
    display.print("WiFi!");
  }

  display.drawLine(0, 10, SCREEN_WIDTH, 10, SSD1306_WHITE);

  display.setTextSize(2);

  // Center LVL text
  String lvlText = "LVL: " + level;
  display.getTextBounds(lvlText, 0, 0, &x1, &y1, &w, &h);
  display.setCursor((SCREEN_WIDTH - w) / 2, 16);
  display.print(lvlText);

  display.setTextSize(1);
  display.setCursor(0, 42);
  display.print("Playtime:");
  display.setCursor(70, 42);
  display.print(playtime);
  display.print("h");

  display.setCursor(0, 54);
  display.print("Games:");
  display.setCursor(70, 54);
  display.print(games);

  display.display();
}

// === WIFI ===

void connectWiFi() {
  if (WiFi.status() == WL_CONNECTED) return;
  Serial.printf("Connecting to %s", WIFI_SSID);
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  unsigned long start = millis();
  while (WiFi.status() != WL_CONNECTED && millis() - start < 10000) {
    delay(300);
    Serial.print(".");
  }
  if (WiFi.status() == WL_CONNECTED) {
    Serial.printf("\nConnected, IP: %s\n", WiFi.localIP().toString().c_str());
  } else {
    Serial.println("\nWiFi connect failed.");
  }
}

// === MAIN ===

void setup() {
  Serial.begin(115200);
  Wire.begin(I2C_SDA_PIN, I2C_SCL_PIN);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.display();

  WiFi.config(INADDR_NONE, INADDR_NONE, INADDR_NONE);
  WiFi.setHostname("SteamDisplayESP");

  connectWiFi();
  drawDisplay("--", "--", "--", "--", WiFi.status() == WL_CONNECTED);
}

unsigned long lastFetch = 0;
String cachedUsername = "--", cachedLevel = "--", cachedPlaytime = "--", cachedGames = "--";

void loop() {
  connectWiFi();

  unsigned long now = millis();
  if (now - lastFetch >= FETCH_INTERVAL_MS || lastFetch == 0) {
    lastFetch = now;
    if (WiFi.status() == WL_CONNECTED) {
      Serial.println("Fetching SteamLadder profile...");
      String html = fetchProfileHtml(STEAM_ID);
      if (html.length() > 0) {
        cachedUsername = extractUsername(html);
        cachedLevel = extractLevel(html); 
        cachedPlaytime = extractPlaytime(html);
        cachedPlaytime.replace(",", "");
        cachedGames = extractGames(html);
        cachedGames.replace(" ", "");

        Serial.printf("Parsed: %s | LVL %s | Playtime %sh | Games %s\n",
                      cachedUsername.c_str(), cachedLevel.c_str(),
                      cachedPlaytime.c_str(), cachedGames.c_str());
      } else {
        Serial.println("Fetch failed - keeping old values");
      }
    }
  }

  drawDisplay(cachedUsername, cachedLevel, cachedPlaytime, cachedGames,
              WiFi.status() == WL_CONNECTED);

  delay(500);
}


```

- Replace the following placeholders:
  - **`YOUR_WIFI`**
  - **`YOUR_PASSWORD`**
  - **`YOUR_STEAMID`**

- Upload the code to your ESP32C3
- Done


### Having issues?

Try the following:

- Is [**SteamLadder**](https://steamladder.com) up?
- Try another username, for example try the top players username.
- Create a issue or send me a message on MakerWorld.

</details>





