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

You can think of it as a tiny and simplify PC without monitor(output), keyboard(input), and mouse(input). The LCD acts as output and the Potentiometer acts as input in our tutorial. There is a big problem, how can this tiny PC communicate with the outside world (such as monitor or keyboard)? Some use wired or wireless. <br />

<p align="center">
<table>
    <thead>
        <tr>
            <th align="center">Wired/Wireless</th>
            <th align="center">Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td align="center">Wired</td>
            <td align="center">SPI, I2C, UART, Ethernet</td>
        </tr>
        <tr>
            <td align="center">Wireless</td>
            <td align="center">Wi-Fi, Bluetooth, Cellular</td>
        </tr>
    </tbody>
</table>
</p>


In this tutorial, we will use SPI (Serial Peripheral Interface) to communicate with the output device ~ LCD. We call our Nucleo board as master and the LCD as slave because we are sending order and command to the LCD. Sparkfun has a great tutorial website about SPI ([link](https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/all)). The image in the following shows the relationship and connection between master and slave.(image from Sparkfun with some modification to fit our case) <br /> 

<p align="center">
<img src="/Image/SPI.JPG" height="60%" width="60%">  
</p>
Some basic term of SPI and their function are shown in the below table. <br />

| Terminology  | Function   
| -------------|------------- 
| MOSI         | Master out Slave in. Master sends command or data to Slave.  
| MISO         | Master in Slave out. Slave sends command or data to Master. <br /> We don't use this in our case. We don't need to get  data from <br /> the LCD. (If you use touchscreen, you would need this function)
| SS (CS)      | Slave select (or Chip select). If you have multiple slaves <br /> connectes to one master, you can use SS to communicate with <br /> the specified slave. Eventhough we only have one slave (LCD), <br /> we still need to use this Slave select to let the LCD knows <br /> we want to talk to him now.
| SCK (CLK)    | Clock. Because SPI is synchronous, it requires clock for sampling.    


### ST7735s LCD <br />
We want to display some string or color on the specific location of the LCD. We can achieve this by sending data to the specific address of RAM (Memory) in the LCD board. This is call Memory to Display Address Mapping. Take the image below for example. We want to make the top left corner of the LCD display a yellow square. First, you need to let the LCD knows at where of the RAM are you going to write the data to (using X_start, X_end, Y_start, Y_end value). Then you can start writing the data (color information) to those address of the RAM. (Please refer to the ST7735s datasheet page 59 ~ 66) <br />
<p align="center">
<img src="/Image/lcd_ram.png" height="70%" width="70%">
</p>

### Communication between STM32-Nucleo board and ST7735s LCD <br />

 
