 marauder_notes.md
Purpose: Dev log, hardware debugging notes, and challenges you faced.
markdown
Copy
Edit
# 🛠️ Marauder NRF24L01 & TFT Wiring Notes

## Display Issues

The ILI9341 screen had no pinout label. I tried wiring using common guides but got a white screen.
Eventually found that my model had different pin assignments.
Used the Adafruit_ILI9341 and GFX libraries for testing with this code:
```
tft.begin();
tft.fillScreen(ILI9341_BLACK);
tft.setCursor(0, 0);
tft.print("Display OK");
```

NRF24L01 Tips
The module crashed without a capacitor. 10μF across VCC and GND fixed this.

Voltage must be exactly 3.3V — no USB power via breadboard.

Use radio.isChipConnected() to test if it initializes.

TFT & NRF SPI Conflict
They share the same SPI lines (MOSI, MISO, SCK) but must use separate CS/CE pins.
TFT CS: GPIO 15
NRF CSN: GPIO 5
NRF CE: GPIO 27


---

## 📄 `nrf_test.ino`

```
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>

RF24 radio(27, 5); // CE, CSN

void setup() {
  Serial.begin(115200);
  if (!radio.begin()) {
    Serial.println("NRF24L01 connection failed");
    while (1);
  }
  radio.setPALevel(RF24_PA_HIGH);
  radio.openReadingPipe(1, "node1");
  radio.startListening();
  Serial.println("Listening...");
}

void loop() {
  if (radio.available()) {
    char data[32] = "";
    radio.read(&data, sizeof(data));
    Serial.print("Received: ");
    Serial.println(data);
  }
}
```
