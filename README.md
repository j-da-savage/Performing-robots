# Performing Robots Journal

# Performing Robots

###  *Robot sketch*
meet FIFI !

![Fifi](https://github.com/j-da-savage/Performing-robots/blob/main/sketch1.jpg)
![Fifi design](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/designFifi.jpeg)
![Fifi Structure](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/fifiStructure.jpg)
###  *Building the robot base*

What we used:
- Wood
- 2 Motors
- Arduino Uno
- H-bridge
- Battery
- 2 Wheels
- 2 Caster Wheels

![Robot Base](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/base1.jpg)
![Building robot base](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/creatingBase.JPG)

###  *Connecting the H-bridge and the Arduino*

![H-bridge/Arduino connections](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/Hbridge.jpg)

#### Issues: 
Upon testing the H-bridge with the Arduino, we were faced with the issue of the H-bridge overheating and not working, so we had to replace it. Then, we were able to test the servo motor we would be using with the H-bridge connected to it.

![H-bridge and Servo Motor](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/HbridgeServo.png)

###  *09/20 - dance_code_test1*
In order to test out the movements for the robot, using the base we had created, we wrote the following code to do a little robotic-dance:

        void setup() {
          // Pins 2 and 3 are connected to In1 and In2 respectively
          // of the L298 motor driver
          pinMode(2, OUTPUT);
          pinMode(3, OUTPUT);
          pinMode(4, OUTPUT);
          pinMode(5, OUTPUT);
        }
        
        void loop() {
        
         //half turn in one direction
          digitalWrite(2, HIGH);
          digitalWrite(3, LOW);
          digitalWrite(4, LOW);
          digitalWrite(5, LOW);
          delay(1000); 
          stop(); 
          
         //half turn in the other direction
          digitalWrite(2, LOW);
          digitalWrite(3, LOW);
          digitalWrite(4, HIGH);
          digitalWrite(5, LOW);
          delay(1000); 
          stop();
          
         //full turn
         digitalWrite(2, LOW);
         digitalWrite(3, HIGH);
         digitalWrite(4, HIGH);
         digitalWrite(5, LOW);
         delay(2000); 
         stop();
        
        
         //front
         digitalWrite(2, LOW);
         digitalWrite(3, HIGH);
         digitalWrite(4, LOW);
         digitalWrite(5, HIGH);
         delay(2000); 
         stop();
        
         //back
         digitalWrite(2, HIGH);
         digitalWrite(3, LOW);
         digitalWrite(4, HIGH);
         digitalWrite(5, LOW);
         delay(2000); 
         stop();
         
        
        }
        
        void stop(){
          digitalWrite(2, LOW);
          digitalWrite(3, LOW);
          digitalWrite(4, LOW);
          digitalWrite(5, LOW);
          delay(500);
        }
![Figuring out angles and the motor](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/movements.jpg)
[Demonstration video](https://drive.google.com/file/d/1vEYi1s2UCRhs5zjfDxcMFtIEram1NQ80/view?usp=drive_link)

#### Issues: 
- Controlling the exact movements
- Controlling speed



###  * Hobby RC Controller *
In order to control our robot remotely, we used a Hobby RC shield on the arduino, connected to a remote control, which would give us the freedom to control the robot on demand and however we want. We initially used a code for the radio that moved at a uniform speed, but we then added the code to control the speed of robot:


        #include <EnableInterrupt.h>
        
        #define SERIAL_PORT_SPEED 9600
        #define RC_NUM_CHANNELS  4
        
        #define RC_CH1  0
        #define RC_CH2  1
        #define RC_CH3  2
        #define RC_CH4  3
        
        #define RC_CH1_PIN  12
        #define RC_CH2_PIN  9
        #define RC_CH3_PIN  8
        #define RC_CH4_PIN  7
        
        uint16_t rc_values[RC_NUM_CHANNELS];
        uint32_t rc_start[RC_NUM_CHANNELS];
        volatile uint16_t rc_shared[RC_NUM_CHANNELS];
        
        void rc_read_values() {
          noInterrupts();
          memcpy(rc_values, (const void *)rc_shared, sizeof(rc_shared));
          interrupts();
        }
        
        void calc_input(uint8_t channel, uint8_t input_pin) {
          if (digitalRead(input_pin) == HIGH) {
            rc_start[channel] = micros();
          } else {
            uint16_t rc_compare = (uint16_t)(micros() - rc_start[channel]);
            rc_shared[channel] = rc_compare;
          }
        }
        
        void calc_ch1() {
          calc_input(RC_CH1, RC_CH1_PIN);
        }
        void calc_ch2() {
          calc_input(RC_CH2, RC_CH2_PIN);
        }
        void calc_ch3() {
          calc_input(RC_CH3, RC_CH3_PIN);
        }
        void calc_ch4() {
          calc_input(RC_CH4, RC_CH4_PIN);
        }
        
        void setup() {
          Serial.begin(SERIAL_PORT_SPEED);
        
          pinMode(RC_CH1_PIN, INPUT); //side wheel
          pinMode(RC_CH2_PIN, INPUT); //trigger
          pinMode(RC_CH3_PIN, INPUT); // side knob
          pinMode(RC_CH4_PIN, INPUT); //bottom button
        
          enableInterrupt(RC_CH1_PIN, calc_ch1, CHANGE);
          enableInterrupt(RC_CH2_PIN, calc_ch2, CHANGE);
          enableInterrupt(RC_CH3_PIN, calc_ch3, CHANGE);
          enableInterrupt(RC_CH4_PIN, calc_ch4, CHANGE);
        
          pinMode(3, OUTPUT);
          pinMode(5, OUTPUT);
          pinMode(6, OUTPUT);
          pinMode(10, OUTPUT);
        }
        
        void loop() {
          rc_read_values();
        
          //  Serial.print("CH1:"); Serial.print(rc_values[RC_CH1]); Serial.print("\t");
          //  Serial.print("CH2:"); Serial.print(rc_values[RC_CH2]); Serial.print("\t");
          //  Serial.print("CH3:"); Serial.print(rc_values[RC_CH3]); Serial.print("\t");
          //  Serial.print("CH4:"); Serial.println(rc_values[RC_CH4]);
        
          delay(200);
        
          // robot moving forward and in reverse using Hobby RC controller
          if (rc_values[RC_CH2] > 1600) {
            forward(map(rc_values[RC_CH2], 1600, 2100, 0, 255));
          }
          else if (rc_values[RC_CH2] < 1400) reverse(map(rc_values[RC_CH2], 1400, 900, 0, 255));
        
          // robot moving right and left using Hobby RC controller
        
          else if (rc_values[RC_CH1] > 1600) right(map(rc_values[RC_CH1], 1600, 2100, 0, 255));
          else if (rc_values[RC_CH1] < 1400) left (map(rc_values[RC_CH1], 1400, 900, 0, 255));
          else stop();
        
        
        }
        
        void reverse(int speed) {
          Serial.print("CH2:"); Serial.print(rc_values[RC_CH2]);
          Serial.print(" front: "); Serial.println(speed);
          analogWrite(3, 0);
          analogWrite(5, speed);
          analogWrite(6, speed);
          analogWrite(10, 0);
        
        }
        
        void forward(int speed) {
          Serial.print("CH2:"); Serial.print(rc_values[RC_CH2]);
          Serial.print(" back: "); Serial.println(speed);
          analogWrite(3, speed);
          analogWrite(5, 0);
          analogWrite(6, 0);
          analogWrite(10, speed);
        
        }
        
        void right(int speed) {
          Serial.print("CH1:"); Serial.print(rc_values[RC_CH1]);
          Serial.print(" right: "); Serial.println(speed);
          analogWrite(3, 0);
          analogWrite(5, speed);
          analogWrite(6, 0);
          analogWrite(10, 0);
        
        }
        
        void left(int speed) {
          Serial.print("CH1:"); Serial.print(rc_values[RC_CH1]);
          Serial.print(" left: "); Serial.println(speed);
          analogWrite(3, 0);
          analogWrite(5, 0);
          analogWrite(6, speed);
          analogWrite(10, 0);
        
        }
        
        
        void stop() {
          analogWrite(3, 0);
          analogWrite(5, 0);
          analogWrite(6, 0);
          analogWrite(10, 0);
        }
[Demonstration ](https://drive.google.com/file/d/1i_314TlIeaqN-DG2CyszPHdHuCzQ7c6g/view?usp=drive_link)


###  *16/10 - Robot play suggestion*

The play follows 7 robots, all coded with fairly ‘simple’ algorithms, made for very specific tasks. 

The limited knowledge and simplicity of the robots (yes, no, maybe) leads to a series of misunderstandings that have the robots end up in situations they did not expect.

The robots however, learn from each other and start to gain knowledge of the world around them, but with some casualties along, leading the spectator to think of the consequences and responsibility of the creators of the robots.

###  * Servo motors *
In order to built our structure for the dancing cactus robot, we needed three stacked rods connected with servo motors to move in different directions in a pendulum-like manner. So, we embarked on a very long and obstacle-filled process of 1) controlling the servo motors and getting them to move how we want them to, and 2) connecting the servo motors to the rods and stabilizing the entire structure.

For the servo motor code, one of the biggest challenged was figuring out the correct angles to get every motor to move the way we want it, back and forth with certain degrees of freedom. This was a process of trial and error, we observed the behavior of the servo motors and utilized it in the code. Sometimes, the angles of the motors would change even-though they were previously calibrated. But, at the end, we were able to figure out a good balance and ended up with the following code as the dancing movements for the robot:

        #include <Servo.h>
        
        Servo myservo; 
        Servo myservo2;
        Servo myservo3;
        
        int pos = 70;    // variable to store the servo position
        int pos2 = 70;    // variable to store the servo2 position
        int pos3 = 70;    // variable to store the servo3 position
        
        
        void setup() {
         Serial.begin(9600);
          myservo.attach(9);
          myservo2.attach(10);
          myservo3.attach(11);
        
        
        }
        
        void loop() {
          int pos2 = 85;
          int pos3 = 55;
          for (pos = 55; pos <= 85; pos +=1) { 
            myservo.write(pos);
            myservo2.write(pos2);
             myservo3.write(pos3);
        
            delay(20); 
            pos2-=1;
            pos3+=1;
        //    Serial.println(pos);
        
        //    pos2 = pos2 += 1;
        //    myservo2.write(pos2);
        
          
          }
        //  
        //  pos2 = 55;
        //  for (pos = 85; pos >= 55; pos -=1){
        //    myservo.write(pos);
        //    myservo2.write(pos2);
        //    delay(15); 
        //    pos2+=1;
        //    delay(15);
        ////    Serial.println(pos);
        //
        //  }
        
        
        //  for (pos2 = 55; pos2 <= 85; pos2 +=1) { 
        //    myservo2.write(pos2);
        //    delay(15); 
        //    Serial.println(pos2);
        //
        //  }
        //
        //  for (pos2 = 85; pos2 >= 55; pos2 -=1){
        //    myservo2.write(pos2);
        //    delay(15);
        //    Serial.println(pos2);
        //
        //  }
          
        //
        //  for (pos3 = 80; po32 >= 60; pos3 -=1){
        //    myservo3.write(pos3);
        //    delay(20);
        //    Serial.println(pos3);
        //
        //  }
        //
        //  for (pos3 = 60; pos3 <= 80; pos3 +=1) { 
        //    myservo3.write(pos3);
        //    delay(20); 
        //    Serial.println(pos3);
        //
        //  }
                        
        }
        
        
        //60 right
        // 80 left

[Figuring out angles](https://drive.google.com/file/d/1raeyuQioLlZDRXU-9tEQKuLOxFMqyUo7/view?usp=drive_link)

[Servo testing](https://drive.google.com/file/d/1m_sVQ6p1YzEFK00D26gkBFE_X5NLKGez/view?usp=drive_link)

[Servo Testing with rod](https://drive.google.com/file/d/10tUkqMrKdUnyErQY8bLYIFvY35p8nDz5/view?usp=drive_link)

After figuring out the code and the angles, we then constructed our rods and connected them to the base, this was also a very challenging and time-consuming process as we used aluminum in order to make the robot lighter, but this proved to be challenging due to the stiffness of the material, which made it hard to drill into and cut. 

The other main difficulty was stabilizing the servo motors, notable the bottom one attached to the base, with the rods and the base. Because of the weight of the structure and the torque of the motor, the hub of the servo motor would keep getting loose upon movement. After trying out various solutions, we ended up welding the shaft to the hub in order to reduce movement and increase stability. We later added wooden stabilizers to the base in order to keep the robot from falling. 

![Hub](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/Hub.jpg)
![Welding](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/Welding.jpg)
![Structure](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/IMG_3296.JPG)



###  *Music Maker Shield and NeoPixels*

We then added Sound and Neopixels to our robot in order to animate it more. This process was relatively easy, we did face some issues in the final receiver code with the music, but were able to figure it out. This is the music maker shield code:
    
        // include SPI, MP3 and SD libraries
        #include <SPI.h>
        #include <Adafruit_VS1053.h>
        #include <SD.h>
        
        // define the pins used
        //#define CLK 13       // SPI Clock, shared with SD card
        //#define MISO 12      // Input data, from VS1053/SD card
        //#define MOSI 11      // Output data, to VS1053/SD card
        // Connect CLK, MISO and MOSI to hardware SPI pins. 
        // See http://arduino.cc/en/Reference/SPI "Connections"
        
        // These are the pins used for the breakout example
        #define BREAKOUT_RESET  9      // VS1053 reset pin (output)
        #define BREAKOUT_CS     10     // VS1053 chip select pin (output)
        #define BREAKOUT_DCS    8      // VS1053 Data/command select pin (output)
        // These are the pins used for the music maker shield
        #define SHIELD_RESET  -1      // VS1053 reset pin (unused!)
        #define SHIELD_CS     7      // VS1053 chip select pin (output)
        #define SHIELD_DCS    6      // VS1053 Data/command select pin (output)
        
        // These are common pins between breakout and shield
        #define CARDCS 4     // Card chip select pin
        // DREQ should be an Int pin, see http://arduino.cc/en/Reference/attachInterrupt
        #define DREQ 3       // VS1053 Data request, ideally an Interrupt pin
        
        Adafruit_VS1053_FilePlayer musicPlayer = 
          // create breakout-example object!
          // Adafruit_VS1053_FilePlayer(BREAKOUT_RESET, BREAKOUT_CS, BREAKOUT_DCS, DREQ, CARDCS);
          // create shield-example object!
          Adafruit_VS1053_FilePlayer(SHIELD_RESET, SHIELD_CS, SHIELD_DCS, DREQ, CARDCS);
          
        void setup() {
          Serial.begin(9600);
          Serial.println("Adafruit VS1053 Simple Test");
        
          if (! musicPlayer.begin()) { // initialise the music player
             Serial.println(F("Couldn't find VS1053, do you have the right pins defined?"));
             while (1);
          }
          Serial.println(F("VS1053 found"));
          
           if (!SD.begin(CARDCS)) {
            Serial.println(F("SD failed, or not present"));
            while (1);  // don't do anything more
          }
        
          // list files
          printDirectory(SD.open("/"), 0);
          
          // Set volume for left, right channels. lower numbers == louder volume!
          musicPlayer.setVolume(20,20);
        
          // Timer interrupts are not suggested, better to use DREQ interrupt!
          //musicPlayer.useInterrupt(VS1053_FILEPLAYER_TIMER0_INT); // timer int
        
          // If DREQ is on an interrupt pin (on uno, #2 or #3) we can do background
          // audio playing
          musicPlayer.useInterrupt(VS1053_FILEPLAYER_PIN_INT);  // DREQ int
          
          // Play one file, don't return until complete
          Serial.println(F("Playing track 001"));
          musicPlayer.playFullFile("/track001.mp3");
          // Play another file in the background, REQUIRES interrupts!
          Serial.println(F("Playing track 002"));
          musicPlayer.startPlayingFile("/track002.mp3");
        }
        
        void loop() {
          // File is playing in the  background
          if (musicPlayer.stopped()) {
            Serial.println("Done playing music");
            while (1) {
              delay(10);  // we're done! do nothing...
            }
          }
          if (Serial.available()) {
            char c = Serial.read();
            
            // if we get an 's' on the serial console, stop!
            if (c == 's') {
              musicPlayer.stopPlaying();
            }
            
            // if we get an 'p' on the serial console, pause/unpause!
            if (c == 'p') {
              if (! musicPlayer.paused()) {
                Serial.println("Paused");
                musicPlayer.pausePlaying(true);
              } else { 
                Serial.println("Resumed");
                musicPlayer.pausePlaying(false);
              }
            }
          }
        
          delay(100);
        }
        
        
        /// File listing helper
        void printDirectory(File dir, int numTabs) {
           while(true) {
             
             File entry =  dir.openNextFile();
             if (! entry) {
               // no more files
               //Serial.println("**nomorefiles**");
               break;
             }
             for (uint8_t i=0; i<numTabs; i++) {
               Serial.print('\t');
             }
             Serial.print(entry.name());
             if (entry.isDirectory()) {
               Serial.println("/");
               printDirectory(entry, numTabs+1);
             } else {
               // files have sizes, directories do not
               Serial.print("\t\t");
               Serial.println(entry.size(), DEC);
             }
             entry.close();
           }
        }


###  *Receiver and Transmitter*

#### Receiver Code

        /*
          m1->19
          m2->21
          m3->20
          m4->16
          m5->18
          m6->17
        */
        // code : https://github.com/michaelshiloh/resourcesForClasses/tree/master/src/arduinoSketches/nRF24L01/rf24PerformingRobotsTemplate_2023
        #define m1 19
        #define m2 21
        #define m3 20
        #define m4 16
        #define m5 18
        #define m6 17
        // ================================================
        // ============= RADIO DECLARE BEGIN ==============
        // ================================================
        
        const int NRF_CE_PIN = A11, NRF_CSN_PIN = A15;
        
        #include <SPI.h>
        #include <nRF24L01.h>
        #include <RF24.h>
        RF24 radio(NRF_CE_PIN, NRF_CSN_PIN);  // CE, CSN
        const byte addr = 0x76;               // change as per the above assignment
        const int RF24_CHANNEL_NUMBER = 30;   // change as per the above assignment
        
        // Do not make changes here
        const byte xmtrAddress[] = { addr, addr, 0xC7, 0xE6, 0xCC };
        const byte rcvrAddress[] = { addr, addr, 0xC7, 0xE6, 0x66 };
        
        const int RF24_POWER_LEVEL = RF24_PA_LOW;
        
        // global variables
        uint8_t pipeNum;
        unsigned int totalTransmitFailures = 0;
        
        struct DataStruct {
          uint8_t stateNumber;
        };
        DataStruct data;
        
        // ================================================
        // ============== RADIO DECLARE END ===============
        // ================================================
        
        // ================================================
        // ============= SERVO DECLARE BEGIN ==============
        // ================================================
        
        #include <Servo.h>
        
        Servo myservo1;  // create servo object to control a servo
        Servo myservo2;
        Servo myservo3;
        
        int pos = 0;  // variable to store the servo position
        const int pos11 = 0;
        const int pos12 = 20;
        int pos2 = 45;
        int pos3 = 100;
        
        unsigned long interval = 40;       // the time we need to wait
        unsigned long previousMillis = 0;  // millis() returns an unsigned long.
        unsigned long previousMillis2 = 0;
        
        bool flag = false;
        
        // ================================================
        // ============== SERVO DECLARE END ===============
        // ================================================
        
        // ================================================
        // ============ NEOPIXEL DECLARE BEGIN ============
        // ================================================
        
        // Additional libraries for graphics on the Neo Pixel Matrix
        #include <Adafruit_NeoPixel.h>
        #include <Adafruit_GFX.h>
        #include <Adafruit_NeoMatrix.h>
        #ifndef PSTR
        #define PSTR  // Make Arduino Due happy
        #endif
        
        const int NEOPIXELPIN = m6;  // Neopixel pin for the mouth
        #define NUMPIXELS_MOUTH 64   // Number of pixels in the mouth matrix
        
        const int NUMPIXELS = m5;    // Neopixel pin for the other LED strip
        #define NUMPIXELS_OTHER 180  // Number of pixels in the other LED strip
        
        Adafruit_NeoPixel pixelsMouth = Adafruit_NeoPixel(NUMPIXELS_MOUTH, NEOPIXELPIN, NEO_GRB + NEO_KHZ800);
        Adafruit_NeoPixel pixelsOther = Adafruit_NeoPixel(NUMPIXELS_OTHER, NUMPIXELS, NEO_GRB + NEO_KHZ800);
        
        int ledState = LOW;
        // unsigned long previousMillis = 0;
        const long interval2 = 10;
        int currentLED = 0;
        
        int mouthClosed[] = { 18, 19, 20, 21, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 42, 43, 44, 45 };
        int mouthOpen[] = { 3, 4, 10, 11, 12, 13, 17, 18, 21, 22, 24, 25, 30, 31, 32, 33, 38, 39, 41, 42, 45, 46, 50, 51, 52, 53, 59, 60 };
        //int mouthClosed[] = { 18, 19, 20, 21, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 42, 43, 44, 45 };
        
        
        int currentState = 0;  // 0 for closed mouth, 1 for open mouth
        unsigned long previousMouthMillis = 0;
        const unsigned long CLOSED_DELAY = 500;  // Delay for closed mouth (in milliseconds)
        const unsigned long OPEN_DELAY = 500;    // Delay for open mouth (in milliseconds)
        
        // ================================================
        // ============= NEOPIXEL DECLARE END =============
        // ================================================
        
        // ================================================
        // ============= MUSIC DECLARE BEGIN ==============
        // ================================================
        
        // Additional libraries for music maker shield
        #include <Adafruit_VS1053.h>
        #include <SD.h>
        
        // Adafruit music maker shield
        #define SHIELD_RESET -1  // VS1053 reset pin (unused!)
        #define SHIELD_CS 7      // VS1053 chip select pin (output)
        #define SHIELD_DCS 6     // VS1053 Data/command select pin (output)
        #define CARDCS 4         // Card chip select pin
        // DREQ should be an Int pin, see http://arduino.cc/en/Reference/attachInterrupt
        #define DREQ 3  // VS1053 Data request, ideally an Interrupt pin
        Adafruit_VS1053_FilePlayer musicPlayer = Adafruit_VS1053_FilePlayer(SHIELD_RESET, SHIELD_CS, SHIELD_DCS, DREQ, CARDCS);
        
        // ================================================
        // ============== MUSIC DECLARE END ===============
        // ================================================
        
        // ================================================
        // ================== Setup BEGIN =================
        // ================================================
        
        void setup() {
          Serial.begin(9600);
          // printf_begin();
        
          // Set up all the attached hardware
          setupMusicMakerShield();
          setupServoMotors();
          setupNeoPixels();
        
          setupRF24();
        
          // Brief flash to show we're done with setup()
          //  flashNeoPixels();
        }
        
        // ================================================
        // =================== Setup END ==================
        // ================================================
        
        // ================================================
        // =============== SETUP FUNCTIONS ================
        // ================================================
        void setupRF24Common() {
        
          // RF24 setup
          if (!radio.begin()) {
            Serial.println(F("radio  initialization failed"));
            while (1)
              ;
          } else {
            Serial.println(F("radio successfully initialized"));
          }
        
          radio.setDataRate(RF24_250KBPS);
          radio.setChannel(RF24_CHANNEL_NUMBER);
          radio.setPALevel(RF24_POWER_LEVEL);
        }
        void setupRF24() {
          setupRF24Common();
        
          // Set us as a receiver
          radio.openWritingPipe(rcvrAddress);
          radio.openReadingPipe(1, xmtrAddress);
        
          // radio.printPrettyDetails();
          Serial.println(F("I am a receiver"));
        }
        
        void setupMusicMakerShield() {
          if (!musicPlayer.begin()) {  // initialise the music player
            Serial.println(F("Couldn't find VS1053, do you have the right pins defined?"));
            while (1)
              ;
          }
          Serial.println(F("VS1053 found"));
        
          if (!SD.begin(CARDCS)) {
            Serial.println(F("SD card failed or not present"));
            while (1)
              ;  // don't do anything more
          }
        
          // Set volume for left, right channels. lower numbers == louder volume!
          musicPlayer.setVolume(20, 20);
        
          // Timer interrupts are not suggested, better to use DREQ interrupt!
          // musicPlayer.useInterrupt(VS1053_FILEPLAYER_TIMER0_INT); // timer int
        
          // If DREQ is on an interrupt pin (on uno, #2 or #3) we can do background
          // audio playing
          musicPlayer.useInterrupt(VS1053_FILEPLAYER_PIN_INT);  // DREQ int
        }
        
        void setupServoMotors() {
          myservo1.attach(m1);
          myservo2.attach(m2);
          myservo3.attach(m3);
        }
        
        void setupNeoPixels() {
          pixelsMouth.begin();
          pixelsMouth.setBrightness(50);  // Set the brightness (0-255)
        
          pixelsOther.begin();
        }
        
        // ================================================
        // ================== SETUP END ===================
        // ================================================
        
        // ================================================
        // =============== LOOP FUNCTIONS =================
        // ================================================
        
        // Function to display mouth based on provided positions for a specific Neopixel strip
        void displayMouth(Adafruit_NeoPixel &strip, int mouth[], int size, uint32_t color) {
          strip.clear();
          for (int i = 0; i < size; i++) {
            strip.setPixelColor(mouth[i], color);
          }
          strip.show();
        }
        
        void ledStrip() {
          unsigned long currentMillis = millis();
          // Handling the other LED strip
          if (currentMillis - previousMillis >= interval2) {
            previousMillis = currentMillis;
        
            pixelsOther.clear();
            pixelsOther.setPixelColor(currentLED, pixelsOther.Color(random(255), random(255), random(255)));
            pixelsOther.show();
        
            currentLED = (currentLED + 1) % NUMPIXELS_OTHER;
          }
        }
        
        void dance() {
          unsigned long currentMillis = millis();
          Serial.print("servo: ");
          // Serial.print("pos1 ");
          // Serial.print(pos);
        
          // Serial.print("    pos2 ");
          // Serial.print(pos2);
        
          // Serial.print("    pos3 ");
          // Serial.println(pos3);
        
        
          if ((unsigned long)(currentMillis - previousMillis2) >= interval) {
            previousMillis2 = currentMillis;
            myservo1.write(pos);
            myservo2.write(pos2);
            myservo3.write(pos3);
            // check direction and add for one/ subtract for one
            if (flag) {
              Serial.print("flag: ");
              Serial.print("pos1 ");
              Serial.print(pos);
        
              Serial.print("    pos2 ");
              Serial.print(pos2);
        
              Serial.print("    pos3 ");
              Serial.println(pos3);
        
              pos -= 1;
              pos2 -= 1;
              pos3 -= 1;
            } else {
              Serial.print("!flag: ");
              Serial.print("pos1 ");
              Serial.print(pos);
        
              Serial.print("    pos2 ");
              Serial.print(pos2);
        
              Serial.print("    pos3 ");
              Serial.println(pos3);
              pos += 1;
              pos2 += 1;
              pos3 += 1;
            }
            // check location and change flag if direction will change
            if (pos >= pos12) {
              flag = true;
            }
            if (pos <= pos11) {
              flag = false;
            }
          }
        }
        
        void talkingMouth() {
          unsigned long currentMouthMillis = millis();
          if (currentState == 0) {                                                                           // Closed mouth
            displayMouth(pixelsMouth, mouthClosed, sizeof(mouthClosed) / sizeof(mouthClosed[0]), 0xFF0000);  // Change color as needed
            if (currentMouthMillis - previousMouthMillis >= CLOSED_DELAY) {
              previousMouthMillis = currentMouthMillis;
              currentState = 1;  // Change state to open mouth
            }
          } else {                                                                                     // Open mouth
            displayMouth(pixelsMouth, mouthOpen, sizeof(mouthOpen) / sizeof(mouthOpen[0]), 0xFF0000);  // Change color as needed
            if (currentMouthMillis - previousMouthMillis >= OPEN_DELAY) {
              previousMouthMillis = currentMouthMillis;
              currentState = 0;  // Change state to closed mouth
            }
          }
        }
        
        // ================================================
        // ============== LOOP FUNCTIONS END ==============
        // ================================================
        
        void loop() {
          // If there is data, read it,
          // and do the needfull
          // Become a receiver
          radio.startListening();
          pixelsOther.clear();
        
          if (radio.available(&pipeNum)) {
            radio.read(&data, sizeof(data));
            Serial.print(F("message received Data = "));
            Serial.print(data.stateNumber);
            Serial.println();
            
        
            switch (data.stateNumber) {
              case 0:
                while (true) {
                  radio.read(&data, sizeof(data));
                  pixelsOther.clear();
                  if (data.stateNumber != 0) {
                    break;
                  }
                }
                break;
        
              //SCRIPT
              case 1:
                Serial.println(F("Playing track 001"));
                musicPlayer.startPlayingFile("/track1.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  //          ledStrip();
                }
                break;
        
        
              case 2:
                Serial.println(F("Playing track 002"));
                musicPlayer.startPlayingFile("/track2.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                }
                break;
        
        
              case 3:
                Serial.println(F("Playing track 003"));
                musicPlayer.startPlayingFile("/track3.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                }
                break;
        
        
        
              case 4:
                Serial.println(F("Playing track 004"));
                musicPlayer.startPlayingFile("/track4.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                }
        
                break;
        
        
              case 5:
                Serial.println(F("Playing track 005"));
                musicPlayer.startPlayingFile("/track5.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  //          ledStrip();
                }
                break;
        
        
              case 6:
                Serial.println(F("Playing track 006"));
                musicPlayer.startPlayingFile("/track6.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  //          ledStrip();
                }
                break;
        
        
        
              case 7:
                Serial.println(F("Playing track 007"));
                musicPlayer.startPlayingFile("/track7.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  //          ledStrip();
                }
                break;
        
        
              case 8:
                Serial.println(F("Playing track 008"));
                musicPlayer.startPlayingFile("/track8.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  //          ledStrip();
                }
                break;
        
        
        
              case 9:
                Serial.println(F("Playing track 009"));
                musicPlayer.startPlayingFile("/track9.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  //          ledStrip();
                }
                break;
        
        
              case 10:
                Serial.println(F("Playing track 10"));
                musicPlayer.startPlayingFile("/track10.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  //          ledStrip();
                }
                break;
        
              case 11:
                Serial.println(F("Playing monologue"));
                musicPlayer.startPlayingFile("/monologue.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  ledStrip();
                }
                break;
        
        
              case 12:
                Serial.println(F("Playing alejandro"));
                musicPlayer.startPlayingFile("/alejandro.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  //          ledStrip();
                }
                break;
        
        
              case 13:
                Serial.println(F("Playing track agreed"));
                musicPlayer.startPlayingFile("/agreed.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  //          ledStrip();
                }
                break;
        
        
              case 14:
                Serial.println(F("Playing track GASP"));
                musicPlayer.startPlayingFile("/GASP.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  ledStrip();
                }
                break;
        
        
              case 15:
                Serial.println(F("Playing track wohoo"));
                musicPlayer.startPlayingFile("/wohoo.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  ledStrip();
                }
                break;
        
        
              case 16:
                Serial.println(F("Playing track shikshak"));
                musicPlayer.startPlayingFile("/shikshak.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  ledStrip();
                }
                break;
        
        
              case 17:
                Serial.println(F("Playing track ruby"));
                musicPlayer.startPlayingFile("/ruby.mp3");
        
                while (musicPlayer.playingMusic) {
                  dance();
                  talkingMouth();
                  ledStrip();
                }
                break;
        
              //      case 1:
              //        while (true) {
              //          radio.read(&data, sizeof(data));
              //          dance();
              ////          ledStrip();
              //          talkingMouth();
              //          if (data.stateNumber != 0) {
              //            break;
              //          }
              //        }
              ////        Serial.println(F("Playing track 001"));
              ////        musicPlayer.startPlayingFile("/track001.mp3");
              //
              //        break;
              //      case 2: //ALL
              //        while (true) {
              //          radio.read(&data, sizeof(data));
              //          dance();
              //          ledStrip();
              //          talkingMouth();
              //          if (data.stateNumber != 0) {
              //            break;
              //          }
              //        }
              //        Serial.println(F("Playing track 002"));
              //        musicPlayer.startPlayingFile("/shikshak.mp3.mp3");
              //        break;
              //      case 3:
              //        while (true) {
              //          radio.read(&data, sizeof(data));
              ////          dance();
              ////          ledStrip();
              //          talkingMouth();
              //          if (data.stateNumber != 0) {
              //            break;
              //          }
              //        }
              //        Serial.println(F("Playing track 002"));
              //        musicPlayer.startPlayingFile("/track002.mp3");
              //        break;
              //      case 4:
              //      while (true) {
              //          radio.read(&data, sizeof(data));
              //          dance();
              ////          ledStrip();
              ////          talkingMouth();
              //          if (data.stateNumber != 0) {
              //            break;
              //          }
              //        }
              //        Serial.println(F("Playing track 002"));
              //        musicPlayer.startPlayingFile("/track002.mp3");
              //        break;
              //
              //      case 5:
              //        while (true) {
              //          radio.read(&data, sizeof(data));
              //          dance();
              //          ledStrip();
              ////          talkingMouth();
              //          if (data.stateNumber != 0) {
              //            break;
              //          }
              //        }
              //        Serial.println(F("ruby.mp3"));
              //        musicPlayer.startPlayingFile("/ruby.mp3.mp3");
              //        break;
              default:
                Serial.println(F("Invalid option"));
            }
            displayMouth(pixelsMouth, mouthClosed, sizeof(mouthClosed) / sizeof(mouthClosed[0]), 0xFF0000);  // Change color as needed
          }
        }


#### Transmitter Code

                
                /*
                   Using the nRF24L01 radio module to communicate
                   between two Arduinos with much increased reliability following
                   various tutorials, conversations, and studying the nRF24L01 datasheet
                   and the library reference.
                
                   Transmitter is
                   https://github.com/michaelshiloh/resourcesForClasses/tree/master/kicad/Arduino_Shield_RC_Controller
                
                  Receiver is
                  https://github.com/instituteforcriticalrobotics/instituteforcriticalrobotics.github.io/tree/main/kicad/nRF_Mega
                
                   This file contains code for both transmitter and receiver.
                   Transmitter at the top, receiver at the bottom.
                   One of them is commented out.
                
                   These sketches require the RF24 library by TMRh20
                   Documentation here: https://nrf24.github.io/RF24/index.html
                
                   change log
                
                   11 Oct 2023 - ms - initial entry based on
                                  rf24PerformingRobotsTemplate
                   26 Oct 2023 - ms - revised for new board: nRF_Servo_Mega rev 2
                   28 Oct 2023 - ms - add demo of NeoMatrix, servo, and Music Maker Shield
                */
                
                // Common code
                //
                
                // Common pin usage
                // Note there are additional pins unique to transmitter or receiver
                //
                
                // nRF24L01 uses SPI which is fixed
                // on pins 11, 12, and 13 on the Uno
                // and on pins 50, 51, and 52 on the Mega
                
                // It also requires two other signals
                // (CE = Chip Enable, CSN = Chip Select Not)
                // Which can be any pins:
                
                // For the transmitter
                const int NRF_CE_PIN = A4, NRF_CSN_PIN = A5;
                
                // for the receiver
                //const int NRF_CE_PIN = A11, NRF_CSN_PIN = A15;
                
                // In summary,
                // nRF 24L01 pin    Uno Mega   name
                //          1                     GND
                //          2                     3.3V
                //          3       9   A11       CE
                //          4       10  A15       CSN
                //          5       13  SCLK
                //          6       11  MOSI/COPI
                //          7       12  MISO/CIPO
                
                #include <SPI.h>
                #include <nRF24L01.h>
                #include <RF24.h>
                RF24 radio(NRF_CE_PIN, NRF_CSN_PIN);  // CE, CSN
                
                //#include <printf.h>  // for debugging
                
                // See note in rf24Handshaking about address selection
                //
                
                // Channel and address allocation:
                // Fatema, Jannah: Channel 30, addr = 0x76
                // Andres, Ryan: Channel 40, addr = 0x73
                // Nouf, Shaikha:  Channel 50, addr = 0x7C
                // Aadhar, Shanaia: Channel 60, addr = 0xC6
                // Akhat, Yunho:  Channel 70, addr = 0xC3
                // Aakif, Marta: Channel 80, addr = 0xCC
                // Yerk, Hamad: Channel 90, addr = 0x33
                const byte addr = 0x76;             // change as per the above assignment
                const int RF24_CHANNEL_NUMBER = 30;  // change as per the above assignment
                
                // Do not make changes here
                const byte xmtrAddress[] = { addr, addr, 0xC7, 0xE6, 0xCC };
                const byte rcvrAddress[] = { addr, addr, 0xC7, 0xE6, 0x66 };
                
                const int RF24_POWER_LEVEL = RF24_PA_LOW;
                
                // global variables
                uint8_t pipeNum;
                unsigned int totalTransmitFailures = 0;
                
                struct DataStruct {
                  uint8_t stateNumber;
                };
                DataStruct data;
                
                void setupRF24Common() {
                
                  // RF24 setup
                  if (!radio.begin()) {
                    Serial.println(F("radio  initialization failed"));
                    while (1)
                      ;
                  } else {
                    Serial.println(F("radio successfully initialized"));
                  }
                
                  radio.setDataRate(RF24_250KBPS);
                  radio.setChannel(RF24_CHANNEL_NUMBER);
                  radio.setPALevel(RF24_POWER_LEVEL);
                }
                
                // Transmitter code
                
                // Transmitter pin usage
                const int LCD_RS_PIN = 3, LCD_EN_PIN = 2, LCD_D4_PIN = 4, LCD_D5_PIN = 5, LCD_D6_PIN = 6, LCD_D7_PIN = 7;
                const int SW1_PIN = 8, SW2_PIN = 9, SW3_PIN = 10, SW4_PIN = A3, SW5_PIN = A2;
                
                // LCD library code
                #include <LiquidCrystal.h>
                
                // initialize the library with the relevant pins
                LiquidCrystal lcd(LCD_RS_PIN, LCD_EN_PIN, LCD_D4_PIN, LCD_D5_PIN, LCD_D6_PIN, LCD_D7_PIN);
                
                
                const int NUM_OF_STATES = 18;
                char *theStates[] = {"0 robot nothing",
                                     "1 robot line1",
                                     "2 robot line2",
                                     "3 robot line3",
                                     "4 robot line4",
                                     "5 robot line5",
                                     "6 robot line6",
                                     "7 robot line7",
                                     "8 robot line8",
                                     "9 robot line9",
                                     "10 robot line10",
                                     "11 robot monologue",
                                     "12 robot alejandro",
                                     "13 robot agreed",
                                     "14 robot GASP",
                                     "15 robot wohoo",
                                     "16 robot SHIKSHAK",
                                     "17 robot ruby",
                                    };
                
                void updateLCD() {
                
                  lcd.clear();
                  lcd.print(theStates[data.stateNumber]);
                  lcd.setCursor(0, 1); // column, line (from 0)
                  lcd.print("not transmitted yet");
                }
                
                void countDown() {
                  if (--data.stateNumber < 0) {
                    data.stateNumber = 0;
                  }
                  updateLCD();
                }
                
                void countUp() {
                  if (++data.stateNumber >= NUM_OF_STATES) {
                    data.stateNumber = NUM_OF_STATES-1;
                  }
                  updateLCD();
                }
                
                
                void stopYamma() {}
                void spare2() {}
                
                void rf24SendData() {
                
                  radio.stopListening(); // go into transmit mode
                  // The write() function will block
                  // until the message is successfully acknowledged by the receiver
                  // or the timeout/retransmit maxima are reached.
                  int retval = radio.write(&data, sizeof(data));
                
                  lcd.clear();
                  lcd.setCursor(0, 0); // column, line (from 0)
                  lcd.print("transmitting");
                  lcd.setCursor(14, 0); // column, line (from 0)
                  lcd.print(data.stateNumber);
                
                  Serial.print(F(" ... "));
                  if (retval) {
                    Serial.println(F("success"));
                    lcd.setCursor(0, 1); // column, line (from 0)
                    lcd.print("success");
                  } else {
                    totalTransmitFailures++;
                    Serial.print(F("failure, total failures = "));
                    Serial.println(totalTransmitFailures);
                
                    lcd.setCursor(0, 1); // column, line (from 0)
                    lcd.print("error, total=");
                    lcd.setCursor(13, 1); // column, line (from 0)
                    lcd.print(totalTransmitFailures);
                  }
                }
                
                class Button
                {
                    int pinNumber;
                    bool previousState;
                    void(* buttonFunction)();
                  public:
                
                    // Constructor
                    Button(int pn, void* bf) {
                      pinNumber = pn;
                      buttonFunction = bf;
                      previousState = 1;
                    }
                
                    // update the button
                    void update() {
                      bool currentState = digitalRead(pinNumber);
                      if (currentState == LOW && previousState == HIGH) {
                        Serial.print("button on pin ");
                        Serial.print(pinNumber);
                        Serial.println();
                        buttonFunction();
                      }
                      previousState = currentState;
                    }
                };
                
                const int NUMBUTTONS = 5;
                Button theButtons[] = {Button(SW1_PIN, countDown),
                                       Button(SW2_PIN, rf24SendData),
                                       Button(SW3_PIN, countUp),
                                       Button(SW4_PIN, stopYamma),
                                       Button(SW5_PIN, spare2),
                                      };
                
                void setupRF24() {
                
                  setupRF24Common();
                
                  // Set us as a transmitter
                  radio.openWritingPipe(xmtrAddress);
                  radio.openReadingPipe(1, rcvrAddress);
                
                  // radio.printPrettyDetails();
                  Serial.println(F("I am a transmitter"));
                
                  data.stateNumber = 0;
                }
                
                void setup() {
                  Serial.begin(9600);
                
                  // set up the LCD's number of columns and rows:
                  lcd.begin(16, 2);
                  // Print a message to the LCD.
                  lcd.print("Radio init ....");
                
                  setupRF24();
                
                  // Initialize the switches
                  pinMode(SW1_PIN, INPUT_PULLUP);
                  pinMode(SW2_PIN, INPUT_PULLUP);
                  pinMode(SW3_PIN, INPUT_PULLUP);
                  pinMode(SW4_PIN, INPUT_PULLUP);
                  pinMode(SW5_PIN, INPUT_PULLUP);
                  lcd.setCursor(0, 1); // column, line (from 0)
                  lcd.print("setup() finished");
                
                  updateLCD();
                }
                
                
                
                void loop() {
                  for (int i = 0; i < NUMBUTTONS; i++) {
                    theButtons[i].update();
                  }
                  delay(50); // for testing
                }
                
                
                void clearData() {
                  // set all fields to 0
                  data.stateNumber = 0;
                }
                
                // End of transmitter code


### *Final product- FIFI the dancing cactus robot*

![FIFI](https://github.com/j-da-savage/Performing-robots/blob/main/Pictures/fifiPIC.jpeg)

[FIFI Dancing](https://drive.google.com/file/d/1GSkqdH5HsAr_0j0N-se8m-Fh96oVDB2w/view?usp=drive_link)
