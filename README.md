# How to Display Color and Character on LCD Using Microcontroller board
This tutorial shows you how to display string, color and dynamic value on LCD with STM32-Nucleo development board from ground up. It will also show you how to read the technical datasheet of LCD. If you are interested in more advance topic of RTOS from scratch, please go to  [Real-Time Operating Systems](https://github.com/Dungyichao/PeriodicScheduler_Semaphore) tutorial. <br />

1. [What is the Goal and what you need to have](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#1-what-is-the-goal-and-what-you-need-to-have-)
	* 1.1 [Hardware](https://github.com/Dungyichao/STM32F4-LCD_ST7735s/blob/master/README.md#hardware-and-documents-you-need-to-have-)
	* 1.2 [Documents](https://github.com/Dungyichao/STM32F4-LCD_ST7735s/blob/master/README.md#hardware-and-documents-you-need-to-have-)
2. [Basic Knowledge](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#2-basic-knowledge-)
	* 2.1 [STM32-Nucleo board](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#21--stm32-nucleo-board-)
	* 2.2 [ST7735s LCD](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#22--st7735s-lcd-)
	* 2.3 [Communication between STM32-Nucleo board and ST7735s LCD](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#23--communication-between-stm32-nucleo-board-and-st7735s-lcd-)
3. [Configure Pins and Code](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#3-configure-pins-and-code)
	* 3.1 [Configure Pins on CubeMX](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#3-configure-pins-and-code)
	* 3.2 [Start Coding](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#32--start-coding)
4. [Connect Hardware and Download the Code to the Nucleo-64](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#4-connect-hardware-and-download-the-code-to-the-nucleo-64)
	* 4.1 [Connect hardware](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#41--connect-hardware)
	* 4.2 [Download code to the Nucleo-64](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#42--download-code-to-the-nucleo-64)
	* 4.3 [More code and download code to the Nucleo-64](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#43--more-code-and-download-code-to-the-nucleo-64)
5. [Let's talk about the code](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#5-lets-talk-about-the-code-)
	* 5.1 [Initialization](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#51--initialization)
	* 5.2 [Sending data (color)](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#52--sending-data-color)
6. [Reference](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#6-reference-)
7. [The End](https://github.com/Dungyichao/STM32F4-LCD_ST7735s#7-the-end-)


# 1. What is the Goal and what you need to have <br />
Let's first take a look what are we going to achieve after this tutorial.<br />
<p align="center">
<img src="/Image/Goal.gif" height="80%" width="80%"> 
</p>  
<br />

### Hardware and Documents you need to have <br />

**1.1 Hardware**
 * STM32F411RE Nucleo 64
 * ST7735s 1.8" TFT LCD 128 X 160 (SKU:MAR1801)
 * Jumper wires (Male-Male, Male-Female, Female-Female)
 * Potentiometer (optional, only affect the dynamic value displaying)
 * USB Cable-A to Mini-B
 * Breadboard
 
**1.2 Documents**
 * STM32F411 User Manual [link](https://www.st.com/resource/en/datasheet/stm32f411re.pdf)
 * Sitronix ST7735s Datasheet [link](http://www.lcdwiki.com/res/MAR1801/ST7735S_V1.1_20111121.pdf)
 
You are ready to go!! <br />

# 2. Basic Knowledge <br />

### 2.1 &nbsp; STM32-Nucleo board <br />
<p align="center">
<img src="/Image/stm32f411re_board.JPG" height="70%" width="70%">  
</p>

A series of Youtube tutorial on STM32-Nucleo is a good start if you never use this [link](https://www.youtube.com/watch?v=FAv0LO5E2TM)

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
We want to display some string or color on the specific location of the LCD. We can achieve this by sending data to the specific address of RAM (Memory) in the LCD board. This is call <b>Memory to Display Address Mapping</b>. Take the image below for example. We want to make the top left corner of the LCD display a yellow square. First, you need to let the LCD knows at where of the RAM are you going to write the data to (using X_start, X_end, Y_start, Y_end value). Then you can start writing the data (color information) to those address of the RAM. Then, the specified portion of LCD will change it's color according to the value you wirte into the address in the RAM. (Please refer to the ST7735s datasheet page 59 ~ 72) <br />
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
In the following, we will first use CubeMX ([link](https://www.st.com/en/development-tools/stm32cubemx.html)) to configure SPI and ADC (optional) on the STM32-Nucleo board and then generate code for keil µVision IDE v5 ([link](http://www2.keil.com/mdk5/uvision/)). Secondly, copy paste the code from the program we provided. Finally, we will test the code on the hardware. <br />    

### 3.1 &nbsp; Configure Pins on CubeMX
Click <b>File</b> --> <b>New Project</b> --> In the search, key in your board model (STM32f411RE in our case) --> Click on the NUCLEO-F411RE --> Double click the item --> Click <b>Yes</b> to initialize all peripherals with their default Mode. --> Clear all Pinout. <br />    
<br />   

<p align="center">
<table>
    <thead>
        <tr>
            <th align="center">Categories</th>
            <th align="center">Configure</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td align="center">SPI</td>
         <td align="left">On the left panel, click <b>Connectivity</b> --> SPI1 --> Select Mode : Transmit Only Master --> Prescaler(for Baud Rate): 32. <br />  On the Pinout view, we will change the pin. <br /> Click on PB5 --> Select SPI1_MOSI. <br /> Click on PB3 --> Select SPI1_SCK. </td>
        </tr>
        <tr>
            <td align="center">ADC (Optional)</td>
            <td align="left">On the left panel, click <b>Analog</b> --> ADC1 --> Select Mode: IN4. </td>
        </tr>
        <tr>
            <td align="center">GPIO</td>
            <td align="left">The following will set the pins in Pinout view to <b>GPIO_Output</b> <br /> <b>CS: </b> Click PB15 --> Select GPIO_Output --> Right click to enter user label: LCD_CS <br /> <b>RS: </b> Click PB14 --> Select GPIO_Output --> Right click to enter user label: LCD_RS <br /> <b>RST: </b> Click PB12 --> Select GPIO_Output --> Right click to enter user label: LCD_RST  </td>
        </tr>
    </tbody>
</table>
</p>
<br />

The configuration process and the result view will be the following. On the top of the panel, click on <b>Project Manager</b>. 

  * Enter Project Name.   
  * Create a folder and set the Project Location to this folder. <br />For the Toolchain/IDE, select MDK-ARM --> Min Version select V5. 
  * In the Code Generator (left panel), click Generate peripheral initialization as a pair of c/h files per peripheral.  
  * On the top of the panel, click on <b>GENERATE CODE</b>.  
  * <b>Click Open Project</b>

<br />
Now the keil uvision IDE would open up itself with you project in it. 
<br />
<p align="center">
<img src="/Image/cubemx/CubeMX_config.gif" height="100%" width="100%">
</p>
<br />

You may wonder what pin of SPI should I use (you see we change the pin from the default PA5~7). Actually, ARM Cortex-M4 defined the combination of pins with SPI, I2C, UART. You can check the table from the STM32F411RE User Manual page 47 ~ 52. In the table, you can see we are using the combination of PB3/PB5, that combination also legit. 
<p align="center">
<img src="/Image/cubemx/SPI.png" height="100%" width="100%">
</p>
<br />

### 3.2 &nbsp; Start Coding
Before we get started, we need to configure the package. Make sure all the package are installed and up to date shown in the following image
<p align="center">
<img src="/Image/cubemx/package.png" height="100%" width="100%">
</p>
<br />

Then, we will configure the dubgger. Nucleo-64 board uses ST-LINK (the upper part of the board which you connect the mini usb to).
Click <b>Flash</b> tab --> Configure flash tools <br />

* C/C++ tab: check <b>C99 Mode</b>
* C/C++ tab: Include Paths --> New (Insert) --> click the MDK-ARM folder --> OK
* Debug tab: On the upper right side --> Use: --> Select ST-Link Debugger --> Settings --> Flash Download tab --> Check <b>Reset and Run</b>

let's check whether the generated code are correct by clicking <b>Project</b> tab --> Rebuild all target files. It should be 0 Error and 0 Warning. In the Project, you should see all the file in the following image. 
<p align="center">
<img src="/Image/cubemx/project_view.JPG" height="50%" width="50%">
</p>
<br />
Now we are going to add the code so that we can communicate with the ST7735 LCD. Right click the <b>Application/User</b> 

* Add new item.... --> Select <b>C File (.c) </b> --> Name: st7735  --> Copy paste the code (st7735.c) we provided in ST7735 folder.
* Add new item.... --> Select <b>Header File (.h) </b> --> Name: st7735 --> Copy paste the code (st7735.h) we provided in ST7735 folder.
* Add new item.... --> Select <b>C File (.c) </b> --> Name: fonts  --> Copy paste the code (fonts.c) we provided in ST7735 folder.
* Add new item.... --> Select <b>Header File (.h) </b> --> Name: fonts --> Copy paste the code (fonts.h) we provided in ST7735 folder.


Now we add something in the main.c file so that we can use st7735.c and st7735.h for our project.

* Open <b>main.c</b> in Application/User folder --> add the following code highlight with red square. These code will be enough for testing.
<p align="center">
<img src="/Image/cubemx/add_code.png" height="60%" width="60%">
</p>
<br />

# 4. Connect Hardware and Download the Code to the Nucleo-64
### 4.1 &nbsp; Connect hardware
Please refer to the following image to connect those devices.
<p align="center">
<img src="/Image/hardware_connect.png" height="100%" width="100%">
</p>
<br />
After you connect these devices, connect USB with the PC and Nucleo-64 board. Your PC should be able to detect the Nucleo board and the LED light near the Mini usb port should be steady red ligh. The LCD should be power on with white screen.

### 4.2 &nbsp; Download code to the Nucleo-64

* Click <b>Project</b> tab --> Rebuild all target files. It should be 0 Error and some Warning (it's fine). 
* Click <b>Flash</b> tab --> Download. 
* Do you see the LCD light become black and a string shows up. Cool !!!

### 4.3 &nbsp; More code and download code to the Nucleo-64
If everything is working good, we can keep going on. <br />
1. Add the following 3 functions in <b>main.c</b> after the main function
```c++
void plotData(void)
{
	ST7735_PlotPoint(sensorValue,ST7735_GREEN);
	ST7735_PlotIncrement();	
}

void drawInfoBar(void)
{
  ST7735_DrawString(1, 0, "CPU =", ST7735_GREEN);
  ST7735_DrawString(7, 0, "75%", ST7735_BLUE );
  ST7735_DrawString(11, 0, "Temp =", ST7735_GREEN);
  ST7735_DrawString(18, 0, "30", ST7735_BLUE );
}

void drawaxes(void){
    ST7735_Drawaxes(AXISCOLOR, BGCOLOR, "Time", "ADC", LIGHTCOLOR, "", 0, YMAX, YMIN);
}
```
Add the three header on the top of the <b>main.c</b> file
```c++
void drawInfoBar(void);
void drawaxes(void);
void plotData(void);
```
2. In the main function will be like the following. We read the value from the ADC (Analog to Digital Conversion) and this value would change if you tune the potentiometer. The plotData function will take this value and plot it on the LCD. 
```c++
int main(void)
{
  HAL_Init();
  //SystemClock_Config();
  MX_GPIO_Init();
  MX_ADC1_Init();
  MX_SPI1_Init();
	
	ST7735_Init();
	ST7735_FillScreen(ST7735_BLACK);
	drawaxes();
	drawInfoBar();
	
  while (1)
  {
		HAL_ADC_Start(&hadc1);
		HAL_ADC_PollForConversion(&hadc1,1);
		sensorValue = HAL_ADC_GetValue(&hadc1);
		
		plotData();		
  }

}
```
3. Rebuild the code and download to the board. Try to tune the Potentiometer, and then you will see the green line moving up and down.

# 5. Let's talk about the code <br />
Before starting to display anything on the LCD, we need to initialize and configure the LCD by sending some command and parameters (data) to the LCD board. This initialization only need to be processed once. After that, we can send data to the LCD with correct protocol.
### 5.1 &nbsp; Initialization

* 1. Chip Select (CS): Make CS pin low.
* 2. Reset (RST): Reset pin low --> delay 5~9 ms --> Reset pin high (Please refer to the ST7735s datasheet page 85)
* 3. Send command and parameter(data): Command such as Frame rate, inversion, color bit, memory reading order, ....(Please refer to the ST7735s datasheet page 104 ~ 186).
* 4. Chip un-select (CS): Make CS pin high.

### 5.2 &nbsp; Sending data (color)
Let's make the uper left corner of the LCD become green. In the following sending command as well as sending data, we all communicate via SPI. Remember that before sending data, we need to set the RS (Data or command) pin high. We set the RS pin low before sending command. 

* 1. Chip Select (CS): Make CS pin low.
* 2. Memory to Display Address Mapping:
	* Send command to perform Column address set
	* Send data: X_start (first 8 bit start from the most significant bit)
	* Send data: X_start (rest of the bit)
	* Send data: X_end (first 8 bit start from the most significant bit)
	* Send data: X_end (rest of the bit)
	* Send command to perform Row address set
	* Send data: Y_start (first 8 bit start from the most significant bit)
	* Send data: Y_start (rest of the bit)
	* Send data: Y_end (first 8 bit start from the most significant bit)
	* Send data: Y_end (rest of the bit)
	* Send command to perform writting to RAM
* 3. Send the color data (16-bit data for each color)
	* Send data: color (first 8 bit start from the most significant bit)
	* Send data: color (rest of the bit)
* 4. Chip un-select (CS): Make CS pin high.
	
	
# 6. Reference <br />

* Udemy course: Build Your Own RealTime OS (RTOS) From Ground Up on ARM 1. Instructor: Israel Gbati
* Github code: https://github.com/afiskon/stm32-st7735

# 7. The End <br />
The reason of making this tutorial is that the code provided from instructor of the Udemy course is not working at all and the instructor didn't reply to any student at all. I start from knowing nothing about SPI no need to say the LCD board. After 5 days of research, reading documentation, and trial and error, I finally make it works. This tutorial is to guide those people who want to get a deep knowledge of making a driver to display on LCD.
