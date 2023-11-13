# Performing Robots

# ROBOT #1 - my first robot
###  *09/12/2023 - robot sketch*
meet عنتر (Aantar)!

![ANTAR](https://github.com/j-da-savage/Performing-robots/blob/main/sketch1.jpg)

###  *09/13-09/18 - building the robot base*

What we used:
-
-
-







###  *09/20 - dance_code_test1*

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
###  *09/25 - Robot story*

[Results](https://youtube.com/shorts/l13Hn9hQDg0?feature=share) (kindly ignore the little accident :) )
[Background Music](https://youtu.be/lRXLAeogv5U?si=yIH5ja6Ax0gdW9EB)


###  *10/01 - RC Hobby Controller*
      
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
        
          Serial.print("CH1:"); Serial.print(rc_values[RC_CH1]); Serial.print("\t");
          Serial.print("CH2:"); Serial.print(rc_values[RC_CH2]); Serial.print("\t");
          Serial.print("CH3:"); Serial.print(rc_values[RC_CH3]); Serial.print("\t");
          Serial.print("CH4:"); Serial.println(rc_values[RC_CH4]);
        
          delay(200);
        
        // robot moving forward and in reverse using Hobby RC controller
          if (rc_values[RC_CH2] > 1570) forward();
          if (rc_values[RC_CH2] < 1530) reverse();
          if ((rc_values[RC_CH2] < 1570) && (rc_values[RC_CH2] > 1530)) stop();
        
        // robot moving right and left using Hobby RC controller
        
          if (rc_values[RC_CH1] > 1510) right();
          if (rc_values[RC_CH1] < 1495) left ();
        
          
          
        }
        
        void forward() {
        
         digitalWrite(3, LOW);
         digitalWrite(5, HIGH);
         digitalWrite(6, LOW);
         digitalWrite(10, HIGH);
          
        }
        
        void reverse() {
        
         digitalWrite(3, HIGH);
         digitalWrite(5, LOW);
         digitalWrite(6, HIGH);
         digitalWrite(10, LOW);
          
        }
        
        void right() {
        
         digitalWrite(3, LOW);
         digitalWrite(5, HIGH);
         digitalWrite(6, LOW);
         digitalWrite(10, LOW);
          
        }
        
        void left() {
        
         digitalWrite(3, LOW);
         digitalWrite(5, LOW);
         digitalWrite(6, LOW);
         digitalWrite(10, HIGH);
          
        }
        
        
        void stop(){
          digitalWrite(3, LOW);
          digitalWrite(5, LOW);
          digitalWrite(6, LOW);
          digitalWrite(10, LOW);
        }

[results](https://youtube.com/shorts/sorYxpKn_Ek?feature=share)


###  *10/09 - RC Hobby Controller with speed*

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


###  *16/10 - Robot play suggestion*


The play follows 7 robots, all coded with fairly ‘simple’ algorithms, made for very specific tasks. 

The limited knowledge and simplicity of the robots (yes, no, maybe) leads to a series of misunderstandings that have the robots end up in situations they did not expect.

The robots however, learn from each other and start to gain knowledge of the world around them, but with some casualties along, leading the spectator to think of the consequences and responsibility of the creators of the robots.

###  *13/11 - Servo motors code*

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


