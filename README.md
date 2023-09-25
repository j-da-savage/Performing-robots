# Performing Robots

# ROBOT #1 - my first robot
### Built with Fatema

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

