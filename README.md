### 💊 AutoDose — Automatic Medication Dispenser

> A standalone, internet-free pill dispenser built for the *VoltHacks Hackathon 2026g* project — designed to help patients (especially the elderly) take the right medication at the right time, without needing a smartphone, subscription, or Wi-Fi. 

<p align="center">
  <img width="480" alt="AutoDose device" src="https://github.com/user-attachments/assets/b748ff1a-7736-40bc-b21c-3d4e8a1166e9" />
</p>

<p align="center">
  <a href="https://github.com/user-attachments/assets/a4bc2392-90bc-4250-8f03-b62334f0b14c">▶️ Watch the demo video</a>
</p>

I use Inventor to create the 3D printed files, Arduino IDE to compile the code for this project.

Because it was made for my own home use, I decided to design the day of the week on the tray and the reminder sound to be in Vietnamese. I also included the original Autodesk Inventor file for anyone who want to change the language of the tray. To change the reminder sound for your own liking, check out #change-reminder-sound
---

## 📖 Overview

Missed or double doses are a common problem for patients managing daily medication — especially older adults living alone. Most "smart" pill dispensers on the market solve this with apps and cloud subscriptions, which adds cost and complexity for the people who need them most.

**AutoDose** takes a different approach: a fully mechanical/electronic dispenser that runs **completely standalone**, with no app, no internet connection, and no recurring fees. It holds a full week of AM/PM doses, dispenses automatically on schedule, confirms the pill was actually picked up, and reminds the patient with sound, light, and text if they forget.

---

## ✨ Features

- 🗓️ **15-compartment rotating tray** — a full week of twice-daily (AM/PM) dosing, plus 1 dedicated calibration/reset slot.
- ⚙️ **Precision-engineered drive train** — a 7.5:1 gear reduction on a 28BYJ-48 stepper motor gives exact integer step alignment (2,048 steps per compartment), so the tray never drifts out of position.
- 👁️ **Pill delivery verification** — a TCRT5000 IR reflective sensor confirms both that the pill dropped *and* that the patient actually retrieved it.
- 🔊 **Multi-sensory alerts** — spoken voice prompts via DFPlayer Mini (*"Đã đến giờ uống thuốc..."*), an LED indicator, and live status on a 16x2 LCD.
- ⏰ **Persistent reminders** — if a dose isn't retrieved, the alarm automatically repeats every 30 minutes until it is.
- 🔌 **Zero-dependency operation** — powered by a standard 5V USB supply, with an onboard DS3231 real-time clock keeping accurate schedules with no internet connection required.

---

## 🧠 System Architecture

<p align="center">
  <img width="500" alt="System architecture" src="https://github.com/user-attachments/assets/6ec572ab-8b4a-48cd-b7c5-66207dadb1fd" />
</p>

---

## 🔩 Bill of Materials

| Component | Qty | Operating Voltage | Function |
|---|:---:|:---:|---|
| Arduino Uno R3 | 1 | 5V | Central processing unit and control logic |
| RTC DS3231 | 1 | 5V | Real-time clock module for accurate scheduling |
| Stepper Motor 28BYJ-48 + ULN2003 Driver | 1 | 5V | Rotates the tray via 7.5:1 gear reduction |
| TCRT5000 IR Reflective Sensor | 1 | 3.3–5V | Detects pill drop and user retrieval |
| LCD 1602 (I2C interface) | 1 | 5V | Displays system status and digital clock |
| DFPlayer Mini + 0.5W 8Ω Speaker | 1 | 3.2–5V | Plays spoken audio reminders |
| Red LED + 220Ω Resistor | 1 | 2V | Visual dispensing alert |
| 5V USB Power Supply | 1 | 5V | Continuous system power |

---

## ⚙️ Mechanical & Gear System Design

The core mechanical challenge was eliminating **positional drift**: dividing the tray directly by the motor's native resolution doesn't produce a clean number of steps per slot, which causes small alignment errors to accumulate over time.

**Base motor:** 28BYJ-48 stepper — 4,096 steps per full rotation.

**Why direct drive doesn't work:**

$$\frac{4096 \text{ steps}}{15 \text{ slots}} \approx 273.07 \text{ steps/slot}$$

This fractional result means the tray would slowly drift out of alignment over repeated cycles.

**The fix — a 7.5:1 gear reduction:**

$$\text{Total steps per tray rotation} = 4096 \times 7.5 = 30{,}720 \text{ steps}$$

$$\text{Steps per compartment} = \frac{30{,}720}{15} = 2{,}048 \text{ steps}$$

With this ratio, every compartment advance lands on exactly **2,048 integer motor steps** — guaranteeing repeatable, drift-free slot alignment indefinitely.

---

## 🔄 Operational Workflow

<p align="center">
  <img width="480" alt="Workflow diagram" src="https://github.com/user-attachments/assets/304e4ebc-f762-4a0d-98ba-375febf228fe" />
</p>

1. **Scheduling** — The Arduino continuously checks the DS3231's current time against the target dosing times (`07:00:00` for AM, `19:00:00` for PM).
2. **Dispensing** — At the target time, the stepper motor advances exactly 2,048 steps, positioning the correct compartment over the drop chute.
3. **Delivery & Alert** — The sloped compartment floor drops the pill into the receiving tray. The LED turns on and DFPlayer Mini plays a spoken reminder.
4. **Verification:**
   - ✅ **Pill retrieved** — the IR sensor detects the state change (LOW → HIGH), the system plays a confirmation ("Đã uống thuốc"), turns off the LED, and logs the successful dose.
   - ⏳ **Pill not retrieved** — if no retrieval is detected within 30 minutes, the reminder alarm re-triggers automatically and repeats every 30 minutes until the pill is taken.

---

## 🚀 Getting Started

1. Assemble the circuit according to the components listed in the [Bill of Materials](#-bill-of-materials).
2. Flash the Arduino sketch (see `/firmware`) to the Arduino Uno R3.
3. Set the current date/time on the DS3231 module (one-time setup, or on battery replacement).
4. Load pills into the 14 weekly compartments (AM/PM × 7 days), leaving the calibration slot empty.
5. Connect the 5V USB power supply — the device will begin tracking scheduled dosing times immediately.

---

## 🛠️ Future Improvements

- Multiple user profiles for multi-patient households
- Optional companion app for caregivers (while keeping the core device fully standalone)
- Battery backup for power-loss resilience
- Expandable tray sizes for more frequent dosing schedules

---

## 📜 License

*Add your chosen license here (e.g. MIT).*

---

## 🙏 Acknowledgments

Built as a project for the *VoltHacks Hackathon 2026*, first year of college.
