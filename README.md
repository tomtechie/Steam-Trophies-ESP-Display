# Steam-Trophies-ESP-Display (Releasing 2025-10-17)


![IMG_5316](https://github.com/user-attachments/assets/56ce7daa-5c21-4405-b9ac-3e49ced594bb)


## [**3D model files can be found on MakerWorld**](https://makerworld.com/en/@tomtechie) 

### This project shows your Steam stats on a ESP32-C3 with an SSD1306 OLED display.


It runs completely standalone on an ESP32-C3. It fetches your Steam stats — Level, Total Playtime, and Total Games Owned — and displays them on a single OLED screen for quick viewing.
The ESP32-C3 connects to Wi-Fi and retrieves your Steam data from SteamLadder using HTTPS requests. The screen updates automatically every 10 minutes (configurable).

![IMG_5305](https://github.com/user-attachments/assets/70979f45-9a59-4226-a080-4f39850106c7)





---

## Installation


## Arduino IDE Steps
<details>
  <summary>🟢 Arduino IDE Installation</summary>


  ## Step 0: What You Need

| Item | Notes |
|------|-------|
| ESP32-C3 DevKit | Development board with USB-C |
| SSD1306 OLED 128x64 | I2C display |
| USB-C cable | For flashing & Power |
| Computer with Arduino IDE installed | Arduino IDE Programming|
| Steam account | For data |

---

## Step 1: Wiring the OLED to ESP32-C3



| ESP32-C3  | → | SSD1306 OLED |
|------|-------|-------|
|------|-------|-------|
|GPIO 8|→|SDA|
|GPIO 9|→|SCL|
|GND|→|GND|
|3.3V|→|VCC|

<img width="539" height="709" alt="SchematicForTrophy" src="https://github.com/user-attachments/assets/7b5961bb-f3f5-489b-9dac-5ca03852029c" />

---

## Step 2: Arduino IDE Installation

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





