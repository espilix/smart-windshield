# Smart Windshield System: Final Documentation

## 1. **Actuators**

| Actuator                      | Description & Function                                                                                   | Typical Control Method           |
|-------------------------------|---------------------------------------------------------------------------------------------------------|----------------------------------|
| **Electrochromic Tilt Film**  | Dynamically adjusts windshield tint to reduce glare and improve visibility.                             | Relay or PWM via GPIO/FPGA       |
| **ITO Heating Film**          | Transparent conductive layer heats windshield for defogging/defrosting.                                 | Relay or MOSFET via GPIO/FPGA    |
| **Ultrasonic Transducer Array** | Emits ultrasonic vibrations to shake off water, dirt, and debris for wiperless cleaning.               | High-frequency driver via FPGA   |
| **Compressed Air Pump**       | Delivers high-pressure air pulses to clear the windshield of debris, water, or snow.                    | Relay or MOSFET via GPIO/FPGA    |
| **Cooling Fan**               | Maintains safe operating temperature for electronics and power devices.                                 | Relay or PWM via GPIO/FPGA       |

---

## 2. **Sensors**

| Sensor                | Description & Function                                                                                 | Typical Interface    |
|-----------------------|-------------------------------------------------------------------------------------------------------|---------------------|
| **Weather Sensor**    | Measures temperature, humidity, and possibly barometric pressure for environmental awareness.         | I2C/SPI             |
| **Ultrasonic Sensor** | Detects proximity, rain, or measures water film thickness on the windshield.                          | GPIO/Analog/I2C     |
| **Light Sensor**      | Monitors ambient light for automatic tint adjustment and AR HUD brightness control.                   | Analog/I2C          |
| **Night Vision Sensor** | Captures low-light images for AI-based object detection and AR HUD overlays.                        | CSI/USB             |
| **CV Camera**         | Provides video input for computer vision algorithms (rain/fog/debris detection, driver monitoring).   | CSI/USB             |

---

## 3. **Display**

| Display Component     | Description & Function                                                                                 | Typical Interface    |
|-----------------------|-------------------------------------------------------------------------------------------------------|---------------------|
| **AR HUD (Augmented Reality Head-Up Display)** | Projects navigation, alerts, and enhanced vision overlays directly onto the windshield. | HDMI/MIPI-DSI/Custom|

---

## 4. **Power Controller**

| Power Rail            | Description & Function                                                                                 | Typical Components  |
|-----------------------|-------------------------------------------------------------------------------------------------------|---------------------|
| **5V Power Rail**     | Supplies power to logic circuits, sensors, relays, and some actuators.                                | DC-DC Converter, LDO|
| **12V Power Rail**    | Supplies power to high-current actuators (air pump, ITO heater, electrochromic film, fans).           | DC-DC Converter     |

- **Power Distribution:**  
  - Fused and protected rails for each subsystem.
  - Relay or MOSFET switching for high-current loads.
  - Shared ground for all components.

---

## 5. **Processor**

| Processor             | Description & Function                                                                                 | Typical Tasks       |
|-----------------------|-------------------------------------------------------------------------------------------------------|---------------------|
| **FPGA (e.g., Xilinx Zynq UltraScale+)** | Central controller for all real-time tasks, sensor fusion, AI inference acceleration, and actuator control. | - Sensor data acquisition<br>- AI/ML inference (Vitis-AI DPU)<br>- Real-time control (Cortex-R5/PL)<br>- Display driving (AR HUD)<br>- Safety monitoring |

- **Partitioning:**  
  - **ARM Cortex-A53:** Runs Linux (PetaLinux), high-level logic, AI, user interface.
  - **ARM Cortex-R5:** Runs RTOS/bare-metal for safety-critical, real-time control.
  - **Programmable Logic (PL):** Hardware acceleration for AI, sensor fusion, and high-speed I/O.

---

## **Block Diagram (Textual)**

```
[ Sensors ]         [ Actuators ]
   |                    |
   |                    |
   +----[ FPGA SoC ]----+
   |        |           |
   |     [ Power ]   [ AR HUD ]
   |     Controller   Display
   |
[ 5V/12V Power Supply ]
```

---

## **Wiring & Integration Notes**

- All high-current actuators are switched via relays or MOSFETs controlled by FPGA GPIO.
- Sensors connect to FPGA via I2C, SPI, analog, or CSI/USB (for cameras).
- Power controller ensures safe, fused distribution to all subsystems.
- AR HUD receives video signal from FPGA or ARM processor.
- Cooling fan is temperature-controlled by FPGA or ARM logic.

---

## **Safety & Reliability**

- Use opto-isolated relays for all high-voltage/current switching.
- Add flyback diodes for inductive loads (air pump, fans).
- Implement watchdog timers and fail-safe logic in FPGA/RTOS.
- Label all wiring and use color codes as per documentation.

---

## **Summary Table**

| Subsystem      | Components Included                                                                 |
|----------------|-------------------------------------------------------------------------------------|
| Actuators      | Electrochromic tilt, ITO heater, ultrasonic array, air pump, cooling fan            |
| Sensors        | Weather, ultrasonic, light, night vision, CV camera                                 |
| Display        | AR HUD                                                                              |
| Power          | 5V/12V rails, relays, MOSFETs, fuses                                                |
| Processor      | FPGA SoC (Zynq UltraScale+), ARM Cortex-A53/R5, programmable logic                  |
