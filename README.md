# 16 BYTES CMOS SRAM

Hi. This project aims to provide a comprehensive knowledge how we initiated our work for SRAM and then went on learning the project.

## Content

- [Introduction](https://github.com/Salonee2002/SRAM-16BYTES/tree/main#introduction)
- [6T SRAM Cell](https://github.com/Salonee2002/SRAM-16BYTES/tree/main#6t-sram-cell)
- [Pre-Charge Circuit](https://github.com/Salonee2002/SRAM-16BYTES/tree/main#pre-charge-circuit)
- [Row Decoder](https://github.com/Salonee2002/SRAM-16BYTES/tree/main#row-decoder)
- [Write Driver](https://github.com/Salonee2002/SRAM-16BYTES/tree/main#write-driver)
- [Sense Amplifier](https://github.com/Salonee2002/SRAM-16BYTES/tree/main#sense-amplifier)
- [Schematic Designs](https://github.com/Salonee2002/SRAM-16BYTES/tree/main#schematic-designs)
- [Testbench](https://github.com/Salonee2002/SRAM-16BYTES/tree/main#testbench)
- [Result Analysis and Discussion](https://github.com/Salonee2002/SRAM-16BYTES/tree/main#result-analysis-and-discussion)
- [Conclusion](https://github.com/Salonee2002/SRAM-16BYTES/tree/main#conclusion)

## Introduction

SRAM, or Static Random-Access Memory, is a fundamental component in modern digital systems, widely used for fast, volatile data storage. It plays a crucial role in processors, memory hierarchies, and various application-specific integrated circuits (ASICs). Unlike dynamic-RAM which must be continuosly refreshed SRAM doesn't have this requirement resulting better performance and low power usage. 
Here is the block diagram of the SRAM:

![sram_block](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/sram_block_1.drawio.png)

## 6T SRAM Cell

For making a memory of 16 Bytes we need 16 rows and 8 columns of SRAM. For single SRAM, there exist a small intrinsic capacitor. But for 16 SRAM in one column each intrinsic capacitor are in parallel, so they gets added up. For writing to or reading from the SRAM the access transistor should be ON. <br>
![6T_sram](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/6T_sram.drawio.png)<br>
**M3 and M4 are called ACCESS TRANSISTORS.**

### Working 
Before reading or writing the two nodes, BL and BLB, should have the same voltage. We can do this in 2 ways - pre-charge and pre-discharge. But here, we have done pre-charge. 
Let initially the data is 0 at node-1 and 1 at node-2. Since the node-1 is 0, NMOS (transistor-1) is ON and similarly PMOS (transistor-6) is ON. After, pre-charge we ON the access transistors, i.e transistor-3 and transistor-4 by giving the WL signal. Since BL and BLB is at Vdd (1.8V), the current starts flowing from transistor-3 to transistor-1 discharging the intrinsic capacitance. These 2 transisitors act like 2 resistors connected in series. And according to voltage divider rule gradually node-1 increases and BL node decreases ( BL doesn't decreases to 0). We need to set the node-1 voltage to less than 0.6V so that it doesn't toggle. So, we have set the node voltage to 0.3V. On the other hand, transistor-4 is OFF so, BLB doesn't change and remains at Vdd. This difference between BL and BLB is connected to the sense amplifier reading and giving the output as 0 or 1 (if BL<BLB, it reads 0 and if BL>BLB, it reads 1). From above mentioned, let node-2 is 1 and we want to overwrite it by 0. So, the BLB is 0 (as we want to overwrite by 0). The current flows from transistor-6 to transistor-4 charging the intrinsic capacitance. Here the transistors also act like resistors in series. While reading transistor-3 is in saturation region and transistor-1 is in linear region and while writing transistor-6 is in linear and transistor-4 is in saturation region.
### Designing of the transistors
![read_op](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/read_operation.drawio.drawio.png)<br>
For M3, Vgs = 1.8-0.3 = 1.5V, Vds = 1.5V. So,Vds>Vgs-Vt. Hence M3 is in **saturation**. <br><br>
**$I_{\text{DS3}} = \frac{1}{2} \mu_{\text{n}} C_{\text{ox}} \frac{W}{L} (V_{\text{GS}} - V_{\text{th}})^2$**<br><br>
**$I_{\text{DS3}} = \mu_{\text{n}} C_{\text{ox}} \frac{W}{L} (0.344)$**<br><br>
For M1, Vgs = 1.8V, Vds = 0.3V. So,Vds<Vgs-Vt. Hence M1 is in **linear**. <br><br>
**$I_{\text{DS1}} = \mu_{\text{n}} C_{\text{ox}} \frac{W}{L}$ [ $\left(V_{\text{GS}} - V_{\text{th}}\right) V_{\text{DS}} - \left(\frac{V_{\text{DS}}}{2}\right)^2$ ]** <br><br>
**$I_{\text{DS1}} = \mu_{\text{n}} C_{\text{ox}} \frac{W}{L} (0.294)$**<br><br>
Since, I3 = I1<br><br>
**$\mu_{\text{n}} C_{\text{ox}} \frac{W}{L} (0.344) = \mu_{\text{n}} C_{\text{ox}} \frac{W}{L} (0.294)$**<br><br>
Since, I3 = I1<br><br>
<span style="font-size: larger;">**$\frac{(\frac{W}{L})_3}{(\frac{W}{L})_1} = \frac{0.294}{0.344}$**</span> <br><br>
<span style="font-size: larger;">**$\frac{(\frac{W}{L})_3}{(\frac{W}{L})_1} = 0.85$**</span> <br><br>
**$\frac{W}{L})_3 = 0.85 (\frac{W}{L})_1$** <br><br>

![write_op](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/write_op.drawio.png)<br><br>
For M3, Vgs = 1.8V, Vds = 0..3V. So,Vds>Vgs-Vt. Hence M3 is in **linear**. <br><br>
**$I_{\text{DS3}} = \mu_{\text{n}} C_{\text{ox}} \frac{W}{L}$ [ $\left(V_{\text{GS}} - V_{\text{th}}\right) V_{\text{DS}} - \left(\frac{V_{\text{DS}}}{2}\right)^2$ ]** <br><br>
**$I_{\text{DS3}} = \mu_{\text{n}} C_{\text{ox}} \frac{W}{L} (0.294)$**<br><br>
For M5, Vgs = 0V, Vds = 0.3V. So,Vds>Vgs-Vt. Hence M1 is in **saturation**. <br><br>
**$I_{\text{DS5}} = \frac{1}{2} \mu_{\text{p}} C_{\text{ox}} \frac{W}{L} (V_{\text{GS}} - V_{\text{th}})^2$**<br><br>
**$I_{\text{DS5}} = \mu_{\text{p}} C_{\text{ox}} \frac{W}{L} (0.4489)$**<br><br>
Since, I5 = I3<br><br>
<span style="font-size: larger;">**$\frac{(\frac{W}{L})_5}{(\frac{W}{L})_3}$ = 2 * &mu;<sub>n</sub> / &mu;<sub>p</sub> $\frac{0.294}{0.4489}$**</span> <br><br>
<span style="font-size: larger;">**$\frac{(\frac{W}{L})_5}{(\frac{W}{L})_3}$** = 2 * 5 * 0.654</span> <br><br>
**$(\frac{W}{L})_5 = 6.54 (\frac{W}{L})_3$** 

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
SNM is the measure of stability of the SRAM cell to hold its data against noise. SNM of SRAM is defined as minimum amount of noise voltage present on the storing nodes of SRAM requires to flip the state of the cell. This graph is plotted between the node-1 and node-2.<br>
![SNM_setup](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/SNM_setup.drawio.png)<br>

![SNM](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/SNM.png)

## Pre-Charge Circuit

This is the circuit for pre-charge.<br><br>
![PC](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/PC.drawio.png)<br>

Why do we do pre-charging or pre-discharging?
Before reading or writing from/to the SRAM, we need to make the BL and BLB of same voltage, we can do it by either ways, by pre-charging them to 1.8V or pre-discharging them to 0V. 

Then the next question arises that why did we go for pre-charge and not pre-discharge? <br>
If we are going for pre-discharge then the area of the transistors increases by 11.9%.

Can we go for pre-charge and pmos as the access transistors?
It's technically possible to use PMOS transistors for access during pre-charge, it would be unconventional and may introduce unnecessary complexity and potential performance drawbacks.
NMOS transistors are used for active pull-down operations in SRAM cells because they can discharge the storage nodes quickly when needed. And using PMOS can discharge but taking up longer time than NMOS making the device slower.

## Row Decoder

The combinational circuit that change the binary informaton into 2^N output lines is known as decoder. Here, we have used 4:16 decoder, i.e 4 input lines and 16 output lines decoder.<br>
![Row_decoder](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/row_decoder.drawio.png)<br>
Row decoder is used to select the row in which we want to write 8-bits data or read 8-bits data to or from the SRAM.<br>
So, basically, we provide the 4-bit address to the decoder where we want to perform the read or write operation. Inside the decoder if the control signal is 1 then the decoder output is the desired address. And this output is the **wordline** that goes to the SRAM to select the row (in each row 8 SRAMs are present) to read or write.

## Write Driver

This is the write driver.<br>
![Write_driver](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/write_driver.drawio.png)<br><br>
This is write driver switch. Its basically used to write the data to the SRAM. <br>
![Write_switch](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/write_switch.drawio.png)<br><br>
**Operation :**
When rw and ctrl signal are both high RW signal is high. And when RW is high the driver switch is ON. din is the input and this will be written to the SRAM through BL and BLB.<br><br>
![Write_ctrl](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/RW.drawio.png)<br>

## Sense Amplifier

It is a differencial amplifier based sense amplifier. This device allows a quick reading of the differencial signal on the bit lines, so that the discharging process can be stopped as soon as possible, to the advantage of speed and power consumption.<br>
![sense_amp](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/sense_amp.drawio.png)<br>
We have designed the transistors in such a way that current through transistor-0 and transistor-5 are equal, i.e 1uA. The transistor-0 and transistor-5 are in current mirror. According to KCL, the current is divided equally through transistor-1 and transistor-2, i.e 0.5uA each. As transistor-3 and transistor-4 are in series with transistor-1 and transistor-2 respectively, same current flows through them. To make the current in transistor-4 same as transistor-3, both should in current mirror.<br> <br>
OPERATION :: <br>
When BL=1.6V and BLB-1.8V, current through transistor-0 is 1uA. As transistor-0 is in current mirror with transistor-5 same current flows through it, i.e 1uA. Since BL voltage is 1.6V, I1=0.4uA (say) and I2=0.6uA (according to KCL ). As transistor-1 and transistor-3 are in series, so, I3=0.4uA. Due to current mirror with transistor-3, the same current flows as of transistor-4, i.e 0.4uA. Current from node (0.2uA) will be added up with 0.4uA to satisfy KCL. Since node current decreases, node voltage also decreases. Here, in this sense amplifier we have set the node voltage to 0.9991, it means when BL and BLB are at 1.8V the node voltage is 0.9991V. When node current decreases, the node voltage decreases below 0.9991V. The buffer connected to the sense amplifier is designed in such a way that a small change in output of sense amplifier amplifies to 0 or 1.<br><br>
DESIGN ::<br><br>
We know, I<sub>0</sub> = $\frac{1}{2} \mu_{\text{n}} C_{\text{ox}} \frac{W}{L} (V_{\text{GS}} - V_{\text{th}})^2 \(1 + \lambda V_{ds}\)$<br>
Due to short channel effect, current increases through the transistors. So, we increase the length of transistor to maximum to decrease the $\lambda$ ($\lambda$ and L are inversely propotional) to make the current constant.<br>
So,we have kept the length of transistors as 1um.
First we have designed the nmos transisitors in such a way that it is in saturation region by keeping the g<sub>m</sub> over I<sub>d</sub> ratio between 4 and 10. We have kept the M5 transistor in saturation by connecting drain and gate. Keeping the gate voltage same of transistor M0 with transistor M5, it is also kept in saturation region.Here, our $\frac{gm}{Id}$ is around 9.<br>
The transistors 1 and 2 are in subthreshold region.<br>
![semse_amp_nmos](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/nmos_SA.png)<br><br>
Then we did the same steps to keep the pmos in saturation region. And the $\frac{gm}{Id}$ is around 9. And also set the node voltage in such away that it is around 0.9V.<br>
![sense_amp_pmos](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/pmos_SA.png)<br><br>
We have kept the length of the transistors of buffer as 0.18um.
Here, in our design we have kept it as 999.1mV. The buffer input voltage is 999.1mV and the first buffer output is 914mV and the final output is designed in such a way that it is either high (1.8V) or low (0V). <br>
![sense_amp_buff](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/buffer_SA.png)<br><br>
By doing the AC analysis of the node, we got the node voltage as 42.75V.
![sense_amp_node](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/node_voltage_senseamp.png)<br><br>
And the gain is 32.6dB. The maximum gain is 40dB when the node voltage is 100V during AC analysis.<br> 
![sense_amp_gain](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/gain_senseamp.png)<br><br>

Here is the sizes of the sense amplifier and the buffer :
| Transistor | Size | Multipliers |
| :---------: | :----------: | :----------: |
| W<sub>M0,M5</sub> | 250n | 1 |
| W<sub>M1,M2</sub> | 700n | 2 |
| W<sub>M3,M4</sub> | 350n | 2 |
| W<sub>pb1</sub> | 930n | 6 |
| W<sub>nb1</sub> | 300n | 1 |
| W<sub>pb2</sub> | 930n | 6 |
| W<sub>nb2</sub> | 300n | 6 |  

<br><br>Here, it is the graph of the sense ampllifier with different conditions of the BL and BLB.
<br>![sense_amp_graph](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/Screenshot%20from%202023-10-16%2013-21-54.png)

## Schematic Designs
**PC**<br>
![PC_sc](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/PC_sc.png)<br><br>
**SRAM**<br>
![sram_sc](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/sram_sc.png)<br><br>
**SRAM_ARRAY**<br>
![sram_arr_sc](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/sram_array_sc.png)<br><br>
**WRITE_SWITCH**<br>
![write_switch](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/write_switch_sc.png)

## Testbench

![test_in](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/testbench_in.png)<br>

<br>TOP TESTBENCH<br>
![test_top](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/testbench_top.png)

## Result Analysis and Discussion

The overall graph we got is shown below : <br><br>
![overall_graph](https://github.com/Salonee2002/SRAM-16BYTES/blob/main/overall_graph.png)<br><br>
From this graph we have also calculated the delays, i.e write delay, read delay, delay from PC to BL (time taken by the BL to become stable from PC), delay from PC to decoder (time taken by the decoder to be stable from PC). The delays are show below : <br><br>

**TO READ OR WRITE '0' :**
| Corners | Operation | -40 | 27 | 85 |
| :---------: | :----------: | :----------: | :----------: | :----------: |
| Wp | read| 10.02ns | 10.16ns | 10ns |
|  | write| 867ps | 971ps | 1.023ns |
| Tm | read| 8.106ns | 8.573ns | 9.043ns |
|  | write | 1.074ns | 1.0178ns | 1.276ns |
| Ws | read | 6.75ns | 7.15ns | 7.253ns |
|  | write | 1.348ns | 1.481ns | 1.601ns |    

**TO READ OR WRITE '1' :**
| Corners | Operation | -40 | 27 | 85 |
| :---------: | :----------: | :----------: | :----------: | :----------: |
| Wp | read| 3.165ns | 2.847ns | 2.757ns |
|  | write| 1.517ns | 1.669ns | 1.81ns |
| Tm | read| 4.567ns | 4.412ns | 4.299ns |
|  | write | 1.895ns | 2.09ns | 2.28ns |
| Ws | read | 5.959ns | 5.817ns | 5.69ns |
|  | write | 2.397ns | 2.649ns | 2.879ns |  

**PC_BL delay and PC_dec delay**
**PC to BL delay**
| Corners | -40 | 27 | 85 |
| :---------: | :----------: | :----------: | :----------: |
| Wp | 399.7ps | 428ps | 296.2ps |
| Tm | 473.6ps | 517.4ps | 384.7ps |
| Ws | 547.1ps | 621.8ps | 601ps |  

**PC to decoder delay**
| Corners | -40 | 27 | 85 |
| :---------: | :----------: | :----------: | :----------: |
| Wp | 528.2psps | 575.8ps | 610.7ps |
| Tm | 659.9ps | 718.1ps | 758.6ps |
| TWs | 828.1ps | 899.5ps | 948.3ps |  


**Power consumption**
| Corners | -40 | 27 | 85 |
| :---------: | :----------: | :----------: | :----------: |
| Wp | 849.3us | 976.25us | 1.049ms |
| Tm | 570.1ps | 649.4ps | 683.1us |
| Ws | 296.7us | 899.5ps | 948.3ps |  

From these tables, we can see the delays are more for reading zero (0) than one (1) because the pmos we have used in sense amplifier's buffer has more strength than the nmos used in buffer. As we know, pmos is a pull-up device, therefore, discharging in the node of sense amplifier takes more time than reading 1.
We can also find that delays are abnormal because the node voltage of the sense amplifier is not being stable at the 999.1mV when BL and BLB are 1.8V.

**The maximum operating frequency is 50Mhz.** <br>
In our circuit we have operated in 20Mhz.


## Conclusion

We have designed a 16 byte SRAM having 16 rows and 8 columns (each row of 1 byte). We have used pre-charge circuit to pre-charge the bitline to 1.8V, row decoder to select the row for performing the operation, write driver to write into the SRAM and sense amplifier to read the data from SRAM.
The maximum operating frequency is 50Mhz.
The worst case power consuption is 1.049U watt at worst power (corner) and 85&deg;C. And the minimum and maximum delays are shown below :<br>
| | Min delay | Max delay |
| :---------: | :----------: | :----------: |
| read | 2.757ns | 5.959ns |
| write | 1.517ns | 2.879ns |
| PC_BL | 296.2ps | 621.8ps |
| PC_dec | 528.2ps | 948.3ps |
| Power | 296.7uW | 1.049mW |  

