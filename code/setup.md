## 💻 Software Setup & Configuration

### Prerequisites

You'll need:

* [Arduino IDE](https://www.arduino.cc/en/software) (v1.8.x or v2.x)
* Arduino Uno R3
* A USB cable
* A microSD card for the DFPlayer Mini

The following Arduino libraries are used by the project:

* `RTClib` — by Adafruit
* `LiquidCrystal_I2C`
* `DFRobotDFPlayerMini`
* `SoftwareSerial`
* `AccelStepper`

You can install these libraries through **Arduino IDE → Library Manager**.

---

### 📥 Uploading the Code

1. Clone this repository:

```bash
git clone https://github.com/your-username/automatic-medication-dispenser.git
```

2. Open the `.ino` sketch in Arduino IDE.

3. Make sure the correct board is selected:

**Tools → Board → Arduino AVR Boards → Arduino Uno**

4. Connect the Arduino Uno to your computer using USB.

5. Click **Upload**.

Once the upload is complete, AutoDose should start running as soon as the Arduino is powered.

---

### ⏰ Changing the Medication Schedule

The default schedule is:

* **07:00:00** — morning medication
* **19:00:00** — evening medication

You can change these times near the top of the code:

```cpp
// Time setting
int amHr = 7,  amMin = 0, amSec = 0;
int pmHr = 19, pmMin = 0, pmSec = 0;
```

For example, to change the morning dose to 8:30 AM:

```cpp
int amHr = 8, amMin = 30, amSec = 0;
```

The values use the 24-hour clock.

---

### 🕐 Setting the RTC Time

The DS3231 keeps track of the date and time even when the Arduino isn't actively checking the schedule.

To manually set it:

1. Upload the code and connect the Arduino to your computer.
2. Open **Serial Monitor** in Arduino IDE.
3. Set the baud rate to **9600**.
4. Type:

```text
u
```

5. The Arduino will ask for the following values one at a time:

```text
year
month
day
hours
minutes
seconds
```

For example:

```text
2026
8
11
14
30
00
```

The RTC will then be updated with the new date and time.

If you want to cancel the process, enter:

```text
-1
```

---

## 🔊 Changing the Reminder Sound

AutoDose uses a **DFPlayer Mini** to play the voice reminders. The audio files are stored on a microSD card inserted into the DFPlayer Mini.

The current code expects the following folder structure:

```text
SD Card
├── 01
│   └── 001.mp3
└── 02
    └── 001.mp3
```

### 🌅 Morning reminder

The morning sound is loaded from:

```text
/01/001.mp3
```

This is triggered by:

```cpp
myDFPlayer.playFolder(1, 1);
```

### 🌇 Evening reminder

The evening sound is loaded from:

```text
/02/001.mp3
```

This is triggered by:

```cpp
myDFPlayer.playFolder(2, 1);
```

### 🎵 Adding your own sounds

To replace the default Vietnamese reminders:

1. Prepare a **microSD card** supported by the DFPlayer Mini.
2. Format the card as **FAT32**.
3. Create two folders in the root of the SD card:

```text
01
02
```

4. Put your morning reminder in:

```text
01/001.mp3
```

5. Put your evening reminder in:

```text
02/001.mp3
```

6. Insert the SD card into the DFPlayer Mini.
7. Restart AutoDose and test both scheduled times.

The files should be **MP3 audio files**. Keeping the filenames as `001.mp3` and the folder names as `01` and `02` is important because the Arduino code specifically looks for those locations.

You can record your own voice, use another language, or replace the reminder with a simple sound.

### 🔊 Changing the volume

The volume is controlled in `setup()`:

```cpp
myDFPlayer.volume(30);
```

The DFPlayer Mini uses a volume range from **0 to 30**.

For example:

```cpp
myDFPlayer.volume(20);
```

would make the reminder quieter.

---

## 🧪 How the Reminder System Works

When the scheduled time is reached, the Arduino:

1. Moves the tray by **2,048 steps**.
2. Turns on the LED.
3. Plays the corresponding audio file from the DFPlayer Mini.
4. Waits for the TCRT5000 sensor to detect whether the pill has been picked up.

When the sensor changes from `LOW` to `HIGH`, the Arduino considers the medication to have been taken, turns off the LED, and displays:

```text
Da uong thuoc
```

on the LCD.

The current version **does not repeat the audio reminder automatically**. The sound is played once when the medication is dispensed, while the LED remains on until the pill is picked up.

---

## 📝 A Note About the Current Firmware

The code in this repository is the version I used for the hackathon project, so some parts are intentionally simple.

For example, the AM and PM doses have their own state variables:

```cpp
amDispensed
amShown
amTaken

pmDispensed
pmShown
pmTaken
```

This prevents the system from repeatedly dispensing the same dose during the same scheduled second.

After a dose is picked up, the LCD message is cleared after 5 minutes:

```cpp
if (amTaken && millis() - takenTimestamp >= 300000)
```

The same logic is used for the evening dose.
