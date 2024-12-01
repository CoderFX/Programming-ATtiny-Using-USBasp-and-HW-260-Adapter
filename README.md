# Programming ATtiny Using USBasp and HW-260 Adapter

This guide explains how to program an ATtiny microcontroller using a **USBasp** programmer (flashed with updated firmware), the **HW-260 ATTiny adapter development board**, and tools like **Arduino IDE**, **PlatformIO**, **avrdude**, and **SinaProg**.

---

## Hardware and Software Requirements

### Hardware
1. **USBasp Programmer**:
   - Updated with **USBASP11.hex** firmware.
2. **HW-260 ATTiny Adapter Development Board**:
   - Used for interfacing the USBasp with the ATtiny.
3. **ATtiny Microcontroller** (e.g., ATtiny45).
4. **Arduino Mega**:
   - Used for updating USBasp firmware.

### Software
1. **Zadig**:
   - Installs the correct USB driver for USBasp.
2. **avrdude**:
   - Command-line tool for flashing AVR microcontrollers.
3. **Arduino IDE** or **PlatformIO**:
   - For writing and compiling sketches.
4. **SinaProg 2.1.1**:
   - GUI for flashing USBasp firmware.

---

## Flashing USBasp Firmware

### Steps to Flash USBASP11.hex

1. **Wire the Arduino Mega to USBasp (ICSP Header)**:

   | Arduino Mega Pin | USBasp ICSP Pin |
   |------------------|-----------------|
   | 5V               | VCC             |
   | GND              | GND             |
   | D11              | MOSI            |
   | D12              | MISO            |
   | D13              | SCK             |
   | D10              | RESET           |

2. **Load Arduino ISP Sketch on Mega**:
   - In Arduino IDE, go to **File > Examples > 11.ArduinoISP > ArduinoISP**.
   - Upload this sketch to the Arduino Mega.

3. **Flash USBasp Using SinaProg**:
   - Open **SinaProg 2.1.1**.
   - Select **USBASP11.hex** firmware.
   - Set Programmer: **Arduino as ISP**.
   - Set Target: **USBasp**.
   - Click **Write Flash**.

---

## Installing USBasp Drivers Using Zadig

1. **Download and Open Zadig**:
   - Install from [Zadig website](https://zadig.akeo.ie/).

2. **Install Driver**:
   - Go to **Options > List All Devices**.
   - Select **USBasp** from the device list (ensure USBasp is connected to your PC via USB).
   - Choose **libusbK (v3.x.x)** driver.
   - Click **Replace Driver**.

---

## Preparing the ATtiny Sketch

### Example Blink Sketch for ATtiny45
Save this example sketch as `blink_test.ino`:

```cpp
// the setup function runs once when you press reset or power the board
void setup() {
  // initialize digital pin 4 (PB4) as an output.
  pinMode(4, OUTPUT);
}

// the loop function runs over and over again forever
void loop() {
  digitalWrite(4, HIGH);  // turn the LED on
  delay(250);             // wait for 250ms
  digitalWrite(4, LOW);   // turn the LED off
  delay(250);             // wait for 250ms
}
```

---

## Preparing the ATtiny Sketch

### Using Arduino IDE

1. Select **ATtiny45** as the board in **Tools > Board**.
2. Set the clock frequency to **1 MHz (Internal Oscillator)** in **Tools > Clock**.
3. Write or open your sketch (e.g., a Blink program).
4. Compile the sketch by going to **Sketch > Export Compiled Binary**.
5. Locate the compiled `.hex` file in the **sketch folder**.

### Example Blink Sketch for ATtiny45

Save this example sketch as `blink_test.ino`:

```cpp
// the setup function runs once when you press reset or power the board
void setup() {
  // initialize digital pin 4 (PB4) as an output.
  pinMode(4, OUTPUT);
}

// the loop function runs over and over again forever
void loop() {
  digitalWrite(4, HIGH);  // turn the LED on
  delay(250);             // wait for 250ms
  digitalWrite(4, LOW);   // turn the LED off
  delay(250);             // wait for 250ms
}
```

## Using PlatformIO

### Create the Configuration File

Create a `platformio.ini` file in your project directory with the following configuration:

```ini
[env:attiny45]
platform = atmelavr
board = attiny45
framework = arduino
upload_protocol = usbasp
```

---

## Write and Compile the Sketch

Write your sketch in `src/main.cpp`. For example, the Blink program:

```cpp
// The setup function runs once when you press reset or power the board
void setup() {
  // Initialize digital pin 4 (PB4) as an output
  pinMode(4, OUTPUT);
}

// The loop function runs over and over again forever
void loop() {
  digitalWrite(4, HIGH);  // Turn the LED on
  delay(250);             // Wait for 250ms
  digitalWrite(4, LOW);   // Turn the LED off
  delay(250);             // Wait for 250ms
}
```

### Compile the Sketch

To compile your sketch using PlatformIO, run:

```bash
pio run
```

Locate the compiled `.hex` file in the `/build` directory of your project.

---

## Flashing the ATtiny

### Wiring USBasp to HW-260

1. Place the ATtiny45 into the HW-260 adapter.
2. Connect USBasp to the HW-260 using the following wiring:

   | USBasp Pin | HW-260 Pin |
   |------------|------------|
   | VCC        | VCC        |
   | GND        | GND        |
   | MOSI       | MOSI       |
   | MISO       | MISO       |
   | SCK        | SCK        |
   | RESET      | RESET      |

### Flash the ATtiny

Run the following command to flash the `.hex` file onto the ATtiny:

```bash
avrdude -c usbasp -p t45 -U flash:w:blink_test.hex:i
```

### If Flashing Fails

If the flashing process fails, short the **JP3 jumper** on the USBasp to enable slow SCK mode. Retry with the following command:

```bash
avrdude -c usbasp -p t45 -B 32 -U flash:w:blink_test.hex:i
```

---

## Flashing USBasp Firmware (Optional)

### Steps to Flash USBASP11.hex

1. **Wire the Arduino Mega to USBasp (ICSP Header):**

   | Arduino Mega Pin | USBasp ICSP Pin |
   |------------------|-----------------|
   | 5V               | VCC             |
   | GND              | GND             |
   | D11              | MOSI            |
   | D12              | MISO            |
   | D13              | SCK             |
   | D10              | RESET           |

2. **Load Arduino ISP Sketch on Mega**:
   - Open Arduino IDE.
   - Go to **File > Examples > 11.ArduinoISP > ArduinoISP**.
   - Upload the sketch to the Arduino Mega.

3. **Flash USBasp Using SinaProg**:
   - Short USBasp JP2 jumper (enter USBasp programmingmode).
   - Open **SinaProg 2.1.1**.
   - Select **USBASP11.hex** firmware.
   - Set Programmer: **Arduino as ISP**.
   - Set Target: **USBasp**.
   - Click **Write Flash**.
   - Unshort USBasp JP2 jumper.

---

## Installing USBasp Drivers Using Zadig

1. **Download and Open Zadig**:
   - Download Zadig from [its website](https://zadig.akeo.ie/).

2. **Install Driver**:
   - Go to **Options > List All Devices**.
   - Select **USBasp** from the device list (ensure USBasp is connected to your PC via USB).
   - Choose **libusbK (v3.x.x)** driver.
   - Click **Replace Driver**.

---

## Quick Tips

- After programming, disconnect the USBasp to run the sketch on the ATtiny.
- Use the `-B` flag to adjust SCK speed for slower target devices.
- Double-check all wiring connections and ensure the ATtiny is properly powered.
- Update USBasp firmware if you encounter compatibility issues.

---

This guide ensures you can easily program ATtiny microcontrollers with the USBasp and HW-260 adapter. Good luck!

