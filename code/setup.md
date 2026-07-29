##Operation logic

<img width="263" height="512" alt="op-logic" src="https://github.com/user-attachments/assets/8f935207-ed9a-4d9d-a5b9-46f24c63f8fe" />


## Software Setup & configuration

### Prerequisites
- [Arduino IDE](https://www.arduino.cc/en/software) (v1.8.x or v2.x)
- Required Arduino Libraries:
  - `RTClib` (by Adafruit)
  - `LiquidCrystal_I2C`
  - `DFRobotDFPlayerMini`
  - `SoftwareSerial`
  - `AccelStepper`

### Flashing code & time setup
1. Clone this repository:
   ```bash
   git clone https://github.com/your-username/automatic-medication-dispenser.git
   ```
2. Open the `.ino` sketch in Arduino IDE.
3. Adjust target reminder schedules in the source code if needed:
   ```cpp
   // Time settings
   int amHr = 7,  amMin = 0, amSec = 0;  // Morning schedule
   int pmHr = 19, pmMin = 0, pmSec = 0;  // Evening schedule
   ```
4. Connect Arduino Uno R3 via USB and upload the sketch.
5. **Adjust RTC Time (Optional):** Open Serial Monitor at `9600 baud`, type `'u'`, and follow prompts to calibrate real-time clock values (`YYYY/MM/DD HH:MM:SS`).

