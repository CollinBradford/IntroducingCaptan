#Introduction

In this tutorial we are going to see some of the main components of the FPGA board.  

##Green Board

The green board is the FPGA board.  The edition I am using comes with a Vertex 4 installed with some supporting hardware along with an ethernet interface card.  The FPGA is programmed through the JTag connector.  

The green board gets its power from two pins near the JTag connector.  3.5 volts should be applied to the pins.  They are labeled gnd and 3.3v.  You will need a benchtop power supply capable of sourcing at least 3 amps.  

##Blue Board

The blue board is the ADC board.  It comes with an ADC from Texas Instruments.  (Part Number: ADC081000)  Please note that this is NOT the ADC that is labeled on the schematics for the blue board.  When the blue board was populated we didn't have the right ADC so we put a different one on  The ADC can sample at up to 1.6 gsps.  

The blue board gets its power from the green board.  No  extra connections need to be made.  

##Schematics

Take a look trough the schematics in the schematics file.  There should be a set for both the blue and green boards.  I would recommend getting familiar with the main components.  The FPGA and ADC are the biggest things on each schematic.  Most of the connectors and ports on the circuit board can be found in the schematic by looking up the connector label.  

It is important to note that we have the 2nd edition green board and only the first edition blue board. If you get blue board v2 schematics they won't match up properly.  

##Software

The software suite for communicating with the Captan board is available for download in the resources folder.  The code should work out of the box.  It contains three seperate programs that work together to communicate and controll the Captan board.  The rest of the software is simply the webPack version of the ISE design suite tools.  You should be able to get a free license from Xilinx for the the software package that comes with only the basics.  
