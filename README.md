# -Line-Follower-Robot-with-PID-Control
This project is an advanced Line Follower Robot built using Arduino, an 8-channel IR sensor array, and PID control. The robot accurately follows a black line on a white surface using real-time error correction for smooth and high-speed movement.

## 🎯 Objective

To design and implement a high-performance autonomous robot capable of precise path tracking using PID control and multiple sensor inputs.

## 🔧 Components Used

* Arduino UNO
* 8-Channel IR Sensor Module
* Motor Driver (TB6612FNG)
* BO Motors (12V, 500 RPM)
* Wheels & Chassis
* 3 × 18650 Li-ion Batteries
* Buck Converter (Step-down to 5V)
* Switch
* Connecting Wires

## 🔋 Power Supply Design

* 3 × 18650 batteries provide ~11.1V–12.6V
* Battery output is split into:

  * Motor Driver (VM) → Direct 12V (for motors)
  * Buck Converter → 5V → Arduino + Sensors

### ⚠️ Important Notes:

* Arduino is NOT powered directly from battery
* Common ground is maintained across all components
* A switch is used to control main power

## 🔌 Circuit Connections

### Motor Driver (TB6612FNG) to Arduino:

* PWMA → D5
* AIN1 → D7
* AIN2 → D8
* PWMB → D6
* BIN1 → D9
* BIN2 → D10
* STBY → D4

### Power Connections:

* VM → Battery + (via switch)
* VCC → 5V (from buck converter)
* GND → Common ground

### 8-Channel IR Sensor to Arduino:

* D0 → A0

* D1 → A1

* D2 → A2

* D3 → A3

* D4 → A4

* D5 → A5

* D6 → D2

* D7 → D3

* VCC → 5V

* GND → GND

## ⚙️ Working Principle

The robot uses an 8-channel IR sensor array to detect the position of the line. Each sensor is assigned a weight value:

* Left side → Negative values
* Right side → Positive values

The error is calculated based on the weighted average of active sensors.

The PID controller continuously adjusts motor speeds based on:

* Proportional (current error)
* Integral (past error)
* Derivative (rate of change of error)

This ensures smooth and accurate line following.

---

## 🧠 PID Control Formula

u(t) = Kp·e(t) + Ki·∫e(t)dt + Kd·de(t)/dt

---

## 💻 Arduino Code
// Motor Pins
#define PWMA 5
#define AIN1 7
#define AIN2 8

#define PWMB 6
#define BIN1 9
#define BIN2 10

#define STBY 4

// Sensor Pins
int sensorPins[8] = {A0, A1, A2, A3, A4, A5, 2, 3};

// Weights
int weights[8] = {-3, -2, -1, 0, 0, 1, 2, 3};

// PID constants
float Kp = 20;
float Ki = 0;
float Kd = 10;

int baseSpeed = 150;

int previousError = 0;
int integral = 0;

void setup() {
  for(int i=0;i<8;i++) {
    pinMode(sensorPins[i], INPUT);
  }

  pinMode(PWMA, OUTPUT);
  pinMode(AIN1, OUTPUT);
  pinMode(AIN2, OUTPUT);

  pinMode(PWMB, OUTPUT);
  pinMode(BIN1, OUTPUT);
  pinMode(BIN2, OUTPUT);

  pinMode(STBY, OUTPUT);
  digitalWrite(STBY, HIGH);
}

void loop() {
  int error = calculateError();

  integral += error;
  int derivative = error - previousError;

  int correction = (Kp * error) + (Ki * integral) + (Kd * derivative);

  int leftSpeed = baseSpeed + correction;
  int rightSpeed = baseSpeed - correction;

  leftSpeed = constrain(leftSpeed, 0, 255);
  rightSpeed = constrain(rightSpeed, 0, 255);

  moveMotors(leftSpeed, rightSpeed);

  previousError = error;
}

// Calculate error using 8 sensors
int calculateError() {
  int sum = 0;
  int activeSensors = 0;

  for(int i=0;i<8;i++) {
    int val = digitalRead(sensorPins[i]);

    if(val == 1) { // black line detected
      sum += weights[i];
      activeSensors++;
    }
  }

  if(activeSensors == 0) return previousError; // line lost

  return sum / activeSensors;
}

void moveMotors(int leftSpeed, int rightSpeed) {
  // Left Motor
  digitalWrite(AIN1, HIGH);
  digitalWrite(AIN2, LOW);
  analogWrite(PWMA, leftSpeed);

  // Right Motor
  digitalWrite(BIN1, HIGH);
  digitalWrite(BIN2, LOW);
  analogWrite(PWMB, rightSpeed);
}

## 📷 Project Images

<img width="899" height="1599" alt="image" src="https://github.com/user-attachments/assets/fbdd4b44-3a0f-4f5e-a094-a277be15aa4f" />
<img width="899" height="1599" alt="image" src="https://github.com/user-attachments/assets/a09d84fb-c1d3-42b1-b2d2-253865708053" />
<img width="899" height="1599" alt="image" src="https://github.com/user-attachments/assets/e97f4a07-c4e7-44d9-a163-ba59466399a3" />


## ⚠️ Challenges Faced

* Sensor calibration and alignment
* Motor speed mismatch
* Power instability during testing
* PID tuning for smooth performance

---

## 🚀 Future Improvements

* Add wireless control (Bluetooth/WiFi)
* Implement obstacle detection
* Use encoder feedback for better accuracy
* Improve chassis design for stability

---

## 🙋‍♂️ Author

**Manohar**
ECE Student | Embedded Systems Enthusiast

---

## 🔗 Connect with Me
www.linkedin.com/in/manohar-vutukuru-153798336

## ⭐ Project Highlights

* Advanced PID-based control system
* 8-sensor precision tracking
* Proper power management design
* Real-world embedded system implementation
