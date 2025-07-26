Task 1: Latching Power Switch Circuit with Auto On/Off
For this part of the assignment, I built a latching power‑switch circuit using an Arduino, a pushbutton, and an NPN transistor. The goal was to have one button press toggle a load (e.g. an LED or motor) on or off—just like a real power switch—without holding the button down.

1. Components & Setup
 - Arduino Uno
 - Pushbutton wired between digital pin 2 and +5 V
 - 10 kΩ pull‑down resistor from pin 2 to GND (to ensure a stable LOW when the button is not pressed)
 - NPN Transistor (e.g. 2N2222) with its collector to the load and emitter to GND
 - Base resistor (4.7 kΩ) between Arduino pin 9 and the transistor base
 - Load (in simulation, I used an LED + 220 Ω resistor to GND)
I laid everything out on a breadboard in Tinkercad, ensuring that:
 - The button input (pin 2) reads HIGH only when pressed.
 - The transistor’s base sees the Arduino’s output (pin 9) through the resistor.
 - When the transistor is driven, current flows through the load.

2. Wiring Logic
 - Button to Arduino
   - One side of the pushbutton → +5 V
   - Opposite side → digital pin 2
   - Pin 2 also → 10 kΩ resistor → GND
 - Transistor Switching
   - Arduino pin 9 → 4.7 kΩ → transistor base
   - Transistor emitter → GND
   - Transistor collector → load → +5 V
This configuration means:
 - Button press sets pin 2 HIGH.
Arduino code flips an internal flag (ledState).
If ledState is true, the code drives pin 9 HIGH → transistor saturates → load powered.
If ledState is false, pin 9 stays LOW → transistor off → load unpowered.

3. Code Walkthrough:

const int buttonPin     = 2;    // Pushbutton input
const int transistorPin = 9;    // Controls the transistor/base
bool ledState           = false; // Tracks ON/OFF state
bool lastButtonState    = LOW;   // Stores previous button reading
bool currentButtonState = LOW;   // Stores current reading
void setup() {
  pinMode(buttonPin, INPUT);
  pinMode(transistorPin, OUTPUT);
  digitalWrite(transistorPin, LOW);  // Ensure load is off initially
}
void loop() {
  currentButtonState = digitalRead(buttonPin);
  // Detect a rising edge: button goes from LOW to HIGH
  if (currentButtonState == HIGH && lastButtonState == LOW) {
    // Toggle the output state
    ledState = !ledState;
    digitalWrite(transistorPin, ledState ? HIGH : LOW);
    delay(200);  // Debounce delay to avoid multiple toggles
  }
  lastButtonState = currentButtonState;
}


 - Edge Detection: Only toggles when the button transitions from unpressed to pressed.
 - Debouncing: A 200 ms delay filters out quick, noisy flips.
 - State Retention: ledState remembers the current on/off until the next valid press.

4. Outcome:
With each distinct button press, the load switches on or off and stays in that state until the next press. This mimics a real latch/power‑switch behavior perfectly.






Task 2: Digital & Analog Sensor Interface
In this part, I demonstrated how an Arduino can read a digital input (pushbutton) and an analog input (potentiometer) and respond accordingly. I focused on the digital portion for the code below, then pointed out how the analog part would integrate.

1. Components & Setup
 - Arduino Uno
 - Pushbutton wired exactly as in Task 1 (pin 2 with pull‑down resistor)
 - Green LED + 220 Ω resistor on digital pin 8 → GND
 - 10 kΩ Potentiometer (analog sensor) with its three pins:
   - Left → +5 V
   - Right → GND
   - Middle (wiper) → analog pin A0

2. Wiring Logic
 - Digital Button
   - Same pull‑down configuration: button→pin 2, pin 2→10 kΩ→GND.
   - Reads HIGH when pressed, LOW otherwise.
 - LED Output
   - Digital pin 8 → LED → 220 Ω → GND.
   - Arduino sets pin 8 HIGH or LOW based on the button.
 - Potentiometer (Analog Input)
   - Wiper → analog pin A0
   - Provides a variable voltage between 0–5 V that maps to 0–1023 in code.

3. Code Walkthrough:

const int buttonPin = 2;  // Digital input
const int ledPin    = 8;  // Digital output (LED)
void setup() {
  pinMode(buttonPin, INPUT);
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);     // For optional debugging
}
void loop() {
  int buttonState = digitalRead(buttonPin);
  if (buttonState == HIGH) {
    digitalWrite(ledPin, HIGH);
    Serial.println("Button Pressed");
  } else {
    digitalWrite(ledPin, LOW);
    Serial.println("Button Released");
  }
  delay(100);  // Debounce and reduce spam on serial
}


 - Digital Read: Checks the button state each loop.
 - LED Control: Directly mirrors the button’s HIGH/LOW to the LED.
 - Serial Feedback: Prints “Button Pressed” or “Button Released” for debugging.


4. Extending to Analog
Although the core code above handles only the digital button, integrating the potentiometer is straightforward:

int potValue = analogRead(A0);        // Returns 0–1023
float voltage = potValue * (5.0/1023);
Serial.print("Potentiometer Voltage: ");
Serial.println(voltage);

You could then use potValue to adjust brightness via PWM on a PWM‑capable pin (e.g., pin 9) or to control motor speed, servo angle, or any other analog‑sensitive output.


5. Outcome
 - The green LED lights up only while the button is held down, demonstrating a simple digital input → digital output reaction.
 - The serial monitor confirms correct readings.
 - The setup is ready for analog integration—perfect for projects requiring both sensor types.


