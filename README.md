//# IR-Remote-controlled-code
#include <Arduino.h>
#include <IRremoteESP8266.h>
#include <IRrecv.h>
#include <IRutils.h>

// An IR detector/demodulator is connected to GPIO pin 2 (D4 on a NodeMCU
// board).
// Note: GPIO 16 won't work on the ESP8266 as it does not have interrupts.
const uint16_t kRecvPin = 2;
unsigned long key_value = 0;
// Control LEDs with the 
const int greenPin = 0; //connected to GPIO pin 0 (D3 on a NodeMCU board).
const int yellowPin = 4; //connected to GPIO pin 2 (D2 on a NodeMCU board).

IRrecv irrecv(kRecvPin);

decode_results results;

void setup() {
  Serial.begin(9600);
  irrecv.enableIRIn();  // Start the receiver
  while (!Serial)  // Wait for the serial connection to be establised.
    delay(50);
  Serial.println();
  Serial.print("IRrecvDemo is now running and waiting for IR message on Pin ");
  Serial.println(kRecvPin);
  pinMode(greenPin, OUTPUT);
  pinMode(yellowPin, OUTPUT);
}

void loop() {
  if (irrecv.decode(&results)) {
    // print() & println() can't handle printing long longs. (uint64_t)
    serialPrintUint64(results.value, HEX);
    Serial.println("");

    switch(results.value){
          case 0x1FE50AF:
          Serial.println("1");
          // green LED on for 2 seconds
          digitalWrite(greenPin, HIGH);
          break ;
          case 0x1FEF807:
          Serial.println("3");
          digitalWrite(greenPin, LOW);
          break ;
          case 0x1FED827:
          Serial.println("2");
          digitalWrite(yellowPin, HIGH);
          break ;  
          case 0x1FE30CF:
          Serial.println("4");
          digitalWrite(yellowPin, LOW);
          break ;     
        }
        key_value = results.value;
        
    irrecv.resume();  // Receive the next value
  }
  delay(100);
}
