# Firmware

This document sheds some light on how the firmware works.  I'll probably just go through page by page and give a general idea of what's going on.  

The firmware should be in the GEL_CAPTAN folder under dig_mac.isxe.  Once it is open, in the navigation bar to the left, select TOP_LEVEL.  The page navigation is done by selecting the tabs at the top of the viewer in the main part of the window.  

## Page 1

Up in the top left corner of page one is the Off The Shelf Ethernet Interface, or OEI.  This is the little black box module that communicates with the computer over the ethernet connection.  The multiplexers in the bottom left corner are for the registers.  

## Page 2

I have never used page 2. 

## Page 3

This is where most of the firmware for the NanoCam project lives.  On the left, there are a collection of flip-flops that comprise the registers used for the NanoCam firmware.  

The data itself starts toward the left side of the page and moves right.  It first goes through the ADC_FIFO.  When it comes in, it is synchronised with the FADC_DCLK which is the returning clock from the FPGA.  At full speed, this clock is at around 750 mhz.  Upon leaving the ADC_FIFO, the data is synchronous with MASTER_CLK.  

After going through the ADC_FIFO, the data passes through the PeakFinder module.  At the present time, there are four of these modules that I put there in an effort to widen the buss, but I plan on combining these into one clean PeakFinder module.  The data coming out of the PeakFinder modules is actually the same data that goes in.  The way I control the sampling is by controlling the ethernet_fifo_in_en signal that enables the ethernet_fifo to record the data.  

The ethernet_FIFO is the main buffer that buffers the data before it is sent through the OEI.  

The data_send module regulates the flow of data to the OEI.  For right now, the data_send module's function is mostly to delay the data in accordance with the delay time specified by the user. However, in the future, this module could be used to add flags and meta info along with the data.  

## Page 4

This page mainly deals with the various clock signals.  The top half contains the DCMs and hardware needed to accelerate the MASTER_CLK signal up to 750 mhz before it is sent to the ADC.  The bottom half conditions the FADC_DCLK signal coming from the FPGA.

## Page 5

These are just the input and output buffers.  This is a good way to find out the pin location of any input and output signals named in the firmware.  

## ChipScope

The ChipScope core is used for analyzing signals.  The signals being analyzed can be edited through the ChipScope Core Inserter by double clicking the ChipScope file in the project explorer.  Opening the Inserter can take a while sometimes as the project has to be synthesized first.  

Once the window is open, click next until you get to the "Trigger Parameters" tab.  If you are going to be adding signals, increase the trigger width.  Otherwise, continue to the "Net Connections" tab.  Click on "Modify Connections".  You will be presented with the Net selections window.  The space on the left of the windows allows you to select different signals and you can place them into the core with the controls on the right side of the window.  

To add a new signal, select "Trigger/Data Signals" on the right, find an empty signal space, select the new signal on the left, and click "Make Connections."

To remove a signal, find it on the right, select it, and click "Remove Connections." You will then have to select all the signals below it and click "Move Nets Up" until the space is filled.  After the signal has been removed, go back to the "Trigger Parameters" tab in the Core Inserter window and edit the trigger width to match the new number of signals.  Please note that the table in the the signal selections window is zero indexed and the trigger width parameter is not, so you will have to add one to the signal number in the signal editor to match the true number of signals.  

## TOP_LEVEL.ucf

The constraints file defines the pins that are used in the firmware.  Most of the pins should already be defined, but most of them are commented out and unused.  Should you need to use an extra pin, uncomment it here, add an IBUF or OBUF in page 5, and give it a proper signal name to be used in the firmware. 
