

#include <IRremote.h>

#include <Microduino_Motor.h>

#if defined(__AVR_ATmega32U4__) ||
(__AVR_ATmega1284P__) || defined(__AVR_ATmega644P__) ||
defined(__AVR_ATmega128RFA1__)

#define motor_pin0A 8  //PWM

#define motor_pin0B 6

#define motor_pin1A 7  //PWM 

#define motor_pin1B 5

#else

#define motor_pin0A 6  //PWM

#define motor_pin0B 8

#define motor_pin1A 5  //PWM

#define motor_pin1B 7

#define RECV_PIN 10

//遥控器按键编码定义

#define INCREASE 0x1FEF807    //增加+

#define NUM_1 0x1FE807F      //数字1

#define NUM_4 0x1FE20DF      //数字4

#define NUM_9 0x1FE906F      //数字9

#endif

 IRrecv irrecv(RECV_PIN);

 decode_results results;

Motor MotorLeft(motor_pin0A, motor_pin0B);

Motor MotorRight(motor_pin1A, motor_pin1B);

#define MAX_THROTTLE 255 //最大油门 100~255

#define MAX_STEERING 200 //最大转向 100~512

int16_t leftVal = 0;  //左边电机旋转速度

int16_t rightVal = 0;  //右边电机旋转速度

void setup() {

 
Serial.begin(115200);  //初始化

 
Serial.println("Microduino!");

  
irrecv.enableIRIn(); 

 
MotorLeft.Fix(1);

 
MotorRight.Fix(1);

 

 
delay(2000);

}

void loop() {

 if
(irrecv.decode(&results))

{

 
Serial.println(results.value, HEX);

 

 
switch (results.value) {

   
case INCREASE: 

       
leftVal+=10;

       
if (leftVal > 255)

         
leftVal = 255;

       
rightVal+=10;

       
if (rightVal > 255)

         
rightVal = 255;

         


     
break;

   

   
case NUM_1:

     
if (leftVal>0)

     
{           

           
leftVal = -leftVal;

           
rightVal = -rightVal;

         
}

     
break;

   
case NUM_9:

     

       
if (leftVal < 0)      //按9号键，旋转方向向右

         
{

           
leftVal = -leftVal;

           
rightVal = -rightVal;

         
}

     
break;

   
case NUM_4:

       
leftVal-=10;    //按4号键，旋转速度减10，最小不超过-255

       
if (leftVal < -255)

         
leftVal = -255;

       
rightVal-=10;

       
if (rightVal < -255)

         
rightVal = -255;

     
break;

  
default:break;

  }

irrecv.resume();

}

 
MotorLeft.Driver(leftVal);  //驱动电机

 
MotorRight.Driver(rightVal);

 
delay(15);

}

