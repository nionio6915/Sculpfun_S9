Adding endstops / homing switches to Sculpfun S9 Laser Engraver
12/19/2021 This is a continual work in progress. Information may change and be revised without notice. As with anythithing you find  on the interwebs, your mileage may vary and with altering any electronic components, there are inherent risks.
  
Why: repeatabilty and consistency. For my use, I need a consistent and repratable start posistion for my work. I have a stop/fence on the spoils baord and I justify and clamp the work to it. 

If you are just tossing some random thing under the laser and etching pretty pictures, this is prpbably not going to matter to you. 

1. Print the endstop brackets. There are 2 versions for each of the X & Y axis. One is for reprap 3-wire types, and one for miniature and sub-miniature SPDT snap action switches.  
Only the non-LED illuminated Reprap endstop switch will work. See https://reprap.org/wiki/Mechanical_Endstop for descriptions.   
See this page for wiring limit switchs to 2-axis system with grbl.  
https://github.com/gnea/grbl/wiki/Two-Axis-System-Considerations and  
https://github.com/gnea/grbl/wiki/Wiring-Limit-Switches  

2. Mount the brackets. Each bracket requires an M5x8 screw and a 'hammernut' to twist into the extrusion channel.

3. Wire the endstops to the controller. Depending on where you mount the endstops, you will need 1m or 2m of 3-wire cable and connectors.  The DLC board uses 3-pin JST XH connectors.

4. Upload the updated firmware that has the homing function compiled into it. 
Using either LAsergrbl or Lightburn, flash the firmware to the controller. 

5. Configure grbl to recognize the switches and the homing direction. 

From the main grbl configuration page- 
https://github.com/gnea/grbl/wiki/Grbl-v1.1-Configuration

pay particular attention to the secion the invert mask and corresponding table.

https://github.com/gnea/grbl/wiki/Grbl-v1.1-Configuration#2--step-port-invert-mask

These are the stock grbl EEPROM parameters for an S9 with DLC version 1.0 controller, grbl version 1.1f as shipped, and with the updates after flashing the .hex file to 1.1h. 

| Orignal grbl 1.1f settings |   |                | Updated grbl 1.1h settings |         |              |                                 |                                                                                    |
|----------------------------|---|----------------|----------------------------|---------|--------------|---------------------------------|------------------------------------------------------------------------------------|
| Parameter                  |   | Original Value | Updated Value              | Status  | Units        | Descriptions                    | Comment                                                                            |
| $0                         | = | 10             | 10                         |         | microseconds | Step pulse time                 |                                                                                    |
| $1                         | = | 25             | 25                         |         | milliseconds | Step idle delay                 |                                                                                    |
| $2                         | = | 0              | 0                          |         | mask         | Step pulse invert               | https://github.com/gnea/grbl/wiki/Grbl-v1.1-Configuration#2--step-port-invert-mask |
| $3                         | = | 0              | 0                          |         | mask         | Step direction invert           |                                                                                    |
| $4                         | = | 0              | 0                          |         | boolean      | Invert step enable pin          |                                                                                    |
| $5                         | = | 0              | 0                          |         | boolean      | Invert limit pins               |                                                                                    |
| $6                         | = | 0              | 0                          |         | boolean      | Invert probe pin                |                                                                                    |
| $10                        | = | 1              | 1                          |         | mask         | Status report options           |                                                                                    |
| $11                        | = | 0.01           | 0.01                       |         | mm           | Junction deviation              |                                                                                    |
| $12                        | = | 0.002          | 0.002                      |         | mm           | Arc tolerance                   |                                                                                    |
| $13                        | = | 0              | 0                          |         | boolean      | Report in inches                |                                                                                    |
| $20                        | = | 0              | 1                          | Changed | boolean      | Soft limits enable              | Safey feature-imposes the movement limits defined by $130 - $132                   |
| $21                        | = | 0              | 1                          | Changed | boolean      | Hard limits enable              | Safety feature- use the switches to halt motion when triggered.                    |
| $22                        | = | 0              | 1                          | Changed | boolean      | Homing cycle enable             | Homing feature - result is the machine knows precise and knownposition             |
| $23                        | = | 0              | 3                          | Changed | mask         | Homing direction invert         | Binary number to direct homing directions Invert X,Invert Y, Z normal              |
| $24                        | = | 25             | 200                        | Changed | mm/min       | Homing locate feed rate         | Homing slow moves speeds.                                                          |
| $25                        | = | 500            | 3000                       | Changed | mm/min       | Homing search seek rate         | Homing fast moves speeds.                                                          |
| $26                        | = | 250            | 250                        |         | milliseconds | Homing switch debounce delay    |                                                                                    |
| $27                        | = | 1              | 3                          | Changed | mm           | Homing switch pull-off distance | Retract distance to reset switch after homing.                                     |
| $30                        | = | 1000           | 1000                       |         | RPM          | Maximum spindle speed           |                                                                                    |
| $31                        | = | 0              | 0                          |         | RPM          | Minimum spindle speed           |                                                                                    |
| $32                        | = | 1              | 1                          |         | boolean      | Laser-mode enable               |                                                                                    |
| $100                       | = | 80             | 80                         |         | steps/mm     | X-axis travel resolution        |                                                                                    |
| $101                       | = | 80             | 80                         |         | steps/mm     | Y-axis travel resolution        |                                                                                    |
| $102                       | = | 250            | 250                        |         | steps/mm     | Z-axis travel resolution        |                                                                                    |
| $110                       | = | 6000           | 6000                       |         | mm/min       | X-axis maximum rate             |                                                                                    |
| $111                       | = | 6000           | 6000                       |         | mm/min       | Y-axis maximum rate             |                                                                                    |
| $112                       | = | 1000           | 1000                       |         | mm/min       | Z-axis maximum rate             |                                                                                    |
| $120                       | = | 1000           | 1000                       |         | mm/sec^2     | X-axis acceleration             |                                                                                    |
| $121                       | = | 1000           | 1000                       |         | mm/sec^2     | Y-axis acceleration             |                                                                                    |
| $122                       | = | 10             | 1000                       | Changed | mm/sec^2     | Z-axis acceleration             |                                                                                    |
| $130                       | = | 410            | 410                        |         | mm           | X-axis maximum travel           |                                                                                    |
| $131                       | = | 400            | 400                        |         | mm           | Y-axis maximum travel           |                                                                                    |
| $132                       | = | 200            | 200                        |         | mm           | Z-axis maximum travel           |                                                                                    |


This is the invert mask from the grbl firmware. grbl default moves are to the MAX directions on each axis. Any direction change casued by the machine configuration needs to be set in the firmware with one of the following masks by setting the integer value in the table.  

| Setting Value | Mask     | Invert X | Invert Y | Invert Z |
|---------------|----------|----------|----------|----------|
| 0             | 00000000 | N        | N        | N        |
| 1             | 00000001 | Y        | N        | N        |
| 2             | 00000010 | N        | Y        | N        |
| 3             | 00000011 | Y        | Y        | N        |
| 4             | 00000100 | N        | N        | Y        |
| 5             | 00000101 | Y        | N        | Y        |
| 6             | 00000110 | N        | Y        | Y        |
| 7             | 00000111 | Y        | Y        | Y        |


The hooming switches in the Sculpfun instructions were are Xmin & Ymin, so the configration mask was "3"- 
  X Invert  Y Invert   Z Normal
Homing switches in this deployment are located at Xmax & Ymin so we must adjust the values accordindly to "2"
  X Normal  Y Invert  Z Normal 



6. Websites with good grbl info

https://github.com/gnea/grbl/wiki

https://diymachining.com/grbl-update/

https://lightburnsoftware.github.io/NewDocs/CommonGrblSetups.html