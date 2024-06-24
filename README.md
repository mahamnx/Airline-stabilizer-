# Airline-stabilizer-with led balancing 
using ardiuno uno & amp MPU 6065 & servo motor; 
		#include <Adafruit_MPU6050.h>
#include <Adafruit_Sensor.h>
#include <Wire.h>
#include <Servo.h> // Include the Servo library
#include <LiquidCrystal_I2C.h> // Include the LCD library

int L1 = 13;
int L2 = 12;
int L3 = 11;
int L4 = 10;
int L5 = 9;
int L6 = 8;
int L7 = 7;
int L8 = 6;
int L9 = 5;
int L10 = 4;
int L11 = 3;
int L12 = 2;
int L13 = A0;
int L14 = A1;
int L15 = A2;
int servoPin = 9; // Define the pin where the servo is connected

Adafruit_MPU6050 mpu;
Servo myServo; // Create a servo object
LiquidCrystal_I2C lcd(0x27, 16, 2); // Set the LCD I2C address (usually 0x27) and size (16x2)

void setup(void) {
  Serial.begin(115200);

  // Try to initialize!
  if (!mpu.begin()) {
    Serial.println("Failed to find MPU6050 chip");
    while (1) {
      delay(10);
    }
  }
  Serial.println("MPU6050 Found!");

  // Set accelerometer range to +-8G
  mpu.setAccelerometerRange(MPU6050_RANGE_8_G);

  // Set gyro range to +- 500 deg/s
  mpu.setGyroRange(MPU6050_RANGE_500_DEG);

  // Set filter bandwidth to 21 Hz
  mpu.setFilterBandwidth(MPU6050_BAND_21_HZ);

  delay(100);

  // Set pinMode for LEDs
  pinMode(L1, OUTPUT);
  pinMode(L2, OUTPUT);
  pinMode(L3, OUTPUT);
  pinMode(L4, OUTPUT);
  pinMode(L5, OUTPUT);
  pinMode(L6, OUTPUT);
  pinMode(L7, OUTPUT);
  pinMode(L8, OUTPUT);
  pinMode(L9, OUTPUT);
  pinMode(L10, OUTPUT);
  pinMode(L11, OUTPUT);
  pinMode(L12, OUTPUT);
  pinMode(L13, OUTPUT);
  pinMode(L14, OUTPUT);
  pinMode(L15, OUTPUT);

  // Attach the servo to the pin
  myServo.attach(servoPin);

  // Initialize the LCD
  lcd.begin();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("MPU6050 Status:");
  lcd.setCursor(0, 1);
  lcd.print("Initializing...");
  delay(1000);
  lcd.setCursor(0, 1);
  lcd.print("Ready");
}

void loop() {
  /* Get new sensor events with the readings */
  sensors_event_t a, g, temp;
  mpu.getEvent(&a, &g, &temp);

  // Update LEDs based on acceleration.y
  updateLEDs(a.acceleration.y);

  // Control the servo based on acceleration.y
  controlServo(a.acceleration.y);

  // Update LCD display based on acceleration.y
  updateLCD(a.acceleration.y);
}

void updateLEDs(float accelY) {
  digitalWrite(L1, accelY <= -6.00 && accelY > -7.00 ? HIGH : LOW);
  digitalWrite(L2, accelY <= -5.00 && accelY > -6.00 ? HIGH : LOW);
  digitalWrite(L3, accelY <= -4.00 && accelY > -5.00 ? HIGH : LOW);
  digitalWrite(L4, accelY <= -3.00 && accelY > -4.00 ? HIGH : LOW);
  digitalWrite(L5, accelY <= -2.00 && accelY > -3.00 ? HIGH : LOW);
  digitalWrite(L6, accelY <= -1.00 && accelY > -2.00 ? HIGH : LOW);
  digitalWrite(L7, accelY < 0.00 && accelY > -1.00 ? HIGH : LOW);
  digitalWrite(L8, accelY == 0 ? HIGH : LOW);
  digitalWrite(L9, accelY <= 1.00 && accelY > 0.00 ? HIGH : LOW);
  digitalWrite(L10, accelY <= 2.00 && accelY > 1.00 ? HIGH : LOW);
  digitalWrite(L11, accelY <= 3.00 && accelY > 2.00 ? HIGH : LOW);
  digitalWrite(L12, accelY <= 4.00 && accelY > 3.00 ? HIGH : LOW);
  digitalWrite(L13, accelY <= 5.00 && accelY > 4.00 ? HIGH : LOW);
  digitalWrite(L14, accelY <= 6.00 && accelY > 5.00 ? HIGH : LOW);
  digitalWrite(L15, accelY <= 7.00 && accelY > 6.00 ? HIGH : LOW);
}

void controlServo(float accelY) {
  int servoAngle;
  if (accelY == 0) {
    servoAngle = 90; // Stable at the middle position
  } else if (accelY < 0) {
    servoAngle = 90 + (accelY * -15); // Tilt to the left (clockwise)
  } else {
    servoAngle = 90 - (accelY * 15); // Tilt to the right (anticlockwise)
  }

  // Constrain servo angle to valid range [0, 180]
  servoAngle = constrain(servoAngle, 0, 180);

  // Move the servo to the calculated angle
  myServo.write(servoAngle);
}

void updateLCD(float accelY) {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Position:");

  lcd.setCursor(0, 1);
  if (accelY == 0) {
    lcd.print("Center");
  } else if (accelY < 0) {
    lcd.print("Left Side");
  } else {
    lcd.print("Right Side");
  }
}
