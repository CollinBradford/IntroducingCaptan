# Firmware

This document sheds some light on how the firmware works.  I'll probably just go through page by page and give a general idea of what's going on.  

The firmware should be in the GEL_CAPTAN folder under dig_mac.isxe.  Once it is open, in the navigation bar to the left, select TOP_LEVEL.  The page navigation is done by selecting the tabs at the top of the viewer in the main part of the window.  

## Page 1

Page 1 is mostly dedicated to communication with the host computer.  In the top left corner is the OTS Ethernet Interface module that allows communication with the computer via setting registers and sending data to the computer via burst mode.  The other two main components on this page are the two multiplexers on the right-hand side that allow for the proper registers to be set depending on the address.  

## Page 2

I have never used page 2. 

## Page 3

This is where most of the firmware for the NanoCam project lives.  On the left, there are a collection of flip-flops that comprise the registers used for the NanoCam firmware.  More about those later.  

The data itself starts toward the left side of the page and moves right.  It first goes through the ADC_FIFO.  When it comes in, it is synchronized with the FADC_DCLK which is the returning clock from the ADC.  During normal operation, this clock frequency should be around 750 mhz.  Data from the ADC is sampled on every rising edge of this clock.  Upon leaving the ADC_FIFO, the data is synchronous with MASTER_CLK on a larger bus.  The data coming from the ADC to the FIFO consists of two 8 bit samples and comes out of the ADC_FIFO as 8 8-bit samples.   

After going through the ADC_FIFO, the data passes through the PeakFinder module.  The PeakFinder module compares each sample of the data with the threshold set by the user.  Once one of the samples is seen to be higher than the threshold, it will pull the ethernet_fifo_in_en signal high for the number of clock cycles specified by the read_size register.  Each clock represents 8 additional signals that are recorded.  

Although the ethernet_fifo_in_en signal causes the ethernet_fifo to read immediately, the data coming from the PeakFinder module is delayed three clocks by using the bank of flip-flops.  This allows for 8x3 or 24 samples to be recorded before the actual trigger which allows for a rough version of pretrigger sampling.  The number of pretrigger samples cannot be adjusted through registers, but this method of pretrigger sampling allows for continuous operation without any pauses between triggers.  Because of the pre-trigger sampling, the read_size register should be set to at least 4 in order to read the actual trigger. 

After being buffered by the ethernet_fifo, the data moves on to the data_send module.  The data_send module regulates the flow of data to the OEI.  The data_send_delay_time register sets the speed of the data.  The data_send module will send a group of 8 samples to the OEI and will then delay for the number of clock cycles specified by the data_send_delay_time register.  In this way, the data flow can be slowed, preventing the ethernet card on the user's computer from becoming overloaded and dropping samples.    

### Extra Features

So far, I have discussed the path that the main data takes, but there are a couple of other features that play into the data's path that don't quite fit into the previous explanation.  

#### Statistics Reporting

Statistics and error reporting are currently being done in the stat_pulse module at the bottom of the page.  This module counts the number of new triggers detected by the peak_finder module and uses two latches to detect whether there has been a data overflow from either the ADC_FIFO or the etherent_fifo memory blocks.  This data is then reported in one 64 bit package every second.  This second could easily be controlled by a register in order to adjust the time between reports, but this feature hasn't been added.  

In order to insert this data into the main flow of data exiting the FPGA, the statists reporting module takes in the ethernet data as well as the ethernet_in_en signal from the PeakFinder module.  When it is time to send the statistics packet, the statistics module will wait until the ethernet_in_en signal is low meaning that the PeakFinder module is not currently sending data.  Since the statistics packet is only 64 bits long, it should only take one clock cycle to insert it into the main data stream going to the ethernet fifo.  However, in the event that a trigger happens in the same clock cycle as the statistic packet is sent into the stream, the ADC data will take precedence over the statistics packet and the packet will be lost.  

Since I have just added this feature, I haven't been able to test it yet, but this is the general idea.  

#### New Packet Signal

The new packet signal travels with the data through the ethernet fifo and allows the data_send module to detect when a new trigger has been reached or a new statistics packet has been placed.  This enables the data_send module to split the sperate events up and send only one trigger per UDP packet or one statistics packet per DUP packet.  The new packet signal coming out of the PeakFinder module is the new_peak signal.  The new packet signal coming out of the statistics module is the new_stat signal.  These are basically the same thing.  After being combined, the two signals enter the etnernet_fifo with the rest of the data from the ADC as ethernet_fifo_d_in(64)  (the 65th bit on the data bus).  

## Page 4

This page mainly deals with the various clock signals.  The top half contains the DCMs and hardware needed to accelerate the MASTER_CLK signal up to 750 mhz before it is sent to the ADC.  The bottom half conditions the FADC_DCLK signal coming from the FPGA. 

## Page 5

These are just the input and output buffers.  This is a good way to find out the pin location of any input and output signals named in the firmware.  

## TOP_LEVEL.ucf

The constraints file defines the pins that are used in the firmware.  Most of the pins should already be defined, but most of them are commented out and unused.  Should you need to use an extra pin, uncomment it here, add an IBUF or OBUF on page 5, and give it a proper signal name to be used in the firmware. 
