# First-Accelerometer-servo-code-For-Arm-thing
First Accelerometer/servo code For Arm thing
// http://chionophilous.wordpress.com/2011/06/20/getting-started-with-accelerometers-and-micro-controllers-arduino-adxl335/  
// these constants describe the pins. They won't change:
const int xpin = A1;                  // x-axis of the accelerometer
const int ypin = A2;                  // y-axis
const int zpin = A3;                  // z-axis (only on 3-axis models)
//
//servo stuff
#include <Servo.h> 
 
Servo myservo;  // create servo object to control a servo 
int servoState = 1;//0 indicates the joint is dampenin 1 indicates normal 
// twelve servo objects can be created on most boards
int pos = 50;

//
int sampleSize = 5;//number of samples taken for average
float threshold = 2;//value above which the damping is triggered
float currSize = 0;//used for sample counting. keeps track of how many samples have been taken per iteration
float currVal = 0;//keeps track of the total value from taken samples
float average = 0;//average acceleraton taken from samples

float sampleTotal = 0;//the total number collected from accelerometer do be divided
int sampleDelay = 50;   //number of milliseconds between readings
void setup()
{
 // initialize the serial communications:
 Serial.begin(9600);
 int pos = 50;//position of servo motor initially
 //Make sure the analog-to-digital converter takes its reference voltage from
 // the AREF pin
 pinMode(xpin, INPUT);
 pinMode(ypin, INPUT);
 pinMode(zpin, INPUT);
 myservo.attach(9);  // attaches the servo on pin 9 to the servo object 
}
void loop()
{
 int x = analogRead(xpin);
 //
 //add a small delay between pin readings.  I read that you should
 //do this but haven't tested the importance
   delay(1); 
 //
 int y = analogRead(ypin);
 //
 //add a small delay between pin readings.  I read that you should
 //do this but haven't tested the importance
   delay(1); 
 //
 int z = analogRead(zpin);
 //
 //zero_G is the reading we expect from the sensor when it detects
 //no acceleration.  Subtract this value from the sensor reading to
 //get a shifted sensor reading.
 float zero_G = 512.0; 
 //
 //scale is the number of units we expect the sensor reading to
 //change when the acceleration along an axis changes by 1G.
 //Divide the shifted sensor reading by scale to get acceleration in Gs.
 float scale = 102.3;
 //
 ///calculating average
 currVal = ((float)x - zero_G)/scale;
 if (currSize < sampleSize){
     sampleTotal = sampleTotal + (float)x + currVal;
     currSize = currSize+1;
 }
 else if (currSize >= sampleSize){
   average = sampleTotal/sampleSize;
   sampleTotal = 0;
   currSize = 0;
   
   ///deciding to dampen or not
   if (currVal<0){
    currVal = currVal*-1; 
   }
   if (currVal > threshold){
     pos = 0;
   }
 }
   else{
     //pos = 50;
 }
 myservo.write(pos);
 
 Serial.print(((float)x - zero_G)/scale);
 Serial.print("\t");
 //
 Serial.print(((float)y - zero_G)/scale);
Serial.print("\t");
 //
 Serial.print(((float)z - zero_G)/scale);
Serial.print("\n");

 // delay before next reading:
 delay(sampleDelay);
}
