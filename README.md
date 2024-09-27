# RISC-V_FlightComputer_Semicon2024
## **Overview**
This project aims to develop a highly efficient, cost-effective flight computer leveraging the power of the RISC-V architecture. The system is designed for applications in unmanned aerial vehicles (UAVs), allowing precise control and seamless communication between the flight controller and receiver modules. The core of the system revolves around an Arduino Nano and the VSD Squadron Mini, equipped with a RISC-V CH32V003 microcontroller.

Our custom flight controller handles communication through SPI and I2C protocols, ensuring reliable data transfer between the transmitter and receiver while managing all essential flight controls like throttle, ailerons, elevators, and rudders. The project serves as a versatile solution for UAV enthusiasts and developers, allowing easy integration with various modules and sensors for extended functionalities.

## **Objectives**
To design and implement a modular flight computer system based on RISC-V architecture.
Achieve reliable wireless communication between the transmitter and receiver using the NRF24L01+ module.
Use the I2C protocol to ensure smooth data transfer from the receiver to the flight controller.
Provide precise control over flight operations with customizable inputs and outputs.
System Architecture
**The RISC-V flight computer system consists of two primary subsystems**:
the transmitter module and the receiver module. The transmitter processes user inputs, while the receiver communicates with the flight controller to execute commands.

### **Transmitter Module**:
- **Arduino Nano**:
 Responsible for processing user input and sending flight control commands.
- **NRF24L01+ Module**:
  Utilized for wireless communication with the receiver. The data is transmitted using the SPI protocol.
- **Custom Input Controls**:
  Manages various flight control parameters like throttle, ailerons, and elevator adjustments.
  ![image](https://github.com/user-attachments/assets/5b815d69-1f9d-41d7-87f4-0797cd30c463)


- **Receiver Module**:
- **Arduino Nano**:
 Receives data transmitted by the NRF24L01+ module using SPI protocol.
- **I2C Communication**:
    Sends the received data to the VSD Squadron Mini, which processes and manages all flight controls.
- **Flight Controller (VSD Squadron Mini)**:
- **RISC-V CH32V003 Microcontroller**:
  This is the core of the flight computer, processing all inputs received from the Arduino Nano via I2C. It controls flight mechanics such as throttle, rudder, and other vital functions, ensuring stable and precise operation.
   ![image](https://github.com/user-attachments/assets/e726c532-e4cd-4186-8594-2f8715d40bba)

## **Technical Specifications**
- **Microcontroller**: RISC-V CH32V003 (on VSD Squadron Mini)
- **Transmitter**: Arduino Nano
- **Receiver**: Arduino Nano
- **Wireless Communication Module**: NRF24L01+ using SPI
- **Inter-Module Communication**: I2C between Arduino Nano (Receiver) and VSD Squadron Mini
## **Circut diagram**
- Transmitter circut diagram
![image](https://github.com/user-attachments/assets/95f583b6-9120-4634-aedc-3956e72db03b)
- Reciver circut dig
![image](https://github.com/user-attachments/assets/6e9350f8-c33d-4584-aca1-6f050fcc538b)


## **RISCV FC Code**
```
int ch_1=0;
int ch_2=0;
int ch_3=0;
int ch_4=0;
int ch_5=0;
int ch_6=0;

struct Signal {
  byte throttle;
  byte pitch;
  byte roll;
  byte yaw;
  byte aux1;
  byte aux2;
};

Signal data={0,0,0,0,0,0};

void setup()
{
  Serial.begin(9600);
  Serial.setTimeout(200); 
  
} 
void writeMicroseconds(int servopin,int ms){
  pinMode(servopin,OUTPUT);
  digitalWrite(servopin,HIGH);
  delayMicroseconds(ms);
  digitalWrite(servopin,LOW);
  delayMicroseconds(2500-ms);
}
void loop()
{
 
  if (Serial.available() >= sizeof(data)) {
    Serial.readBytes((char*)&data, sizeof(data));
    ch_1 = data.throttle;
    ch_2 = data.pitch;
    ch_3 = data.roll;
    ch_4 = data.yaw;
    ch_5 = data.aux1;
    ch_6 = data.aux2;
    writeMicroseconds(PD2,ch_1*10);
    writeMicroseconds(PC4,ch_2*10);
    writeMicroseconds(PC3,ch_3*10);
    writeMicroseconds(PC0,ch_4*10);
    Serial.print("Throttle: ");
    Serial.print(ch_1);
    Serial.print(" Pitch: ");
    Serial.print(ch_2);
    Serial.print(" Roll: ");
    Serial.print(ch_3);
    Serial.print(" Yaw: ");
    Serial.print(ch_4);
    Serial.print(" Aux1: ");
    Serial.print(ch_5);
    Serial.print(" Aux2: ");
    Serial.println(ch_6);
  }
}
```

**Aurduino code**
```
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>

int ch_1 = 0;
int ch_2 = 150;
int ch_3 = 150;
int ch_4 = 150;
int ch_5 = 0;
int ch_6 = 0;



struct Signal {
byte throttle;      
byte pitch;
byte roll;
byte yaw;
byte aux1;
byte aux2;
};

Signal data;

const uint64_t pipeIn = 0xE9E8F0F0E1LL;
RF24 radio(2, 3); 

void ResetData()
{
  data.throttle = 100;   
  data.pitch = 150;   
  data.roll = 150;     
  data.yaw = 150;     
  data.aux1 = 0;   
  data.aux2 = 0; 
}

void setup()
{
  //Set the pins for each PWM signal 
 

  //Configure the NRF24 module
  ResetData();
  radio.begin();
  radio.openReadingPipe(1,pipeIn);
  radio.setAutoAck(false);
  radio.setDataRate(RF24_250KBPS);
  radio.setPALevel(RF24_PA_MIN);
  radio.startListening(); //start the radio comunication for receiver
  Serial.begin(9600);

}
unsigned long lastRecvTime = 0;

void recvData()
{
while ( radio.available() ) {
radio.read(&data, sizeof(Signal));
lastRecvTime = millis(); 
  }
    }
void loop()
{
recvData();
unsigned long now = millis();
if ( now - lastRecvTime > 1000 ) {
ResetData(); 
}
ch_1 = data.throttle;     // pin D5 (PWM signal)
ch_2 = data.pitch;     // pin D3 (PWM signal)
ch_3 = data.roll;     // pin D4 (PWM signal)
ch_4 = data.yaw;    // pin D2 (PWM signal)

ch_5 = data.aux1;     // pin D6 (PWM signal)
ch_6 = data.aux2;     // pin D7 (PWM signal)


Serial.write((byte*)&data, sizeof(data));
delay(100);
}
```
## **Transmitter code**

```
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>
  const uint64_t pipeOut = 0xE9E8F0F0E1LL;  
  RF24 radio(2, 3); // select CE,CSN pin 
  struct Signal {
  byte throttle;
  byte pitch;
  byte roll;
  byte yaw;
  byte aux1;
  byte aux2;
};
  Signal data;
  void ResetData() 
{
  data.throttle = 0;  
  data.pitch = 150;   
  data.roll = 150;     
  data.yaw = 150;   
  data.aux1 = 150;    
  data.aux2 = 150;    
}
  void setup()
{
  //Start everything up
  radio.begin();
  radio.openWritingPipe(pipeOut);
  radio.setAutoAck(false);
  radio.setDataRate(RF24_250KBPS);
  radio.setPALevel(RF24_PA_MIN);
  radio.stopListening(); //start the radio comunication for Transmitter | Verici olarak sinyal iletişimi başlatılıyor
  ResetData();
 Serial.begin(9600);
}

  void loop()
{

  data.roll = map( analogRead(A3), 0, 1023, 100 ,200 );
  data.throttle = map(analogRead(A0),1023,1,100,200);
  data.pitch = map(analogRead(A1),0,1023,100,200);
  data.yaw = map(analogRead(A6),0,1023,100,200);
  Serial.println(data.roll);
  radio.write(&data, sizeof(Signal));
}
```

## **Protocols**:
- **SPI**: Transmitter and Receiver communication through NRF24L01+.
- **I2C**: Communication between Receiver and VSD Squadron Mini for flight control.
- **Power Supply**: Supports various power configurations depending on the UAV setup.
## **Features**
- **Custom Flight Controller**: Designed to handle dynamic flight inputs and commands, allowing for customizable and expandable features.
- **RISC-V Architecture**: Utilizes the open-source RISC-V architecture for scalability and adaptability.
- **Wireless Communication**: SPI-based wireless data transmission via NRF24L01+.
- **Flight Control**: Full control over flight operations such as throttle, ailerons, elevators, and rudders.
## **Groundlevel Testing**
https://private-user-images.githubusercontent.com/171113429/371167415-71c4f411-5ce5-4add-9790-7fed913bb83c.mp4?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjczNTkxNzEsIm5iZiI6MTcyNzM1ODg3MSwicGF0aCI6Ii8xNzExMTM0MjkvMzcxMTY3NDE1LTcxYzRmNDExLTVjZTUtNGFkZC05NzkwLTdmZWQ5MTNiYjgzYy5tcDQ_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwOTI2JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDkyNlQxMzU0MzFaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1hMzk4ZjI2MGMyNWI5MzI4NmVlYWE2YTYzMDA2MDY2OTg5NTYxYzlhMDY1MTJlZWQwMGFiMDJlNTlkNWE5ZDg2JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.ssfpFbvQ6cQTE-n4JJcjUm1Lyu-J44KsjigxSA1eM5c
## **Semicon**
https://github.com/user-attachments/assets/94c8d74a-792c-4c41-a822-38ce032a5a74


## **Applications**
- UAV (Unmanned Aerial Vehicles)
- Aerospace research and development
- Drone flight control systems
- Education and development in RISC-V architecture and embedded systems
## **Future Enhancements**
- Addition of GPS and other telemetry systems for advanced navigation and autonomous flight.
- Integration of machine learning algorithms for predictive control and autonomous decision-making.
- Expansion to multi-channel communication for more complex flight operations and real-time data analytics.



