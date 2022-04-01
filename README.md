# introduction to RTOS

## Code examples

### Super Loop

```c++
void setup() {
  // put your setup code here, to run once:

}

void loop() {
  // put your main code here, to run repeatedly:

}
```

---

### Blink

```c++
// the setup function runs once
void setup() {
  // initialize digital pin LED_BUILTIN as an output.
  pinMode(LED_BUILTIN, OUTPUT);
}

// the loop function runs over and over again forever
void loop() {
  digitalWrite(LED_BUILTIN, HIGH);   // turn the LED on (HIGH is the voltage level)
  delay(1000);                       // wait for a second
  digitalWrite(LED_BUILTIN, LOW);    // turn the LED off by making the voltage LOW
  delay(1000);                       // wait for a second
}
```

---

### Let's add more led to this code

```c++
int ledPin1 = 13;              // LED 1 connected to digital pin 13
int ledPin2 = 14;              // LED 2 connected to digital pin 14

void setup() {
  pinMode(ledPin1, OUTPUT);    // sets the digital pin as output
  pinMode(ledPin2, OUTPUT);    // sets the digital pin as output
}

void loop() {
  digitalWrite(ledPin1, HIGH); // sets LED 1 on
  delay(1000);                 // waits for a second
  digitalWrite(ledPin1, LOW);  // sets LED 1 off
  delay(1000);                 // waits for a second

  digitalWrite(ledPin2, HIGH); // sets LED 2 on
  delay(2000);                 // waits for two seconds
  digitalWrite(ledPin2, LOW);  // sets LED 2 off
  delay(2000);                 // waits for two seconds
}
```

---

## millis();

### blink without delay

```c++
const int ledPin =  LED_BUILTIN;      // the number of the LED pin
int ledState = LOW;                   // ledState used to set the LED

unsigned long previousMillis = 0;     // will store last time LED was updated

const long interval = 1000;    // interval at which to blink (milliseconds)

void setup() {
  pinMode(ledPin, OUTPUT);
}

void loop() {

  unsigned long currentMillis = millis();

  if (currentMillis - previousMillis >= interval) {
    // save the last time you blinked the LED
    previousMillis = currentMillis;

    // if the LED is off turn it on and vice-versa:
    if (ledState == LOW) {
      ledState = HIGH;
    } else {
      ledState = LOW;
    }

    // set the LED with the ledState of the variable:
    digitalWrite(ledPin, ledState);
  }
}
```

---

### Few more functions

```c++
int ledPin1 = 13;              // LED 1 connected to digital pin 13
int ledPin2 = 14;              // LED 2 connected to digital pin 14

int ledState1 = LOW;           // LED 1 is initially set to off
int ledState2 = LOW;           // LED 2 is initially set to off

unsigned long millis1;         // initialises millis marker for LED 1
unsigned long millis2;         // initialises millis marker for LED 2

void setup() {
  pinMode(ledPin1, OUTPUT);    // sets the digital pin as output
  pinMode(ledPin2, OUTPUT);    // sets the digital pin as output
}

void loop() {

  // Update LED States
  digitalWrite(ledPin1, ledState1);
  digitalWrite(ledPin2, ledState2);

  // Toggle the LED states if the duration has passed

  if ( (millis() - millis1) > 1000) {
    millis1 = millis();
    if (ledState1 == LOW) {
      ledState1 = HIGH;
    } else if (ledState1 == HIGH) {
      ledState1 = LOW;
    }
  }

  if ( (millis() - millis2) > 2000) {
    millis2 = millis();
    if (ledState2 == LOW) {
      ledState2 = HIGH;
    } else if (ledState2 == HIGH) {
      ledState2 = LOW;
    }
  }
}
```

---

### Class{};

```c++
class blinkingLED {
  int ledPin;
  int ledState = LOW;
  long duration;
  unsigned long millisMarker;


  public:
  blinkingLED(int pin, long interval){ //constructor
    ledPin = pin;
    pinMode(ledPin, OUTPUT);

    duration = interval;
    millisMarker = 0;
  };


  void Update() { //method
    digitalWrite(ledPin, ledState);

    if ( (millis() - millisMarker) > duration) {
      millisMarker = millis();
      if (ledState == LOW) {
        ledState = HIGH;
      } else if (ledState == HIGH) {
        ledState = LOW;
      }
    }
  }
};

blinkingLED LED1(12, 1000); //object 1
blinkingLED LED2(13, 2000); //object 2

void setup() {
}

void loop() {
  LED1.Update();
  LED2.Update();
}
```

---
