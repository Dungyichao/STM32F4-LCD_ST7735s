# How to Display Color and Character on LCD Using Microcontroller board
This tutorial shows you how to display string, color and dynamic value on LCD with STM32-Nucleo development board from ground up. It will also show you how to read the technical datasheet of LCD.<br />

# 1. What is the Goal and what you need to have <br />
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
 * Potentiometer (optional, only affect the dynamic value displaying)
 * USB Cable-A to Mini-B
 
**2. Documents**
 * STM32F411 User Manual [link](https://www.st.com/resource/en/datasheet/stm32f411re.pdf)
 * Sitronix ST7735s Datasheet [link](http://www.lcdwiki.com/res/MAR1801/ST7735S_V1.1_20111121.pdf)
 
You are ready to go!! <br />

# 2. Basic Knowledge <br />

### 2.1 &nbsp; STM32-Nucleo board <br />
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


### 2.2 &nbsp; ST7735s LCD <br />
We want to display some string or color on the specific location of the LCD. We can achieve this by sending data to the specific address of RAM (Memory) in the LCD board. This is call Memory to Display Address Mapping. Take the image below for example. We want to make the top left corner of the LCD display a yellow square. First, you need to let the LCD knows at where of the RAM are you going to write the data to (using X_start, X_end, Y_start, Y_end value). Then you can start writing the data (color information) to those address of the RAM. Then, the specified portion of LCD will change it's color according to the value you wirte into the address in the RAM. (Please refer to the ST7735s datasheet page 59 ~ 72) <br />
<p align="center">
<img src="/Image/lcd_ram.png" height="70%" width="70%">
</p>

Let's take a look the back side of the LCD board in the following image. You only need to focus on the left side.
<p align="center">
<img src="/Image/lcd_back.jpg" height="50%" width="50%">
</p>

The following table shows each pinout of the LCD board and its function. <br />
<p align="center">
<table>
    <thead>
        <tr>
            <th align="center">Pinout on LCD  </th>
            <th align="center">Function</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td align="center">VCC</td>
            <td align="left">Power 3.3V ~ 5V</td>
        </tr>
        <tr>
            <td align="center">GND</td>
            <td align="left">Connect to ground</td>
        </tr>
        <tr>
            <td align="center">NC</td>
            <td align="left">No need to connect</td>
        </tr>
        <tr>
            <td align="center">CLK</td>
            <td align="left">Clock. Also call SCK </td>
        </tr>
        <tr>
            <td align="center">SDA</td>
            <td align="left">MOSI for receiving data from the master</td>
        </tr>
        <tr>
            <td align="center">RS</td>
            <td align="left">Data/Command selection. (some LCD board calls it DC or D/C). <br /> When applying high voltage meaning sending data while <br /> applying low voltage meaning sending command.</td>
        </tr>
        <tr>
            <td align="center">RST</td>
            <td align="left">Reset (reset at low level)</td>
        </tr>
        <tr>
            <td align="center">CS</td>
            <td align="left">Chip selection (some LCD calls it SS)</td>
        </tr>
    </tbody>
</table>
</p>

### 2.3 &nbsp; Communication between STM32-Nucleo board and ST7735s LCD <br />
We will talk about how to connect these two devices by wires later. Let's see the protocol when communicating to the LCD board. First look at the graphic explanation below.<br />  
<p align="center">
<img src="/Image/st_lcd_comm.bmp" height="100%" width="100%">
</p>
<br /> 
<p align="center">
<table>
    <thead>
        <tr>
            <th align="center">Connection Pin</th>
            <th align="center">Explanation</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td align="center">CS</td>
         <td align="left">Only at the <b>falling edge</b> on CS (voltage become low) enables the SPI and indicates the start of data or command transmission. When the CS is high (voltage is high), the clock is ignored. </td>
        </tr>
        <tr>
            <td align="center">Data or Command</td>
            <td align="left"><b>Data:</b> You use the high voltage from the GPIO pin to tell the LCD board that you are transmitting the data. <br />  <b>Command:</b> You use the low voltage from the GPIO pin to tell the LCD board that you are transmitting the command. (Please refer to the ST7735s datasheet page 36 ~ 51)</td>
        </tr>
        <tr>
            <td align="center">MOSI</td>
            <td align="left">While sending a command, it's not necessary to send data. How many data right after the command is defined by the command. (Please refer to the ST7735s datasheet page 104 ~ 186) <br /> In our code, our SPI transmit 8-bit data at a time, however, the color in our case is defined in 16-bit, so you need to transfer the first 8 bit of the color and then the rest 8 bit.  (Please refer to the ST7735s datasheet page 44 ~ 58)</td>
        </tr>
        <tr>
            <td align="center">Clock</td>
            <td align="left">MOSI data is sampled at the rising edge of clock </td>
        </tr>
    </tbody>
</table>
</p>

<br />

# 3. Configure Pins and Code
In the following, we will first use CubeMX ([link](https://www.st.com/en/development-tools/stm32cubemx.html)) to configure SPI and ADC (optional) on the STM32-Nucleo board and then generate code for keil µVision IDE v5 ([link](http://www2.keil.com/mdk5/uvision/)). Secondly, copy paste the code from the program we provided (with some explanation of the code). Finally, we will test the code on the hardware. <br />    

### 3.1 &nbsp; Configure Pins on CubeMX
Click <b>File</b> --> <b>New Project</b> --> In the search, key in your board model (STM32f411RE in our case) --> Click on the NUCLEO-F411RE --> Double click the item --> Click <b>Yes</b> to initialize all peripherals with their default Mode.  <br />    
<p align="center">
<img src="/Image/cubemx/CubeMX_pin.JPG" height="100%" width="100%">
</p>

 
