# a07g-exploring-the-CLI

* Team Number: 29
* Team Name: Team Straw Hats
* Team Members: Siddharth Ramanathan & Komalika Gaikwad
* GitHub Repository URL: https://github.com/ese5160/final-project-a07g-a14g-t29-straw-hats.git
* Description of test hardware: (development boards, sensors, actuators, laptop + OS, etc) Macbook Air M1, Dell G15

## 1. Software Architecture

### 1.
*Hardware Requirements:*

### Power Management
| **HRS ID** | **Requirement** |
| --- | --- |
| HRS-01 | The power ON/OFF button shall control when the circuit should be powered. |
| HRS-02 | Power rail 1 should have 3.3V, which is stepped down from the input 5V using a buck convertor. |
| HRS-03 | Power rail 2 should have 5V, which is maintained by a buck boost convertor.|

### Sensor setup 
| **HRS ID** | **Requirement** |
| --- | --- |
| HRS-04 | MICS-5524 sensor shall measure with a ±5% accuracy. |
| HRS-05 | The sensor shall measure the gas in parts per millions based on the change in output voltage across the load. |


### Web Interface

| **HRS ID** | **Requirement** |
| --- | --- |
| HRS-06 | The exhaust fan runs at 50% of its speed at level 2 toxicity.  |
| HRS-07 | The exhaust fan runs at 100% of its speed at level 3 toxicity. |
| HRS-08 | The ON/OFF button has a green LED in it, which lights up when power flows to the circuit. |
| HRS-09 | Buzzer turns On at level 3 toxicity. |
| HRS-10 | Buzzer and exhaust fan shall turn OFF when CO levels go low. |

*Software Requirements:*

#### Sensing 

| **SRS ID** | **Requirement** |
| --- | --- |
| SRS-01 | The gas sensor shall measure CO levels and communicate with the SAMW25 MCU over ADC. |
| SRS-02 | Sensor data shall be read every 500 milliseconds ±10 milliseconds.|
| SRS-03 | The sensor shall aqquire new sample data every 5 seconds. |
| SRS-04 | System shall register different levels of CO that get classified as Level 1, Level 2, and level 3 where the level 3 is highest in terms of toxicity. |
| SRS-05 | Sensor shall give a resistance value which can later be converted to gas part per million using a relation between Ideal resistance and observed resistance.  |

#### Display 
| **SRS ID** | **Requirement** |
| --- | --- |
| SRS-06 | The system shall display on the OLED, the CO levels in the units of parts per million and the room status based on the level of toxicity. |
| SRS-07 | The OLED display will be connected via i2c to the MCU. |
| SRS-08  | The system shall display an Start text "Hello <user_name>!" when turned on. |
| SRS-09  | When the first reading is registered by the MCU, the Display screen should display - "Carbon Monoxide Level __ PPM " |
| SRS-10  | On the second line of the display the text printed out shall show the Current Toxicity level text.  |
| SRS-11  | When Level 1 toxicity is observed, the displayed text shall be "Toxicity Level- 1, You are safe." |
| SRS-12 | When Level 1 toxicity is observed, the displayed text shall be "Toxicity Level- 2, Exhaust is turned on."  |
| SRS-13 | When Level 1 toxicity is observed, the displayed text shall be "Toxicity Level- 3, Danger! Please evacuate."  |
| SRS-14 | The OLED display shall be refreshed every 5 seconds with new CO readings Observed from the sensor.  |

### Operations
| **SRS ID** | **Requirement** |
| --- | --- |
| SRS-15 | The system shall have different modes of operations based on the toxicity level that are classified based on the CO detected ppm. |
| SRS-16 | At level 1 toxicity, i.e negligible CO presence, the system shall have the Exhaust fan off. |
| SRS-17 | At level 2 toxicity, i.e CO presence detected, the system shall have the Exhaust fan on with 50% of its speed |
| SRS-18 | At level 3 toxicity, i.e negligible CO presence, the system shall have the Exhaust fan on with 100% of its speed.|
| SRS-19 | The speed of the fan shall be controlled by PWM mode. |


### Indicators 
| **SRS ID** | **Requirement** |
| --- | --- |
| SRS-20 | The buzzer shall go off when the toxicity level 3 is flagged. |
| SRS-21 | The Red LED shall turn ON when toxicity level 3 is flagged. |


### Web Interface

| **SRS ID** | **Requirement** |
| --- | --- |
| SRS-22 | The system shall support communication between the MCU and a web interface via WiFi.  |
| SRS-23 | The web interface shall display the CO levels in the units of parts per million and the room status based on the level of toxicity |
| SRS-24 | The display on the Node red shall refresh every second after we get  the input reading. |
| SRS-25 | The Display text on the Node red shall include data from all the three units that is transfered over the MQTT protocol, including . |

### 2. Block Diagram for different tasks

<div align=center>
<img src="/Images/TaskBD.drawio.png" width="600">  
</div>

### 3. State Machine Diagram

<div align=center>
<img src="/Images/StateDiagram.drawio.png" width="600">  
</div>

## 2. Software Architecture

**1. What does “InitializeSerialConsole()” do? In said function, what is “cbufRx” and “cbufTx”? What type of data structure is it?** 
* InitializeSearialConsole() sets up the MCU to communicate using UART. It sets up circular buffters to store data when recieving and sending messages. 
* cbuffRx stores recieved data
* cbufTx stores data that is to be transmitted. 
* Both cubffRx and cbuffTx are circular buffters. 

**2. How are “cbufRx” and “cbufTx” initialized? Where is the library that defines them (please list the *C file they come from).** 
* This is how cbuffRx and cbuffTx are initialized. 
  
  cbufRx = circular_buf_init((uint8_t *)rxCharacterBuffer, RX_BUFFER_SIZE);
  cbufTx = circular_buf_init((uint8_t *)txCharacterBuffer, TX_BUFFER_SIZE);


* they are defined in CLI Starter [circular_buffer](<CLI Starter Code/src/SerialConsole/circular_buffer.c>)
  

**3. Where are the character arrays where the RX and TX characters are being stored at the end? Please mention their name and size. Tip: Please note cBufRx and cBufTx are structures.** 



char rxCharacterBuffer[RX_BUFFER_SIZE];

char txCharacterBuffer[TX_BUFFER_SIZE];

#define RX_BUFFER_SIZE 512 

#define TX_BUFFER_SIZE 512
name - rxCharacterBuffer, txCharacterBuffer
size- 512 bytesfor both.

Being stored in latestRx and latestTx variable. 

usart_read_buffer_job(&usart_instance, (uint8_t *)&latestRx, 1); // Kicks off constant reading of characters

**4. Where are the interrupts for UART character received and UART character sent defined?**

* interrupts for UART character received and UART character sent are defined in usart_write_callback () and usart_read_callback(). 
* The interrupt handler is part of the Microchip ASF library, which manages low-level USART operations. The callback usart_read_callback() is linked to this driver and is executed automatically when a receive interrupt occurs.

**5. What are the callback functions that are called when:**
**A character is received? (RX)**
**A character has been sent? (TX)**
void usart_write_callback(struct usart_module *const usart_module); // Callback for when we finish writing characters to UART
void usart_read_callback(struct usart_module *const usart_module); // Callback for when we finis reading characters from UART


**6. Explain what is being done on each of these two callbacks and how they relate to the cbufRx and cbufTx buffers.**

**For receiving**
* This function is called when a new character is received from UART.
* It stores the recieved character into cbuffRx circular buffer. 
* The recieved character latestRx is added to the circular buffer. 
* usart_read_buffer_job() this function is restarted to keep recieving more characters.

**For sending**
* This function is called when a character has been sent.
* It fetches the next character from cbuffTx and sends it.
* If cbufTx has more data, it takes the next character (latestTx) and sends it.
* Calls usart_write_buffer_job() to send the next character.


**7. Draw a diagram that explains the program flow for UART receive – starting with the user typing a character and ending with how that characters ends up in the circular buffer “cbufRx”. Please make reference to specific functions in the starter code.**
<div align=center>
<img src="/Images/P_7.jpg" width="600">  
</div>

**8. Draw a diagram that explains the program flow for the UART transmission – starting from a string added by the program to the circular buffer “cbufTx” and ending on characters being shown on the screen of a PC (On Teraterm, for example). Please make reference to specific functions in the starter code.**

<div align=center>
<img src="/Images/P_8.jpg" width="600">  
</div>

**9. What is done on the function “startStasks()” in main.c? How many threads are started?**

* startStasks() logs heap memory before creating tasks, it creates Command line interface task & Logs heap memory after task creation.
* vCommandConsoleTask is the only task that is created, but FreeRTOS itself starts Idle task and timer Task when this task is called.

## 3. Debug Logger Module  


## 4. Wiretap the convo!
### 1. Questions 
#### 1. What nets must you attach the logic analyzer to? (Check how the firmware sets up the UART in SerialConsole.c!)

The Pins connected to the logic analyser are:
- Tx: PB 10 in Sercom4 pad 2
- Rx: PB 11 in Sercom4 pad 3

#### 2. Where on the circuit board can you attach / solder to?

The logic analyser should be connected to the pin headers or test points.

#### 3. What are critical settings for the logic analyzer?

- Baud rate: 115200
- Channel: Serial Async

### 2. 
Picture of the logic analyzer connections-

<div align=center>
<img src="/Images/Salea.jpg" width="600">  
</div>


### 3. SS 
<div align=center>
<img src="/Images/Decoded_text.jpg" width="600">  
</div>


### 4. SAl
[.sal](Part4.sal)

## 5.

## 6. 
