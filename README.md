# Finalyear-project

#define USE_ARDUINO_INTERRUPTS true   
#include <PulseSensorPlayground.h>  
#include <LiquidCrystal.h>
int led = 13;
int digitalPin = A5;
int digitalVal;

const int PulseWire = A2;      
const int LED13 = 13;          
int Threshold = 550;           

PulseSensorPlayground pulseSensor;  

LiquidCrystal lcd(7, 12, A0, A1, 9, 8, 2, A3, 5, 4, 3);  /* For 8-bit mode */

void setup()
{
pinMode(led, OUTPUT);
pinMode(digitalPin, INPUT);
pinMode(10, INPUT); 
pinMode(11, INPUT); 

Serial.begin(9600);        
lcd.begin(16, 2);
lcd.setCursor(0,0);
lcd.print("Health Care");

pulseSensor.analogInput(PulseWire);
pulseSensor.blinkOnPulse(LED13);       
pulseSensor.setThreshold(Threshold);


if (pulseSensor.begin()) {
Serial.println("We created a pulseSensor Object !");  //This prints one time at Arduino power-up,  or on Arduino reset.
lcd.clear();
lcd.print("BPM:"); // BEATS PER MINUTE
}
}

void loop()
{
while(1)
{
ECG();
delay(100);
temperature();
delay(100);
heart_rate();
delay(100);
}
}
void ECG()
{
if((digitalRead(10) == 1)||(digitalRead(11) == 1)){
Serial.println('!');
//lcd.print('-');
}
else{
unsigned int AnalogValue;
AnalogValue = analogRead(A4);
lcd.setCursor(0,1);
lcd.print(AnalogValue);
}
//Wait for a bit to keep serial data from saturating
delay(1);
}
void temperature()
{
// Read the digital interface
digitalVal = digitalRead(digitalPin);
if(digitalVal == HIGH) // if temperature threshold reached
{
digitalWrite(led, LOW); // turn ON Arduino's LED
}
else
{
digitalWrite(led, HIGH); // turn OFF Arduino's LED
}
}
void heart_rate()
{
int myBPM = pulseSensor.getBeatsPerMinute();
// "myBPM" hold this BPM value now.

if (pulseSensor.sawStartOfBeat()) {           
Serial.println("   A HeartBeat Happened ! ");
Serial.print("BPM: ");                       
Serial.println(myBPM);                      
lcd.clear();
lcd.setCursor(0,0);
lcd.print("BPM:");
lcd.setCursor(4,0);
lcd.print(myBPM);
}

delay(20);                  

}
