# How to Display Color and Character on LCD Using Microcontroller board
This tutorial shows you how to display string, color and dynamic value on LCD with STM32F4 development board from ground up. It will also show you how to read the technical datasheet of LCD.<br />

# What is the Goal and what you need to have <br />
Let's first take a look what are we going to achieve after this tutorial.<br />
<p align="center">
<img src="/Image/Goal.gif" height="80%" width="80%"> 
</p>  
<br />

### Hardware and Documents you need to have <br />

**1. Hardware**
 * STM32F411RE Nucleo 64
 * ST7735s 1.8" TFT LCD 128 X 160 (SKU:MAR1801)
 * Jumper wires (Male-Male, Male-Female, Female-Female)
 * Potentiometer
 * USB Cable-A to Mini-B
 
**2. Documents**
 * STM32F411 User Manual [link](https://www.st.com/resource/en/datasheet/stm32f411re.pdf)
 * Sitronix ST7735s Datasheet [link](http://www.lcdwiki.com/res/MAR1801/ST7735S_V1.1_20111121.pdf)
 
You are ready to go!! <br />

# Basic Knowledge <br />

### STM32-Nucleo board <br />
A series of Youtube tutorial on STM32-Nucleo is a good start if you never use this. : [Youtube](https://youtu.be/FAv0LO5E2TM)   (Include CubeMX and Keil 5) <br />

You can think of it as a tiny and simplify PC without monitor(output), keyboard(input), and mouse(input). The LCD acts as output and the Potentiometer acts as input in our tutorial. There is a big problem, how can this tiny PC communicate with the outside world? Some use wired or wireless. <br />
| Wired/Wireless  | Example           |  
| -------------   |:-------------:|  
| Wired           | SPI, I2C, UART, Ethernet |  
| Wireless        | Wi-Fi, Bluetooth, Cellular      |   

In this tutorial, we will use SPI to communicate with the output device ~ LCD.

### ST7735s LCD <br />





 
