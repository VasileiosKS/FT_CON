# PCB Design: Version 0

PCB Schematics             |  PCB 3D Rendering         | Assembled PCB
:-------------------------:|:-------------------------:|:-------------------------:
![](https://github.com/VasileiosKS/FT_CON/blob/main/images/FT_RP2040_V0_Schematics.PNG)  |  ![](https://github.com/VasileiosKS/FT_CON/blob/main/images/FT_RP2040_V0_PCB_CAD.PNG) | <img src="https://github.com/VasileiosKS/FT_CON/blob/main/images/FT_RP2040_V0_PCB.jpg" alt="Alt Text" style="width:80%; height:auto;"> 

# PCB Design: Version 1

PCB Schematics             |  PCB 3D Rendering         |
:-------------------------:|:-------------------------:|
![](https://github.com/VasileiosKS/FT_CON/blob/main/images/FT_RP2040_V1_Schematics.PNG)  |  ![](https://github.com/VasileiosKS/FT_CON/blob/main/images/FT_RP2040_V1_PCB_CAD.PNG) | 

# PCB Design: Version 2 

Rather than using through-hole components that consume excessive space, this final iteration adopts surface-mount components to achieve a more compact design. Also, i recently aquired a hot air station making it a bit easier to work with those tiny components.

PCB Schematics             |  PCB 3D Rendering         | Assembled PCB
:-------------------------:|:-------------------------:|:-------------------------:
![](https://github.com/VasileiosKS/FT_CON/blob/main/images/FT_RP2040_V2_Schematics.PNG)  |  ![](https://github.com/VasileiosKS/FT_CON/blob/main/images/FT_RP2040_V2_PCB_CAD.PNG) | 
![](https://github.com/VasileiosKS/FT_CON/blob/main/images/FT_RP2040_V2_PCB.png) 


# Overview:
Although this was the first PCB i designed and manufactured, its functional though not without its challenges. Most of the issues encountered are detailed below.

# Issues enountered with V0
  - Power: No onboard voltage regulators were included, limiting the power that can be supplied on the 5V and 3.3V rails.
  - Footprint Error: An incorrect footprint was used for the TB6612FNG motor driver.
  - Connector & GPIOs: Not enough GPIOs were broken out, as a result permanent wires had to be soldered to the Pico.
    Additionaly the FT connector pads are spaced too tight.  
  - PCB Size: The PCB dimensions far exceeded the allowed dimensions, preventing it from fitting into its case.
  - I2C Issues: Certain pins belonging to non I2C devices were tied to I2C0 bus. Also could not get the AS5600 to work on I2C1 bus.
# Addresing issues of V0 in V1
To address the shortcomings of the prototype Version 0 (V0), several modifications were implemented in Version 1 (V1).
- Power Managment: The 5V and 3.3V power railes were reinforced by adding two AMS1117 LDO voltage regulators. Although these are considered obsolute with lower  effieciency compared to newer regulators, they were used due to existing stock.
- Motor Driver: The footprint error was corrected by replacing the TB6612FNG iwth the DRV8333. Decreased the number of module used by one, as well as the total amount of GPIOs needed. 
- Layout: Tight spacing issues were resolved. The number of broken-out GPIOs remains the same but changes to the pin assignments were made.
- Size Comparison:
  - V0 Dimensions: 68.453 mm x 57.150 mm aprox 3912.09 mm^2.
  - V1 Dimensions: 73.500 mm x 58.000 mm aprox 42563 mm^2.
  - Result in an area increase aprox 8.97 %.
  - The extra space was used to resolve previous clearance issues, while still staying within the safe margins of the 75 mm x 60 mm enclosure.
# Changes made in V2
 - Microcontroller: Migrated from RP2040 W to ESP32.
 - Component: From through-hole to surface mounted components.
 - Size: Reduced pcb size.
# Goals for V2 iteration
With this revision, i had a few goals in mind.
  1. Work on my soldering skills, especially get with surface mounted components (SMD).
  2. Schematic Design: Remember how to create schematics diagrams and use the editors.
  3. Custom Footprints: Design own footprints.
  4. ESP32: Develop a custom pcb with an ESP32 microcontroller.
  5. PCB Routing: Route the entire PCB manually. In previous revisions, i had heavily relied on the auto-route function.
  6. PCB Manufacturing: Order the PCB with a stainless steel stencil to practice applying solder paste.
# Issues encountered with V2
After visually inspecting the manufactured pcb the followinmg isssues were found:
 - Component Interference: Resistors R1 & R2  were incorrectly placed and routed on the top layer instead at the bottom. As a result neither the resistors nor the USB Type-C connector can be soldered at the same time. 
 - Clearence:
   - C7 was placed close to both DRV8833 motor driver ICs, complicating the soldering process.
   - C16 was also placed too close to the UART & I2C headers.
 - Prefixes:
   - The prefix for the 5V regulator was wrongly stated as U4.
   - Multiple instances of clashing silkscreen labels were found between the top and bottom silklayers (C11, C12, C16 ,R1 ,USB-TYPE C)
 
 Status: All routing and placement issues have been corrected in the latest design files.
 Most issues could have been avoided had i been more carefull.
 
