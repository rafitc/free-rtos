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

## Free RTOS

### Scheduling multiple tasks

```c++
#include <Arduino_FreeRTOS.h>

void TaskOne(void *pvParameter); //two tasks
void TaskTwo(void *pvParameter);

void setup() {
  Serial.begin(115200);
  delay(100);
  while (!Serial) {
    ; // wait for serial port to connect.
  }
  //----setup tasks----//

  xTaskCreate(
    TaskOne, //task function
    "Task One", //just for reference
    128, //stack size
    NULL,
    2,//Priority, with 3 (configMAX_PRIORITIES - 1) being the highest, and 0 being the lowest.
    NULL );

  xTaskCreate(
    TaskTwo, //task function
    "Task Two", //just for reference
    128, //stack size
    NULL,
    1,//Priority, with 3 (configMAX_PRIORITIES - 1) being the highest, and 0 being the lowest.
    NULL );

}

void loop() {
  // Empty
}

void TaskOne(void *pvParameter) {
  (void) pvParameter;
  while (1) {
    Serial.println("Task One ");
    vTaskDelay( 1000 / portTICK_PERIOD_MS ); // wait for one second
  }
}

void TaskTwo(void *pvParameter) {
  (void) pvParameter;
  while (1) {
    Serial.println("Task Two ");
    vTaskDelay( 2000 / portTICK_PERIOD_MS ); // wait for one second
  }
}

```

---

### few more tasks.

```c++
#include <Arduino_FreeRTOS.h>
#define LED_ONE 12
#define LED_TWO 13

int ledTwoDelay = 500; //initially 500ms

void TaskOne(void *pvParameter); //two tasks
void TaskTwo(void *pvParameter);

void LedOneBlinkTask(void *pvParameter);
void LedTwoBlinkTask(void *pvParameter);

void SerialReadTask(void *pvParameter);

void setup() {
  Serial.begin(115200);
  delay(1000);
  Serial.println("Multi-task LED Demo");
  Serial.println("Enter a number in milliseconds to change the LED delay.");

  while (!Serial) {
    ; // wait for serial port to connect.
  }
  //----setup tasks----//

  xTaskCreate(
    TaskOne, //task function
    "Task One", //just for reference
    128, //stack size
    NULL,
    1,//Priority, with 3 (configMAX_PRIORITIES - 1) being the highest, and 0 being the lowest.
    NULL );

  xTaskCreate(
    TaskTwo, //task function
    "Task Two", //just for reference
    128, //stack size
    NULL,
    1,//Priority, with 3 (configMAX_PRIORITIES - 1) being the highest, and 0 being the lowest.
    NULL );

  xTaskCreate(
    LedOneBlinkTask, //task function
    "LED one Task", //just for reference
    128, //stack size
    NULL,
    1,//Priority, with 3 (configMAX_PRIORITIES - 1) being the highest, and 0 being the lowest.
    NULL );

  xTaskCreate(
    LedTwoBlinkTask, //task function
    "LED Two Task ", //just for reference
    128, //stack size
    NULL,
    1,//Priority, with 3 (configMAX_PRIORITIES - 1) being the highest, and 0 being the lowest.
    NULL );

  xTaskCreate(
    SerialReadTask, //task function
    "Serial Read Task ", //just for reference
    128, //stack size
    NULL,
    1,//Priority, with 3 (configMAX_PRIORITIES - 1) being the highest, and 0 being the lowest.
    NULL );


}

void loop() {
  // Empty
}

void TaskOne(void *pvParameter) {
  (void) pvParameter;
  while (1) {
    Serial.println("Task One ");
    vTaskDelay( 1000 / portTICK_PERIOD_MS ); // wait for one second
  }
}

void TaskTwo(void *pvParameter) {
  (void) pvParameter;
  while (1) {
    Serial.println("Task Two ");
    vTaskDelay( 2000 / portTICK_PERIOD_MS ); // wait for one second
  }
}

void LedOneBlinkTask(void *pvParameter) {
  (void) pvParameter;
  pinMode(LED_ONE, OUTPUT);
  while (1) {
    digitalWrite(LED_ONE, HIGH);
    vTaskDelay( 1000 / portTICK_PERIOD_MS ); // wait for one second
    digitalWrite(LED_ONE, LOW);
    vTaskDelay( 1000 / portTICK_PERIOD_MS ); // wait for one second
  }
}

void LedTwoBlinkTask(void *pvParameter) {
  (void) pvParameter;
  pinMode(LED_TWO, OUTPUT);
  while (1) {
    digitalWrite(LED_TWO, HIGH);
    vTaskDelay( ledTwoDelay / portTICK_PERIOD_MS ); // wait for one second
    digitalWrite(LED_TWO, LOW);
    vTaskDelay( ledTwoDelay / portTICK_PERIOD_MS ); // wait for one second
  }
}

void SerialReadTask(void *pvParameter) {
  (void) pvParameter;
  int a;
  while (1) {
    if (Serial.available() > 0) {
      a = Serial.parseInt();
      //i'm getting a zero value after reading serial data.
      // To omit Zero, simple if() is used
      if (a != 0) {
        ledTwoDelay = a;
        Serial.print("updated delay value : ");
        Serial.println(ledTwoDelay);
      }
    }
  }
}
```

---
