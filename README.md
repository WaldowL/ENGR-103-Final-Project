# ENGR-103-Final-Project
**Handheld Rockband video game**


#include <Adafruit_CircuitPlayground.h>

const int switchPin=7;
const int buttonPinA=4;
const int buttonPinB=5;
const int speakerPin=5;
volatile bool switchflag = false;
volatile bool switchState=false;
volatile bool buttonflagA = false;
volatile bool buttonflagB = false;
//variables copied from the studio with the midi function
float midi[127];
int A_four = 440; // a is 440 hz

float count=0;

int song[][2] = 
  {
  {69, 100}, 
  {69, 100}, 
  {65, 100}, 
  {62, 100},
  {62, 200}, 
  {67, 200}, 
  {67, 200}, 
  {67, 200},
  {71, 100}, 
  {71, 100}, 
  {72, 100}, 
  {62, 100},
  {72, 100}, 
  {72, 100}, 
  {72, 100}, 
  {69, 100},
  {65, 200},
  {69, 200}, 
  {69, 200}, 
  {69, 200},
  {69, 200}, 
  {67, 200}
  };

int happyBirthday[11][2] //happy birthday song. Midi values from chat GPT
{
  {60, 200},  // C4 quarter
  {62, 100},  // D4 eighth
  {64, 100},  // E4 eighth
  {65, 200},  // F4 quarter
  {67, 400},  // G4 half
  {69, 200},  // A4 quarter
  {67, 200},  // G4 quarter
  {65, 200},  // F4 quarter
  {64, 200},  // E4 quarter
  {62, 400},  // D4 half
  {60, 800}   // C4 whole
};


int test[3][2]
{
  {69, 200},
  {69, 200},
  {69, 200}
};

void setup() {
  CircuitPlayground.begin();
  Serial.begin(9600);
  CircuitPlayground.begin();
  generateMIDI(); // Calculating midi
  //creating the isr's for the switch and the button
  attachInterrupt(digitalPinToInterrupt(switchPin), ISRSwitch, CHANGE);
  attachInterrupt(digitalPinToInterrupt(buttonPinA), ISRButtonA, RISING); 
  attachInterrupt(digitalPinToInterrupt(buttonPinB), ISRButtonB, RISING); 
}


void ISRSwitch() {
    delay(10);
    switchflag = !switchflag;
    Serial.println("Switch changed");
  }

void ISRButtonA() {
    delay(10);
    buttonflagA = true;
    //Serial.println("button pressed");
  }

void ISRButtonB() {
    delay(10);
    buttonflagB = true;
    //Serial.println("button pressed");
  }


void loop() {

    // Playing the 2d array (copied from week 5 studio assignment)
  /*for(int i = 0; i < sizeof(song) / sizeof(song[0]); i++) // Calculate how many rows are in the array using: sizeof(song) / sizeof(song[0])
  {
    CircuitPlayground.playTone(midi[song[i][0]], song[i][1]);
    Serial.println(midi[song[i][0]]);
    delay(1);
  }*/
if (buttonflagA)
  {
  for(int i = 0; i < sizeof(test) / sizeof(test[0]); i++) // Calculate how many rows are in the array using: sizeof(song) / sizeof(song[0])
  {
    if(buttonflagB)
    {
      count=count+0.5;
    }
    /*tone(speakerPin,midi[test[i][0]]); // found the tone function on https://docs.arduino.cc/language-reference/en/functions/advanced-io/tone/
    delay(500);
    noTone(speakerPin);*/
    tone(speakerPin, midi[test[i][0]], test[i][1]); // Start playing 1000 Hz tone on pin 8
    //delay(500);    // Wait for 500 milliseconds
    //noTone(speakerPin);     // Stop the tone
    //CircuitPlayground.playTone(midi[test[i][0]], (test[i][1]));
    //Serial.println(midi[test[i][0]]);
    if(buttonflagB)
    {
      count=count+0.5;
      buttonflagB=false;
    }
  }
  buttonflagA=false;
  Serial.println("Your score is");
  Serial.println(count);
  count=0;
  }
}

//Midi generator given from the course. This function was copied and not modified with the exception of changing x to i. 
void generateMIDI()
{
  for (int i = 0; i < 127; ++i)
  {
    midi[i] = (A_four / 32.0) * pow(2.0, ((i - 9.0) / 12.0));
    Serial.println(midi[i]);
  }
}

//tone gnereating function from https://learn.adafruit.com/circuit-playground-music/using-the-circuit-playground-speaker
/*
void makeTone (int frequencyInHertz, long timeInMilliseconds) 
{
  int x;   
  long delayAmount = (long)(1000000/frequencyInHertz);
  long loopTime = (long)((timeInMilliseconds*1000)/(delayAmount*2));
  for (x=0; x<loopTime; x++) 
  {        // the wave will be symetrical (same time high & low)
     digitalWrite(speakerPin,HIGH);   // Set the pin high
     delayMicroseconds(delayAmount);  // and make the tall part of the wave
     digitalWrite(speakerPin,LOW);    // switch the pin back to low
     delayMicroseconds(delayAmount);  // and make the bottom part of the wave
  }
}*/
