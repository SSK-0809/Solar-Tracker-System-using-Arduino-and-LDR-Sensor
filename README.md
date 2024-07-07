# Solar-Tracker-System-using-Arduino-and-LDR-Sensor
## Project Description

### Overview
This project involves creating a sun-tracking system using servo motors and Light Dependent Resistors (LDRs). The system adjusts the position of the solar panel to face the sun directly, maximizing the efficiency of solar energy capture. The servos receive control signals from a microcontroller, which calculates the position of the sun based on the LDR readings and adjusts the panel's orientation accordingly.

### Components
- **Servo Motors**: Two servo motors (horizontal and vertical) to adjust the panel's position.
- **Light Dependent Resistors (LDRs)**: Four LDRs to detect the intensity of sunlight from different directions.
- **Microcontroller**: An Arduino to process the LDR readings and control the servo motors.

### How It Works
1. **Initialization**: The servos are initialized and set to their starting positions.
2. **LDR Readings**: The system reads the values from the four LDRs.
3. **Calculations**:
    - **Vertical Adjustment**: The average values from the top LDRs are compared with the bottom LDRs.
    - **Horizontal Adjustment**: The average values from the left LDRs are compared with the right LDRs.
4. **Adjust Servos**: Based on the differences, the servos are adjusted to minimize the light difference, ensuring the panel faces the sun directly.

### Code Explanation

```cpp
#include <Servo.h>

Servo horizontal;
Servo vertical;

int servoh = 180;
int servohLimitHigh = 175;
int servohLimitLow = 5;

int servov = 45;
int servovLimitHigh = 60;
int servovLimitLow = 1;

int ldrlt = A0; // Top left LDR
int ldrrt = A3; // Top right LDR
int ldrld = A1; // Bottom left LDR
int ldrrd = A2; // Bottom right LDR

void setup() {
  horizontal.attach(9); // Horizontal servo connected to pin 9
  vertical.attach(10);  // Vertical servo connected to pin 10
  horizontal.write(180); // Set horizontal servo to initial position
  vertical.write(45);    // Set vertical servo to initial position
  delay(2500);           // Wait for 2.5 seconds for servos to stabilize
}

void loop() {
  int lt = analogRead(ldrlt); // Read top left LDR
  int rt = analogRead(ldrrt); // Read top right LDR
  int ld = analogRead(ldrld); // Read bottom left LDR
  int rd = analogRead(ldrrd); // Read bottom right LDR

  int dtime = 10; // Delay time
  int tol = 90;   // Tolerance

  int avt = (lt + rt) / 2; // Average top LDRs
  int avd = (ld + rd) / 2; // Average bottom LDRs
  int avl = (lt + ld) / 2; // Average left LDRs
  int avr = (rt + rd) / 2; // Average right LDRs

  int dvert = avt - avd; // Difference in vertical LDRs
  int dhoriz = avl - avr; // Difference in horizontal LDRs

  if (abs(dvert) > tol) {
    if (avt > avd) {
      servov++;
      if (servov > servovLimitHigh) servov = servovLimitHigh;
    } else {
      servov--;
      if (servov < servovLimitLow) servov = servovLimitLow;
    }
    vertical.write(servov);
  }

  if (abs(dhoriz) > tol) {
    if (avl > avr) {
      servoh--;
      if (servoh < servohLimitLow) servoh = servohLimitLow;
    } else {
      servoh++;
      if (servoh > servohLimitHigh) servoh = servohLimitHigh;
    }
    horizontal.write(servoh);
  }

  delay(dtime); // Small delay before the next loop iteration
}
```

### Code Breakdown
- **Initialization**:
  - `Servo horizontal` and `Servo vertical` objects are created.
  - `servoh` and `servov` store the initial positions of the horizontal and vertical servos.
  - `servohLimitHigh`, `servohLimitLow`, `servovLimitHigh`, and `servovLimitLow` define the movement limits for the servos.
  - `ldrlt`, `ldrrt`, `ldrld`, and `ldrrd` are the analog pins connected to the LDRs.

- **Setup**:
  - The servos are attached to their respective pins.
  - The servos are moved to their initial positions and allowed to stabilize.

- **Loop**:
  - LDR readings are taken and stored in `lt`, `rt`, `ld`, and `rd`.
  - Average values for top (`avt`), bottom (`avd`), left (`avl`), and right (`avr`) LDRs are calculated.
  - The differences `dvert` (vertical) and `dhoriz` (horizontal) are computed.
  - If the difference exceeds the tolerance, the servos are adjusted accordingly.
  - The system waits for a short period before repeating the process.

