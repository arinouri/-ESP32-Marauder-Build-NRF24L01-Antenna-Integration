![image](https://github.com/user-attachments/assets/2437685a-7182-46aa-b2c4-f802272acbc9)📁 Project 1: ESP32 Marauder with NRF24L01 & TFT Display
🧠 Overview
This project involves building and flashing JustCallMeKoko's ESP32-Marauder, a wireless attack tool that runs on ESP32. The build is enhanced with a TFT display and an NRF24L01+PA+LNA long-range antenna to simulate and scan for 2.4GHz communications.

🔌 Hardware Used
ESP32 WROOM Dev board
TFT LCD (ILI9341, SPI, 2.4")
NRF24L01+PA+LNA module
Capacitor (10μF across VCC and GND on NRF)
Breadboard and jumper wires

⚙️ Wiring - NRF24L01
| NRF Pin | ESP32 Pin |
| ------- | --------- |
| VCC     | 3.3V      |
| GND     | GND       |
| CE      | GPIO 27   |
| CSN     | GPIO 5    |
| SCK     | GPIO 18   |
| MOSI    | GPIO 23   |
| MISO    | GPIO 19   |
| IRQ     | Not Used  |


⚙️ Wiring - TFT Display
| TFT Pin Label | Function        | ESP32 Label          | GPIO       |
| ------------- | --------------- | -------------------- | ---------- |
| VCC           | Power           | VIN or 3U3           | 5V or 3.3V |
| GND           | Ground          | GND                  | GND        |
| CS            | TFT Chip Select | TX2                  | GPIO17     |
| RESET         | Reset           | D5                   | GPIO5      |
| DC            | Data/Command    | RX2                  | GPIO16     |
| SDI(MOSI)     | SPI MOSI        | D23                  | GPIO23     |
| SCK           | SPI Clock       | D18                  | GPIO18     |
| LED           | Backlight       | D32                  | GPIO32     |
| SDO(MISO)     | SPI MISO        | D19                  | GPIO19     |
| T\_CLK        | Touch Clock     | D18                  | GPIO18     |
| T\_CS         | Touch CS        | D21                  | GPIO21     |
| T\_DIN        | Touch MOSI      | D23                  | GPIO23     |
| T\_DO         | Touch MISO      | D19                  | GPIO19     |
| T\_IRQ        | Touch IRQ       | VP or D36 (optional) | GPIO36     |

![image](https://github.com/user-attachments/assets/be3eff80-d8ec-4730-ae8a-34cdf7dd13b8)


⚠️ Challenges
The TFT display lacked a proper pinout guide. Manual testing and mapping of each pin were necessary, which involved extensive debugging with the ILI9341 test sketch to achieve reliable output. This process also provided insights into efficient use of SPI and display buffers in the Arduino IDE.

Snippet of code for NRF Adapter:
```
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>

RF24 radio(27, 5); // CE, CSN

void setup() {
  Serial.begin(115200);
  if (!radio.begin()) {
    Serial.println("NRF24 not responding");
    while (1);
  }
  radio.setPALevel(RF24_PA_HIGH);
  radio.openReadingPipe(1, "node1");
  radio.startListening();
}

void loop() {
  if (radio.available()) {
    char text[32] = "";
    radio.read(&text, sizeof(text));
    Serial.println(text);
  }
}
```


Ethical Use & Reflection
As a cybersecurity student, I approached this project with strict ethical intent. The ESP32-Marauder is a powerful educational tool that demonstrates how wireless attacks like deauthentication, probe floods, beacon spoofing, and BLE scanning work. However, I never used this device on unauthorized networks, public Wi-Fi, or real users.

Instead, I followed these key ethical guidelines during development:

What I Did:
Created a test environment using a private hotspot and my own BLE devices for experiments

Used the tool only on networks/devices I own or had explicit permission to test
Treated the project as a learning experience in wireless protocol behavior, not exploitation
Focused on understanding how attacks are performed so they can be detected or mitigated
What I Did Not Do:
Did not run deauthentication, beacon spam, or SSID flooding in public spaces
Did not interfere with any third-party or production network
Did not attempt to harvest MAC addresses, credentials, or location data from others
Did not share binaries or builds that could be easily misused by others without context

📚 What I Learned
This project taught me how wireless exploits function under the hood, beyond theory:

Low-level packet structures: I understood the anatomy of Wi-Fi frames and BLE advertising packets
Hardware tuning: I learned how voltage, timing, and SPI mapping affect performance and stability
Risk vs. utility in offensive tools: Even simple packet flooding can degrade a network — I now respect the power of these tools and the importance of ethical hacking boundaries
The limits of detection: Tools like this highlight how hard it is to trace spoofed or randomized MACs, deepening my appreciation for Wi-Fi IDS/IPS systems

Responsible Disclosure Reminder
Anyone using the ESP32-Marauder or similar tools should remember:

⚠️ Just because a tool is open-source doesn't mean it's safe to use anywhere.
Ethical hacking requires consent, boundaries, and purpose.

I encourage others to use this project in the spirit of education, research, and ethical testing — not disruption.

