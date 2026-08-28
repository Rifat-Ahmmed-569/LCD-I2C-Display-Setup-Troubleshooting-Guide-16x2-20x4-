# LCD I2C Display Setup & Troubleshooting Guide (16x2 / 20x4)

This guide helps beginners connect an I2C LCD display to an Arduino, find the correct I2C address, diagnose common issues, and print **"Hello World"** on both **16x2** and **20x4** displays.

---
<img width="1920" height="1080" alt="I2C LCD" src="https://github.com/user-attachments/assets/b6f9f7dd-a6b4-480a-a257-5001d5f00382" />

---

## Overview

An I2C LCD uses only four wires:

| LCD I2C Pin | Arduino Uno/Nano |
| ----------- | ---------------- |
| VCC         | 5V               |
| GND         | GND              |
| SDA         | A4               |
| SCL         | A5               |

<img width="400" height="455" alt="2c6a4403cf046091196d5e4eb3cb9aa0" src="https://github.com/user-attachments/assets/acaa4525-586f-4d11-b833-604d76e55aa1" />


### Benefits of I2C LCDs

* Uses only 2 communication pins
* Easy wiring
* Supports multiple devices on the same bus
* Ideal for beginner Arduino projects

---

## Required Components

* Arduino Uno or Nano
* I2C LCD Display (16x2 or 20x4)
* USB Cable
* Jumper Wires

---

## Required Library

Install the following library from the Arduino Library Manager:

```cpp
LiquidCrystal_I2C
```

---

## Common I2C Addresses

Most LCD modules use one of the following addresses:

```text
0x27
```

or

```text
0x3F
```

Do not assume the address. Always scan first.

---

# Step 1: Find the I2C Address

Upload the following I2C scanner sketch.

```cpp
#include <Wire.h>

void setup() {
  Serial.begin(9600);
  Wire.begin();

  Serial.println("I2C Scanner");
  Serial.println("Scanning...");
}

void loop() {

  byte found = 0;

  for (byte address = 1; address < 127; address++) {

    Wire.beginTransmission(address);

    if (Wire.endTransmission() == 0) {

      Serial.print("Found I2C Device at 0x");
      Serial.println(address, HEX);

      found++;
    }
  }

  if (found == 0) {
    Serial.println("No I2C devices found.");
  }

  delay(5000);
}
```

---

## Expected Output

Example:

```text
I2C Scanner
Scanning...
Found I2C Device at 0x27
```

or

```text
Found I2C Device at 0x3F
```

---

# Troubleshooting

## Case 1: No I2C Device Found

Output:

```text
No I2C devices found.
```

### Possible Causes

#### Incorrect Wiring

Verify the following connections:

| LCD | Arduino |
| --- | ------- |
| VCC | 5V      |
| GND | GND     |
| SDA | A4      |
| SCL | A5      |

#### No Power

Check:

* LCD backlight is ON
* Arduino power LED is ON

#### Loose Connections

Reconnect all jumper wires.

#### Damaged Module

Try another LCD or I2C backpack module.

---

## Case 2: Device Found but No Text Appears

### Wrong Contrast

Most I2C modules have a blue potentiometer.

Rotate it slowly until black blocks become visible.

### Wrong Address

The scanner found:

```text
0x3F
```

but your code uses:

```cpp
LiquidCrystal_I2C lcd(0x27,16,2);
```

Update the address accordingly.

### Wrong LCD Size

A 16x2 display should use:

```cpp
LiquidCrystal_I2C lcd(0x27,16,2);
```

A 20x4 display should use:

```cpp
LiquidCrystal_I2C lcd(0x27,20,4);
```

---

# Step 2: Hello World Test

Use the address detected by the scanner.

---

## 16x2 LCD Example

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27,16,2);

void setup() {

  lcd.init();
  lcd.backlight();

  lcd.setCursor(0,0);
  lcd.print("Hello World");

  lcd.setCursor(0,1);
  lcd.print("16x2 LCD Test");
}

void loop() {
}
```

### Expected Output

```text
Hello World
16x2 LCD Test
```

---

## 20x4 LCD Example

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27,20,4);

void setup() {

  lcd.init();
  lcd.backlight();

  lcd.setCursor(0,0);
  lcd.print("Hello World");

  lcd.setCursor(0,1);
  lcd.print("20x4 LCD Test");

  lcd.setCursor(0,2);
  lcd.print("Bots Bangla");

  lcd.setCursor(0,3);
  lcd.print("Arduino + I2C");
}

void loop() {
}
```

### Expected Output

```text
Hello World
20x4 LCD Test
Bots Bangla
Arduino + I2C
```

---

# How to Identify Your LCD Size

### 16x2 LCD

Two visible rows:

```text
----------------
----------------
```

### 20x4 LCD

Four visible rows:

```text
--------------------
--------------------
--------------------
--------------------
```

---

# Quick Troubleshooting Checklist

| Symptom                           | Possible Cause                             |
| --------------------------------- | ------------------------------------------ |
| No backlight                      | No power                                   |
| Backlight ON but blank            | Contrast issue                             |
| Scanner finds nothing             | Wiring problem                             |
| Scanner finds address but no text | Wrong address or LCD configuration         |
| Random characters                 | Poor connection or incorrect configuration |
| Upload error                      | COM port, cable, or driver issue           |

---

# Final Verification

Before using the LCD in a project, confirm:

* [ ] LCD backlight is ON
* [ ] I2C scanner detects the device
* [ ] Correct I2C address is configured
* [ ] Correct display size is configured
* [ ] "Hello World" displays successfully

If all checks pass, the LCD is correctly configured and ready for Arduino projects.
