#Introduction

In this tutorial we are going to see some of the main components of the FPGA board.  

##Green Board

The green board is the FPGA board.  The edition I am using comes with a Vertex 4 installed with some supporting hardware along with an ethernet interface card.  The FPGA is programmed through the JTag connector.  

The green board gets its power from two pins near the JTag connector.  3.5 volts should be applied to the pins.  They are labeled gnd and 3.3v.  You will need a benchtop power supply capable of soursing at least 3 amps.  

##Blue Board

The blue board is the ADC board.  It comes with an ADC from Texes Instruments.  (Part Number: ADC08D1020)  This ADC can sample two different channels at 1 gsps, or it can sampel just one channel at double the speed.  For this project I am using the second mode to achieve a sample rate of about 1.5 gsps.  I could go further, but the FPGA has limitations that prevent me from doing so.  

The blue board gets its power from the green board.  No  extra connections need to be made.  

##Schematics

Take a look trough the schematics in the schematics file.  There should be a set for both the blue and green boards.  I would recommend getting familiar with the main components.  The FPGA and ADC are the biggest things on each schematic.  
