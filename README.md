<img width="512" height="288" alt="auto-med-dispenser" src="https://github.com/user-attachments/assets/abfca2df-5c90-41b5-a494-9ab77f361cee" />


## Video
https://github.com/user-attachments/assets/a4bc2392-90bc-4250-8f03-b62334f0b14c


A low-cost Arduino UNO R3-based automatic medication dispenser with a 3D-printed gear mechanism, real-time scheduling, and reminder system for home use.

## Features

<img width="512" height="238" alt="architecture" src="https://github.com/user-attachments/assets/6ec572ab-8b4a-48cd-b7c5-66207dadb1fd" />


- 15-compartment rotating tray accommodating twice-daily dispensing (AM/PM) for a full week, plus 1 calibration/reset slot.
- 7.5:1 gear reduction ratio driving a 28BYJ-48 stepper motor for exact integer step alignment (2048 steps per compartment).
- Integrated TCRT5000 IR reflective sensor to verify pill drop and user retrieval from the receiving tray.
- Audio prompts via DFPlayer Mini speaker ("Đã đến giờ uống thuốc...") and visual alerts via LED indicator and LCD 1602 display.
- Automatically repeats reminder alarms every 30 minutes if pills remain unretrieved.
- Powered via standard 5V USB, utilizing an RTC DS3231 real-time clock to run completely standalone without internet dependency or subscription fees.

---

## BOM List

| Component | Quantity | Operating Voltage | Function |
| :--- | :---: | :---: | :--- |
| **Arduino Uno R3** | 1 | 5V | Central processing unit and control logic |
| **RTC DS3231** | 1 | 5V | Real-Time Clock module for accurate scheduling |
| **Stepper Motor 28BYJ-48 + ULN2003 Driver** | 1 | 5V | Rotates tray with 7.5:1 gear reduction |
| **TCRT5000 IR Reflective Sensor** | 1 | 3.3V - 5V | Detects pill drop and user retrieval |
| **LCD 1602 (I2C interface)** | 1 | 5V | System status display and digital clock |
| **DFPlayer Mini + 0.5W 8Ω Speaker** | 1 | 3.2V - 5V | Audio prompts and spoken voice reminders |
| **Red LED Indicator + 220Ω Resistor** | 1 | 2V | Visual dispensing alert signal |
| **5V USB Power Supply Cable** | 1 | 5V | Continuous system power supply |

---

## Mechanical & Gear system design

To eliminate positioning drift caused by fractional motor step divisions:
- **Base Motor:** 28BYJ-48 Stepper Motor (4,096 steps/rotation).
- **Direct Drive Limitation:** $\frac{4096 \text{ steps}}{15 \text{ slots}} \approx 273.07 \text{ steps/slot}$ (Non-integer leading to position errors).
- **Gear Reduction (7.5:1 Ratio):**
  $$\text{Total Steps per Tray Rotation} = 4096 \times 7.5 = 30,720 \text{ steps}$$
  $$\text{Steps per Compartment} = \frac{30,720}{15} = 2,048 \text{ steps}$$

Each rotation advances by exactly **2,048 integer motor steps**, ensuring repeatable slot alignment over long term operation.

---

## Workflow

<img width="481" height="512" alt="workflow" src="https://github.com/user-attachments/assets/304e4ebc-f762-4a0d-98ba-375febf228fe" />


### Operational logic:

1. Arduino Uno R3 constantly checks RTC time against target schedules (`07:00:00` for AM and `19:00:00` for PM).
2. At target time, motor steps 2,048 counts forward to position the designated compartment over the drop chute.
3. Sloped tray compartment delivers pill to receiving tray. LED turns ON and DFPlayer broadcasts audio prompt.
4. Verification:
   - If IR Sensor detects pill taken (State transitions LOW $\rightarrow$ HIGH), system plays confirmation ("Đã uống thuốc"), turns off LED, and logs success.
   - If pill is not taken within 30 minutes, reminder alarm re-triggers until retrieved.

