# SRAM-16BYTES

Hi. This project aims to provide a comprehensive knowledge how we initiated our work for SRAM and then went on learning and finished the project.

## Content

- Introduction
- 6T SRAM Cell
- Pre-Charge Circuit
- Row Decoder
- Write Driver
- Sense Amplifier
- Schematic Designs
- Testbench
- Result Analysis and Discussion
- Conclusion

## Introduction
SRAM, or Static Random-Access Memory, is a fundamental component in modern digital systems, widely used for fast, volatile data storage. It plays a crucial role in processors, memory hierarchies, and various application-specific integrated circuits (ASICs). Unlike dynamic-RAM which must be continuosly refreshed SRAM doesn't have this requirement resulting better performance and low power usage. 
Here is the block diagram of the SRAM:

![sram_block](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/sram_block_1.drawio.png)

## 6T SRAM Cell
For making a memory of 16 Bytes we need 16 rows and 8 columns of SRAM. For single SRAM, there exist a small intrinsic capacitor. But for 16 SRAM in one column each intrinsic capacitor are in parallel, so they gets added up. For writing to or reading from the SRAM the access transistor should be ON. <br>
![6T_sram](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/6T_sram.drawio.png)
### Working 
Before reading or writing the two nodes, BL and BLB, should have the same voltage. We can do this in 2 ways - pre-charge and pre-discharge. But here, we have done pre-charge. 
Let initially the data is 0 at node-1 and 1 at node-2. Since the node-1 is 0, NMOS (transistor-1) is ON and similarly PMOS (transistor-6) is ON. After, pre-charge we ON the access transistors, i.e transistor-3 and transistor-4 by giving the WL signal. Since BL and BLB is at Vdd (1.8V), the current starts flowing from transistor-3 to transistor-1 discharging the intrinsic capacitance. These 2 transisitors act like 2 resistors connected in series. And according to voltage divider rule gradually node-1 increases and BL node decreases ( BL doesn't decreases to 0). We need to set the node-1 voltage to less than 0.6V so that it doesn't toggle. So, we have set the node voltage to 0.3V. On the other hand, transistor-4 is OFF so, BLB doesn't change and remains at Vdd. This difference between BL and BLB is connected to the sense amplifier reading and giving the output as 0 or 1 (if BL<BLB, it reads 0 and if BL>BLB, it reads 1). From above mentioned, let node-2 is 1 and we want to overwrite it by 0. So, the BLB is 0 (as we want to overwrite by 0). The current flows from transistor-6 to transistor-4 charging the intrinsic capacitance. Here the transistors also act like resistors in series. While reading transistor-3 is in saturation region and transistor-1 is in linear region and while writing transistor-6 is in linear and transistor-4 is in saturation region.
### Designing of the transistors
![read_op](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/read_operation.drawio.drawio.png)

### DC sweep (reading)
Varying Vbl from 0 to Vdd the node-1 voltage (Vx) increases gradually and becomes constant at our set voltage 0.3V.<br>
![read_sweep](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/read_sweep.png)
### Transient (reading)
When Vwl=1 it reads the node-1 voltage, i.e 0.
![read_trans](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/read_trans.png)

### DC sweep (writing)
varying Vbl from 1.8V to 0V, the node voltage toggle at 0.
![write_sweep](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/write_sweep.png)

### Transient (writing)
When Vwl=1 it reads the node-1 voltage, i.e 0.
![write_trans](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/write_trans.png)

### SNM
![SNM](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/SNM.png)

## Pre-Charge Circuit
![PC](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/PC.drawio.png)


## Row Decoder
The combinational circuit that change the binary informaton into 2^N output lines is known as decoder. Here, we have used 4:16 decoder, i.e 4 input lines and 16 output lines decoder.<br>
![Row_decoder](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/row_decoder.drawio.png)<br>
Row decoder is used to select the row in which we want to write 8-bits data or read 8-bits data to or from the SRAM.<br>
So, basically, we provide the 4-bit address to the decoder where we want to perform the read or write operation. Inside the decoder if the control signal is 1 then the decoder output is the desired address. And this output is the **wordline** that goes to the SRAM to select the row (in each row 8 SRAMs are present) to read or write.

## Write Driver
![Write_driver](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/write_driver.drawio.png)<br>
![Write_switch](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/write_switch.drawio.png)<br>

## Sense Amplifier
It is a differencial amplifier based sense amplifier. This device allows a quick reading of the differencial signal on the bit lines, so that the discharging process can be stopped as soon as possible, to the advantage of speed and power consumption.<br>
![sense_amp](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/sense_amp.drawio.png)
We have designed the transistors in such a way that current through transistor-1 is equal, i.e 1uA. The transistor-1 and transistor-2 are in current mirror. According to KCL, the current is divided equally through transistor-3 and transistor-4, i.e 0.5uA each. As transistor-5 and transistor-6 are in series with transistor-3 and transistor-4 respectively, same current flows through them. To make the current in transistor-6 same as transistor-5, both should in current mirror.<br> 
OPERATION :: <br>
When BL=1.6V and BLB-1.8V, current through transistor-1 is 1uA. As transistor-2 is in current mirror with transistor-1 same current flows through it, i.e 1uA. Since BL voltage is 1.6V, I1=0.4uA (say) and I2=0.6uA. As transistor-5 and transistor-3 are in series, so, I3=0.4uA. Due to current mirror with transistor-6, the same current flows as of transistor-5, i.e 0.4uA. Current from node (0.2uA) will be added up with 0.4uA to satisfy KCL. Since node decreases, node voltage also decreases. Here, in this sense amplifier we have set the node voltage to 0.9991, it means when BL and BLB are at 1.8V the node voltage is 0.9991V. When node current decreases, the node voltage decreases below 0.9991V. The buffer connected to the sense amplifier is designed in such a way that a small change in output of sense amplifier amplifies to 0 or 1.
