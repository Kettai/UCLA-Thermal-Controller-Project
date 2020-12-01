# UCLA-Thermal-Controller-Project
#include <Wire.h>
#include <SPI.h>
#include <SD.h>
#include <max6675.h>
#include <LiquidCrystal_I2C.h>
LiquidCrystal_I2C lcd(0x27, 16, 2);

int thermoSO = 4;
int thermoCS = 5;
int thermoCLK = 6;

MAX6675 thermocouple(thermoCLK, thermoCS, thermoSO);
int vccPin = 3;
int gndPin = 2;
  
void setup() {
  Serial.begin(9600);
  // use Arduino pins 
 
  pinMode(vccPin, OUTPUT); 
  digitalWrite(vccPin, HIGH);
  pinMode(gndPin, OUTPUT); 
  digitalWrite(gndPin, LOW);
  pinMode(9, OUTPUT);// connected to S terminal of Relay
  pinMode(8, OUTPUT);// connected to S terminal of Relay
  pinMode(7, OUTPUT);// connected to S terminal of Relay
int pinCS = 10;
pinMode(pinCS, OUTPUT);
  File myFile; //for SDcard
      
  Serial.println(" MAX6675 is Ready");
  // wait for MAX chip to stabilize
  delay(1500);
   if (SD.begin())
  {
    Serial.println("SD card is ready to use.");
  } else
  {
    Serial.println("SD card initialization failed");
    return;
  }
myFile = SD.open("Data1.txt", FILE_WRITE);
myFile.println("Begin of Data Collection");
myFile.close();
}

void loop() {
  // basic readout test, just print the current temp
   Serial.print("C = "); 
   Serial.println(thermocouple.readCelsius());
 float y= millis();
 float  x = thermocouple.readCelsius(); 
        File myFile; //for SDcard
      myFile = SD.open("Data1.txt", FILE_WRITE);
  String C = "C = ";
  // if the file opened okay, write to it:
  if (myFile) {
    // Write to file
        String temp = String(thermocouple.readCelsius());
        
        myFile.println(C + temp);
 
    myFile.println(float(y));
    myFile.close();
    Serial.println("SD IS WORKING");
  }
  // if the file didn't open, print an error:
  else {
    Serial.println("error opening test.txt");
  }
   {
  lcd.init();//LCD screensetup
  lcd.backlight();//LCD screensetup
  lcd.setCursor(0,0);
  lcd.print("TEMPERATURE (C):");
  lcd.setCursor(7,1);  
  lcd.print(x,1);    
  delay(500);
   }
   if(x >= 300){
    digitalWrite(9, HIGH);
    digitalWrite(8, HIGH);
    digitalWrite(7, HIGH);
    }
    //if greater or equal to 245 but less than 250, activate only 7
    else if(x >= 275 && x < 300){
    digitalWrite(9, LOW);
    digitalWrite(8, HIGH);
    digitalWrite(7, HIGH);
    }
    //if temp is greater than 240C but lower than 245 than only activate the relay 9 & 8
    else if(x >= 250 && x < 275){
    digitalWrite(9, LOW);
    digitalWrite(8, LOW);
    digitalWrite(7, HIGH);
    }
    else{
    digitalWrite(9, LOW);
    digitalWrite(8, LOW);
    digitalWrite(7, LOW);
    }
 Serial.println(millis());
   delay(500);
   
}
