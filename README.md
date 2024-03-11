#include <TimerOne.h>

const int sensorPin = A0;  // Analog pin connected to the LM35 temperature sensor
const int ledPin = 13;    // Digital pin connected to the onboard LED

const float thresholdTemperature = 30.0;
const long intervalLowTemp = 250;
const long intervalHighTemp = 500;

volatile bool ledState = LOW;

void setup() {
  pinMode(ledPin, OUTPUT);  // Set the LED pin as an output
  pinMode(sensorPin, INPUT); // Set the sensor pin as an input
  attachInterrupt(digitalPinToInterrupt(sensorPin), checkTemperature, CHANGE);

  Timer1.initialize(1000); // Set up Timer1 with a period of 1000 microseconds (1ms)
  Timer1.attachInterrupt(blinkLED); // Attach the blinkLED function to Timer1
}

void loop() {
  int sensorValue = analogRead(sensorPin);

  // Convert the analog value to temperature in degrees Celsius
  float temperature = (sensorValue * 0.48875);

  // Print the temperature to the Serial Monitor for debugging
  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" °C");

  // Check the temperature conditions and control the onboard LED accordingly
  if (temperature < 30.0) {
    blinkLED(250);  // Blink every 250 milliseconds
  } else {
    blinkLED(500);  // Blink every 500 milliseconds
  }
}

void checkTemperature() {
  int sensorValue = analogRead(sensorPin);
  float temperature = (sensorValue * 0.48875);

  if (temperature < thresholdTemperature) {
    Timer1.setPeriod(intervalLowTemp * 1000);  // Set Timer1 period for low-temperature interval
  } else {
    Timer1.setPeriod(intervalHighTemp * 1000);  // Set Timer1 period for high-temperature interval
  }
}

void blinkLED() {
  ledState = !ledState;
  digitalWrite(ledPin, ledState);
}

