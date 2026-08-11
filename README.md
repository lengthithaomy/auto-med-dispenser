# 💊 AutoDose — Automatic Medication Dispenser

> A standalone pill dispenser I built for the **VoltHacks Hackathon 2026**. It is designed to help people, especially elderly users, remember to take the right medication at the right time without needing a phone, Wi-Fi, or a subscription.

<p align="center">
  <img width="480" alt="AutoDose device" src="https://github.com/user-attachments/assets/b748ff1a-7736-40bc-b21c-3d4e8a1166e9" />
</p>

<p align="center">
  <a href="https://youtu.be/d-JQTOUDefM?si=IusnsNRkliP58Vxm">▶️ Check out the video</a>
</p>

I designed the 3D-printed parts in **Autodesk Inventor** and wrote the firmware using the **Arduino IDE**.

Since I originally built AutoDose for use at home, I made the day labels on the tray and the reminder audio in Vietnamese. I've also included the original Inventor file, so you can modify the tray if you want to use a different language. The reminder sounds can also be replaced — see [Changing the Reminder Sound](#-changing-the-reminder-sound).

---

## 📖 What is AutoDose?

I wanted to make a pill dispenser that could do more than just remind someone that it was time to take their medication.

AutoDose stores a week's worth of morning and evening doses. At the scheduled time, it rotates the correct compartment into position and releases the pill. It then uses an IR sensor to check whether the pill was actually picked up.

If it isn't, the device keeps reminding the user every 30 minutes.

The whole thing runs locally on an Arduino. There's no app, internet connection, account, or subscription involved.

---

## ✨ Features

* 🗓️ **15-compartment rotating tray** — 14 compartments for AM/PM doses across 7 days, plus one slot used for calibration/reset.
* ⚙️ **Drift-free tray positioning** — a 7.5:1 gear reduction makes each compartment exactly 2,048 motor steps apart.
* 👁️ **Pill pickup detection** — a TCRT5000 IR sensor checks both the pill drop and whether the user has picked it up.
* 🔊 **Sound, light, and LCD reminders** — the DFPlayer Mini plays a voice reminder, while an LED and 16x2 LCD show the current status.
* ⏰ **Repeating reminders** — if the pill isn't picked up, the reminder repeats every 30 minutes.
* 🔌 **Works without the internet** — the DS3231 RTC keeps track of the time, so the dispenser doesn't need Wi-Fi or a phone.

---

## 🔩 Bill of Materials

| Component                               | Qty | Operating Voltage | Function                         |
| --------------------------------------- | :-: | :---------------: | -------------------------------- |
| Arduino Uno R3                          |  1  |         5V        | Main controller                  |
| RTC DS3231                              |  1  |         5V        | Keeps track of the time          |
| 28BYJ-48 Stepper Motor + ULN2003 Driver |  1  |         5V        | Rotates the tray                 |
| TCRT5000 IR Reflective Sensor           |  1  |       3.3–5V      | Detects pill drop and pickup     |
| LCD 1602 (I2C)                          |  1  |         5V        | Shows the time and system status |
| DFPlayer Mini + 0.5W 8Ω Speaker         |  1  |       3.2–5V      | Plays voice reminders            |
| Red LED + 220Ω Resistor                 |  1  |         2V        | Indicates an active reminder     |
| 5V USB Power Supply                     |  1  |         5V        | Powers the device                |

---

## ⚙️ Mechanical & Gear System

One of the biggest problems I ran into was keeping the tray aligned.

The 28BYJ-48 has 4,096 steps per full rotation. If the motor directly rotated the 15-slot tray, the number of steps per compartment would be:

$$
\frac{4096}{15} \approx 273.07 \text{ steps/slot}
$$

That isn't an integer, so the tray would gradually lose alignment after repeated movements.

### The 7.5:1 gear reduction

I solved this by adding a **7.5:1 gear reduction** between the motor and the tray.

That gives:

$$
4096 \times 7.5 = 30{,}720
$$

steps for one full tray rotation.

Since the tray has 15 compartments:

$$
\frac{30{,}720}{15} = 2{,}048
$$

So every compartment is exactly **2,048 motor steps** apart.

This means the motor always lands on the same position for each compartment instead of accumulating a small positioning error every time the tray moves.

---

## 🔄 How It Works

<p align="center">
  <img width="480" alt="Workflow diagram" src="https://github.com/user-attachments/assets/304e4ebc-f762-4a0d-98ba-375febf228fe" />
</p>

### 1. Scheduling

The Arduino checks the current time from the DS3231 against the scheduled dosing times:

* **07:00** — morning dose
* **19:00** — evening dose

### 2. Dispensing

When it's time, the stepper motor moves **2,048 steps** to bring the correct compartment above the drop chute.

### 3. Reminder

The pill falls into the receiving tray. The LED turns on and the DFPlayer Mini plays a voice reminder.

### 4. Checking if the pill was picked up

The IR sensor monitors the receiving area.

* ✅ **Pill picked up** — the system detects the change, plays a confirmation message, turns off the LED, and records the dose as completed.
* ⏳ **Pill still there** — the reminder stays active and repeats every 30 minutes until the pill is picked up.

---

## 🚀 Getting Started

1. Assemble the circuit using the components listed in the [Bill of Materials](#-bill-of-materials).
2. Upload the Arduino sketch from `/code` to the Arduino Uno.
3. Set the current date and time on the DS3231.
4. Fill the 14 medication compartments (AM/PM × 7 days). Leave the calibration slot empty.
5. Plug in the 5V USB power supply.
6. The dispenser will start checking the schedule automatically.

---

## 🔊 Changing the Reminder Sound

The reminder audio is stored on the **DFPlayer Mini**.

The included audio files are in Vietnamese because that's what I use at home. You can replace them with your own recordings if you want different languages or sounds.

See the relevant instructions in the project files for the required filenames and SD card setup.

---

## 🛠️ Things I'd Like to Improve

There are still quite a few things I'd change if I had more time:

* Add support for multiple users
* Add a battery backup so the device keeps running during a power outage
* Make different tray sizes for different medication schedules
* Add an optional caregiver app while keeping the device usable without it
* Improve the mechanical design and make the 3D-printed parts easier to assemble

---

## 📜 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

Built for the **VoltHacks Hackathon 2026** as a first-year college project.

This started as something I wanted to make for use at home, and the hackathon gave me a good excuse to actually build it.
