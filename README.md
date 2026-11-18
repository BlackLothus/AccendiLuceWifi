# AccendiLuceWifi (Lazyometro)

**AccendiLuceWifi** is a personal IoT project born from the desire to automate the main lights in my room. 

I am well aware that there are already commercial smart switches available for a few euros—certainly less bulky and more refined than mine. However, I wanted to challenge myself, have fun building something from scratch, and apply my engineering skills. Therefore, I stubbornly decided to build my own.

The project is tailored to the physical wall switch in my room. While there are thousands of different switch models out there, the core logic remains the same: interacting mechanically with a physical button. Depending on your wall switch, you might need more torque, a longer servo arm, or a differently sized 3D-printed chassis, but I am confident that my code and mechanical logic can provide a solid foundation for anyone wanting to build their own custom "AccendiLuceWifi".

The firmware is developed in **MicroPython** (using Thonny as the main IDE) and is designed to handle hardware limitations gracefully.

## Hardware & Bill of Materials (BOM)

The project can be built in two different configurations: **Standalone** (battery-powered) or **Mains-powered**. 

### Core Components (Required for both setups)
- **ESP32-WROOM WiFi** (Any WiFi-enabled microcontroller will do).
- **MG90S Servo Motor** (Used to push the switch. If your switch is stiff, choose a servo with metal gears like this one for more torque).
- **28BYJ-48 Stepper Motor** with **ULN2003 Driver** (Used to move the servo sled).
- **Tactile Push Button / Microswitch** (Used as a limit switch to establish the absolute "zero" position during the homing cycle).
- **1000µF 16V Capacitor** (Crucial: placed across the power rails to absorb the servo's current spikes and prevent the ESP32 from resetting due to brownouts).
- **Breadboard and Jumper Wires** (At least for prototyping).

### Standalone Configuration (Battery)
- **5V Power Bank** (Any standard, lightweight power bank will last for days).
- **USB Type-C Cable**.
- **TP4056 Module** (*Note: I used this creatively just as a Type-C breakout board to avoid cutting my USB cable, bypassing its charging ICs and taking power directly from the +/- pads. A standard USB-C breakout board is recommended for future builds*).

### Mains-Powered Configuration
- **Power Supply Module** (e.g., a breadboard power supply).
- **6V to 9V Switching Power Supply**.

## Project Features & Architecture

### Hardware Constraints & Strict Sequential Movement
To compensate for the power bank limitations and avoid voltage drops, the firmware enforces a **Strict Sequential Movement**: The motors never move simultaneously. The system performs a Homing cycle, moves the stepper, completely turns off the stepper coils, activates the servo to press the switch, and returns to rest.

### User Interface & Cloud Communication
For user interaction, I initially considered running a local Node.js server, but that would require keeping my PC turned on at all times. Instead, I opted for a serverless cloud architecture. 

I set up a cloud MQTT broker using **EMQX** and wrote a lightweight HTML interface that communicates directly with the cloud broker via WebSockets to send commands to the ESP32. Finally, I hosted this HTML file on **Netlify**. This allows me to control the device from anywhere in the world using just a URL, without needing to maintain local infrastructure or carry the HTML file around.