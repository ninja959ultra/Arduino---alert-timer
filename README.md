# Arduino---alert-timer


```cpp
#include <LiquidCrystal_I2C.h>
#include <Servo.h>

Servo servo;
LiquidCrystal_I2C lcd(0x27, 16, 2);


byte buzzer = 3;
byte blueLED = 4;
byte redLED = 5;
byte echo = 6;
byte trig = 7;
byte btn1 = 9;
byte btn2 = 10;
byte btn3 = 11;
byte btn4 = 12;
byte count = 0;


int lastTime = 0;
unsigned long travelTime;
int distance;
int timeOut;
byte angle;
int btn1Value;
int btn2Value;
int btn3Value;
int btn4Value;
unsigned long lastTimePressed;

bool checkDone = true;

byte clickedButtons[4];
byte rightButtons[4] = {2,4,1,3};

void setup() {
  Serial.begin(9600);
  servo.attach(2);
  lcd.init();
  lcd.backlight();
  pinMode(buzzer, OUTPUT);
  pinMode(blueLED, OUTPUT);
  pinMode(redLED, OUTPUT);
  pinMode(trig, OUTPUT);
  pinMode(echo, INPUT);

  for (byte i=9; i<13; i++){ // setup buttons
    pinMode(i, INPUT_PULLUP);
  }
  
  servo.write(0);
  delay(1000);
}

void loop() {
  digitalWrite(trig, LOW);
  delayMicroseconds(10);
  digitalWrite(trig, HIGH);
  delayMicroseconds(5);
  digitalWrite(trig, LOW);

  travelTime = pulseIn(echo, HIGH);

  distance = (travelTime / 2) * 0.0343;

  if (distance < 10) {
    digitalWrite(redLED, HIGH);
    tone(buzzer, 700, 1000);

    lastTime = millis();

    while ((millis() - lastTime) < 10000){
      timeOut = (millis() - lastTime);

      btn1Value = digitalRead(btn1);
      btn2Value = digitalRead(btn2);
      btn3Value = digitalRead(btn3);
      btn4Value = digitalRead(btn4);

      if (count < 4) {
        if (btn1Value == LOW) {
          clickedButtons[count] = 1;
          count++;
        }
        
        if (btn2Value == LOW) {
          clickedButtons[count] = 2;
          count++;
        }
        
        if (btn3Value == LOW) {
          clickedButtons[count] = 3;
          count++;
        }

        if (btn4Value == LOW) {
          clickedButtons[count] = 4;
          count++;
        }
      }

      else{ // Start check
        for (byte z=0; z<4; z++) {
          if (clickedButtons[z] != rightButtons[z]){
            checkDone = false;
            break;
          }
        }

      if (checkDone){
        Serial.println("correct");
        count = 0;
        break;
      }
      
      else{
        count = 0;
      }

    }

      Serial.println(count);
      delay(50);
    } // End while loop

  if (!checkDone){
    while (true){
      Serial.println("System close");
    }
  }

  } // End alert
}



```
