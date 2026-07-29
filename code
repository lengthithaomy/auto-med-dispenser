#include <Arduino.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <RTClib.h>
#include <AccelStepper.h>
#include <SoftwareSerial.h>
#include <DFRobotDFPlayerMini.h>

// ---------------- LCD ----------------
LiquidCrystal_I2C lcd(0x27, 16, 2);

// ---------------- RTC ----------------
RTC_DS3231 rtc;

// ---------------- Stepper Motor ----------------
#define motorPin1  8
#define motorPin2  9
#define motorPin3  10
#define motorPin4  11
#define MotorInterfaceType 8
AccelStepper stepper = AccelStepper(MotorInterfaceType, motorPin1, motorPin3, motorPin2, motorPin4);

// ---------------- DFPlayer Mini ----------------
SoftwareSerial mySoftwareSerial(6, 7); // RX, TX
DFRobotDFPlayerMini myDFPlayer;

// ---------------- Time setting ----------------
int amHr = 7, amMin = 0, amSec = 0;
int pmHr = 19, pmMin = 0, pmSec = 0;

// ---------------- Constants ----------------
const int led = 2;
const int sensorPin = 4;   // TRTC5000

// ---------------- Variables ----------------
int sensorState = 0;

// --- state flags ---
bool amDispensed = false;
bool amShown = false;
bool amTaken = false;

bool pmDispensed = false;
bool pmShown = false;
bool pmTaken = false;

unsigned long takenTimestamp = 0;

// ---------------- VALIDATION ----------------
typedef struct minMax_t {
  int minimum;
  int maximum;
};

bool checkInput(const int value, const minMax_t minMax) {
  if (value >= minMax.minimum && value <= minMax.maximum)
    return true;

  Serial.print(value);
  Serial.print(" is out of range ");
  Serial.print(minMax.minimum);
  Serial.print(" - ");
  Serial.println(minMax.maximum);
  return false;
}

// ---------------- UPDATE RTC ----------------
void updateRTC() {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Edit Mode...");

  const char txt[6][15] = {
    "year [4-digit]", "month [1~12]", "day [1~31]",
    "hours [0~23]", "minutes [0~59]", "seconds [0~59]"
  };

  const minMax_t minMax[] = {
    {2000, 9999}, {1, 12}, {1, 31},
    {0, 23}, {0, 59}, {0, 59},
  };

  String str = "";
  long newDate[6];
  DateTime newDateTime;

  while (1) {
    while (Serial.available()) Serial.read();

    for (int i = 0; i < 6; i++) {
      while (1) {
        Serial.print("Enter ");
        Serial.print(txt[i]);
        Serial.print(" (or -1 to abort) : ");

        while (!Serial.available());
        str = Serial.readString();

        if (str == "-1" || str == "-1\n" || str == "-1\r" || str == "-1\r\n") {
          Serial.println("\nABORTED");
          return;
        }

        newDate[i] = str.toInt();
        if (checkInput(newDate[i], minMax[i])) break;
      }
      Serial.println(newDate[i]);
    }

    newDateTime = DateTime(
      newDate[0], newDate[1], newDate[2],
      newDate[3], newDate[4], newDate[5]
    );

    if (newDateTime.isValid()) break;
    Serial.println("Date/time invalid, try again.");
  }

  rtc.adjust(newDateTime);
  Serial.println("RTC Updated!");
}

// ---------------- UPDATE LCD ----------------
void updateLCD() {
  DateTime rtcTime = rtc.now();
  char timeBuffer[] = "hh:mm:ss";

  lcd.setCursor(0, 0);
  lcd.print(" TG: ");
  lcd.setCursor(5, 0);
  lcd.print(rtcTime.toString(timeBuffer));
}

// ---------------- ROTATE MOTOR ----------------
void rotateMotor() {
  stepper.enableOutputs();
  stepper.setCurrentPosition(0);

  while (stepper.currentPosition() != -2048) {
    stepper.setSpeed(-500);
    stepper.runSpeed();
  }
  stepper.disableOutputs();

  digitalWrite(led, HIGH);  // LED sáng lúc thuốc rơi
}

// ---------------- ROTATE AM ----------------
void rotateAM() {
  rotateMotor();
  amDispensed = true;
  amShown = false;
  amTaken = false;

  // Phát âm thanh buổi sáng sau khi motor quay xong
  myDFPlayer.playFolder(1, 1); // Folder 01, file 001.mp3
  Serial.println("Chao buoi sang");
}

// ---------------- ROTATE PM ----------------
void rotatePM() {
  rotateMotor();
  pmDispensed = true;
  pmShown = false;
  pmTaken = false;

  // Phát âm thanh buổi chiều sau khi motor quay xong
  myDFPlayer.playFolder(2, 1); // Folder 02, file 001.mp3
  Serial.println("Chao buoi chieu");

}

// ---------------- SETUP ----------------
void setup() {
  stepper.setMaxSpeed(1000);

  Serial.begin(9600);

  // LCD setup
  lcd.init();        // chắc chắn LCD được init
  lcd.backlight();   // bật đèn nền
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Khoi dong..."); // test chữ hiển thị

  rtc.begin();

  pinMode(led, OUTPUT);
  pinMode(sensorPin, INPUT);

  // ---------------- DFPlayer Setup ----------------
  mySoftwareSerial.begin(9600);
  if (!myDFPlayer.begin(mySoftwareSerial)) {
    Serial.println(F("Khong ket noi duoc DFPlayer Mini!"));
    while(true);
  }
  myDFPlayer.volume(30); // âm lượng 0~30
}

// ---------------- LOOP ----------------
void loop() {
  DateTime rtcTime = rtc.now();
  updateLCD();

  sensorState = digitalRead(sensorPin);  // LOW = có thuốc

  // ----------- AM DISPENSE -----------
  if (rtcTime.hour() == amHr && rtcTime.minute() == amMin && rtcTime.second() == amSec) {
    lcd.clear();
    rotateAM();
  }

  if (amDispensed && !amTaken) {
    if (sensorState == LOW && !amShown) {
      lcd.setCursor(0, 1);
      lcd.print("Thuoc buoi sang ");
      amShown = true;
    }

    if (sensorState == HIGH && amShown) {
      lcd.setCursor(0, 1);
      lcd.print(" Da uong thuoc   ");
      digitalWrite(led, LOW);
      amTaken = true;
      takenTimestamp = millis();
    }
  }

  if (amTaken && millis() - takenTimestamp >= 300000) { // 5 phút
    lcd.setCursor(0, 1);
    lcd.print("                ");
  }

  // ----------- PM DISPENSE -----------
  if (rtcTime.hour() == pmHr && rtcTime.minute() == pmMin && rtcTime.second() == pmSec) {
    lcd.clear();
    rotatePM();
  }

  if (pmDispensed && !pmTaken) {
    if (sensorState == LOW && !pmShown) {
      lcd.setCursor(0, 1);
      lcd.print("Thuoc buoi chieu");
      pmShown = true;
    }

    if (sensorState == HIGH && pmShown) {
      lcd.setCursor(0, 1);
      lcd.print(" Da uong thuoc   ");
      digitalWrite(led, LOW);
      pmTaken = true;
      takenTimestamp = millis();
    }
  }

  if (pmTaken && millis() - takenTimestamp >= 300000) {
    lcd.setCursor(0, 1);
    lcd.print("                ");
  }

  // ----------- SERIAL KEPT ORIGINAL -----------
  if (Serial.available()) {
    char input = Serial.read();
    if (input == 'u') updateRTC();
  }
}
