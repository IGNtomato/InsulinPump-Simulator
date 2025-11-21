# InsulinPump-Simulator
The code I used for my Grade 8 science fair project where I created a model of an artifical pancreas/insulin pump.

  /*
 * Artificial Pancreas model code
 * 
 */

  int pumpPin = 11; // pin used to control pump
  
  int pumpSpeed = 255; // pump speed, value between 0-255
  
 int conductivity;    // conductivity value, this will be 0-1023 from analogRead
 
 int threshold = 450; // conductivity threshold at which pump should turn off
 
 int measure_error = 0; // The difference between threshold and and conductivity

 int total = 0; /* this is to average out the readings*/
 int average = 0; 

 int lastSpeedLevel = 0;   // Variable to remember the last speed level
int lastMeasureError = 0; // Variable to remember the last measure_error for buffering

// Define the gap between the speed levels (5-point gap = 10 points of buffer)
int gapLow = 5;

void setup() {
  // put your setup code here, to run once:
    pinMode(pumpPin,OUTPUT); // set pump pin as output
    Serial.begin(9600);      // initialize serial communication
}

void loop() {
  total = 0; // reset total variable

  for(int i = 0; i < 10; i++) {
    conductivity = analogRead(A1);  // get conductivity reading
    total += conductivity;
    delay(5);
  }
  average = total / 10;
  measure_error = average - threshold;

  if (measure_error <= 235 && measure_error >= 225) {  // Speed level 4 (255)
    if (lastSpeedLevel != 4) {
      pumpSpeed = 255;  // Speed level 4
      lastSpeedLevel = 4;  // Update lastSpeedLevel
    }
  } 
  else if (measure_error <= 215 && measure_error >= 160) {  // Speed level 3 (232)
    if (lastSpeedLevel != 3) {
      pumpSpeed = 232;  // Speed level 3
      lastSpeedLevel = 3;  // Update lastSpeedLevel
    }
  } 
  else if (measure_error <= 140 && measure_error >= 85) {  // Speed level 2 (200)
    if (lastSpeedLevel != 2) {
      pumpSpeed = 200;  // Speed level 2
      lastSpeedLevel = 2;  // Update lastSpeedLevel
    }
  } 
  else if (measure_error <= 65 && measure_error >= 11) {  // Speed level 1 (185)
    if (lastSpeedLevel != 1) {
      pumpSpeed = 185;  // Speed level 1
      lastSpeedLevel = 1;  // Update lastSpeedLevel
    }
  } 
  else if (measure_error < 11) {  // Speed level 0 (off)
    if (lastSpeedLevel != 0) {
      pumpSpeed = 0;  // Speed level 0 (pump off)
      lastSpeedLevel = 0;  // Update lastSpeedLevel
    }
  }
    lastMeasureError = measure_error;
  // Print average and error for debugging
  Serial.print("Average: "); // Print the conductivity reading
  Serial.print(average);
  Serial.print(" | Error: ");
  Serial.print(measure_error);
  Serial.print(" | Pump Speed: ");
  Serial.println(pumpSpeed);

  delay(200);

  if(average > threshold + 10){
    analogWrite(pumpPin,pumpSpeed); // turn pump on if threshold has not been reached yet
  }
  else if (average < threshold - 10) {
    digitalWrite(pumpPin,LOW); // turn pump off if threshold has been reached
  }

}
