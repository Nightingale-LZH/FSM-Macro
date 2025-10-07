# FSM-Macro

Arduino Macro Templates for Implementing Finite State Machines.

This is just a macro collection, so it could be used for other systems. This collection also contains other helper libraries like `scheduler`, `Timer`, etc.

## Folder Structure

- `FSM`
  - Simple finite state machine macro templates
  - See Documents [here](FSM/README.md)
- `Timer`
  - Simple timer/event macro templates
  - See Documents [here](Timer/README.md)
- `Scheduler`
  - Scheduler base class
- `DataStructure`
  - Collection of useful data structures
  - `AverageSampleing`
  - `ByteSQ`
  - `StackQueue`
  - `Vector3D`

## FSM Overview


This is the library that helps the development of Finite State Machine (FSM). It handles many details of FSM, state variable, and transition, so the user can focus on its content. 

### Examples

The default Arduino blinking example implemented using FSM Macro V2.

```C++
#include "FSMMacroV2.h"

#define PIN_LED_BUILTIN 13
CREATE_FSM(BLINK, 0)
void blink_update();

void setup() {  }

void loop() {
  blink_update();
}

void blink_update() {
  SETUP_FSM(BLINK);

  STATE(0) {
    pinMode(PIN_LED_BUILTIN, OUTPUT);
    TO_NEXT();
  }

  STATE(1) {
    digitalWrite(PIN_LED_BUILTIN, HIGH);
    SLEEP_TO_NEXT(1000);
  }

  STATE(2) {
    digitalWrite(PIN_LED_BUILTIN, LOW);
    SLEEP_TO(1000, 1);
  }
}
```

## Timer Overview

### Event Timeline

- The ``duration`` (unit: milliseconds) field sets a boundary between ``preDueEvent`` and ``postDueEvent``. The due time is refresh time + duration. The illustration of the timeline is below

```
  REFRESH                      DUE
-----+--------------------------+----------------------> time
     |<--------duration-------->|
              preDueEvent              postDueEvent
```

### Examples

- The following code demostrate basic time stamp functions
  - The built-in LED (pin 13) will lit up after one second without any button input (pin 12)
  - Pressing the button (set pin 12 to low/GND) reset the timeStamp.
  
``` C++
#include <Arduino.h>
#include "TimeStampMacro.h"

//  Create timeStamp named "ts1" with due event functionality. 
//  Set its duration for 1000 ms, or 1 second. 
TS_CREATE_W_EVENT(ts1, 1000)

void setup() {
  //  initialization
  Serial.begin(9600);
  pinMode(13, OUTPUT);
  pinMode(12, INPUT_PULLUP);
  TS_REFRESH(ts1);          //  Reset timeStamp right before program started
}

void loop() {
  TS_DUE_EVENT(ts1)         //  Invoking the event

  if (!digitalRead(12)) {   //  If button is pressed, i.e. pin 12 is low
    TS_REFRESH(ts1);        //  reset timeStamp
  }
  
  delay(200);               // "clock speed"
}

TS_DUE_EVENT_DEFINITION(ts1, 
  //  preDueEvent
  Serial.println("preDue"); 
  digitalWrite(13, LOW);
  , 
  //  postDueEvent
  Serial.println("postDue");
  digitalWrite(13, HIGH);
)

```