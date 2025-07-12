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
byte angl1;
int btn1Value;
int btn2Value;
int btn3Value;
int btn4Value;
unsigned long lastTimePressed;

bool checkDone = true;

byte clickedButtons[4];
byte rightButtons[4] = {2,4,1,3};


void correctSignal() {
  digitalWrite(blueLED, HIGH);
  tone(buzzer, 700, 1000);
  digitalWrite(blueLED, LOW);
}


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
  lcd.clear();
  lcd.print("Safe Mode");

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

    lcd.clear();
    lcd.print("Time Left");
    lcd.print("    SEC");
    
    
    while ((millis() - lastTime) / 1000 < 10.050){
      lcd.setCursor(9, 0);
      lcd.print(' ');
      lcd.print(timeOut);

      timeOut = (millis() - lastTime) / 1000;

      btn1Value = digitalRead(btn1);
      btn2Value = digitalRead(btn2);
      btn3Value = digitalRead(btn3);
      btn4Value = digitalRead(btn4);

      if (count < 4) {
        if (millis() - lastTimePressed > 200){  // check no repeat
          if (btn1Value == LOW) {
            clickedButtons[count] = 1;
            tone(buzzer, 600, 500);
            lastTimePressed = millis();
            count++;
          }
          
          if (btn2Value == LOW) {
            clickedButtons[count] = 2;
            tone(buzzer, 600, 500);
            lastTimePressed = millis();
            count++;
          }
          
          if (btn3Value == LOW) {
            clickedButtons[count] = 3;
            tone(buzzer, 600, 500);
            lastTimePressed = millis();
            count++;
          }

          if (btn4Value == LOW) {
            clickedButtons[count] = 4;
            tone(buzzer, 600, 500);
            lastTimePressed = millis();
            count++;
          }

        }

      } 

      else{ // Start check
        lcd.setCursor(0, 0);

        for (byte z=0; z<4; z++) {
          if (clickedButtons[z] != rightButtons[z]){
            checkDone = false;
            count = 0;
            break;
          }
        }

        if (checkDone) {
          lcd.clear();
          lcd.print("Correct!");
          digitalWrite(redLED, LOW);

          for (byte time=0; time<2; time++) {
            digitalWrite(blueLED, HIGH);
            tone(buzzer, 700);
            delay(300);
            digitalWrite(blueLED, LOW);
            noTone(buzzer);
            delay(300);
          }

          break; // if pass correct get out of loop
        }

      }

    } // End while loop

  if (timeOut > 10) {
    lcd.clear();
    lcd.print("System closed");
    while (true); delay(1000);
  }

  }

  count = 0; 

  delay(200);
}



```
