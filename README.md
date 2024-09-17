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
- **Receiver Module**:
- **Arduino Nano**:
 Receives data transmitted by the NRF24L01+ module using SPI protocol.
- **I2C Communication**:
    Sends the received data to the VSD Squadron Mini, which processes and manages all flight controls.
- **Flight Controller (VSD Squadron Mini)**:
- **RISC-V CH32V003 Microcontroller**:
  This is the core of the flight computer, processing all inputs received from the Arduino Nano via I2C. It controls flight mechanics such as throttle, rudder, and other vital functions, ensuring stable and precise operation.
## **Technical Specifications**
- **Microcontroller**: RISC-V CH32V003 (on VSD Squadron Mini)
- **Transmitter**: Arduino Nano
- **Receiver**: Arduino Nano
- **Wireless Communication Module**: NRF24L01+ using SPI
- **Inter-Module Communication**: I2C between Arduino Nano (Receiver) and VSD Squadron Mini
## **Protocols**:
- **SPI**: Transmitter and Receiver communication through NRF24L01+.
- **I2C**: Communication between Receiver and VSD Squadron Mini for flight control.
- **Power Supply**: Supports various power configurations depending on the UAV setup.
## **Features**
- **Custom Flight Controller**: Designed to handle dynamic flight inputs and commands, allowing for customizable and expandable features.
- **RISC-V Architecture**: Utilizes the open-source RISC-V architecture for scalability and adaptability.
- **Wireless Communication**: SPI-based wireless data transmission via NRF24L01+.
- **Flight Control**: Full control over flight operations such as throttle, ailerons, elevators, and rudders.
## **Applications**
- UAV (Unmanned Aerial Vehicles)
- Aerospace research and development
- Drone flight control systems
- Education and development in RISC-V architecture and embedded systems
## **Future Enhancements**
- Addition of GPS and other telemetry systems for advanced navigation and autonomous flight.
- Integration of machine learning algorithms for predictive control and autonomous decision-making.
- Expansion to multi-channel communication for more complex flight operations and real-time data analytics.
