# Introduction

This is just a simple explanation of the main features of the project.  

## Hardware

### Green Captan Board

The green board is the FPGA board.  The edition I am using comes with a Vertex 4 installed with some supporting hardware along with an ethernet interface card.  The FPGA is programmed through the JTag connector.  

The green board gets its power from two pins near the JTag connector.  3.5 volts should be applied to the pins.  They are labeled gnd and 3.3v.  

### Blue Captan Board

The blue board is the ADC board.  It comes with an ADC from Texas Instruments.  (Part Number: ADC081000)  Please note that this is NOT the ADC that is labeled on the schematics for the blue board.  When the blue board was populated we didn't have the right ADC so we put a different one on  The ADC can sample at up to 1.6 gsps.  

The blue board gets its power from the green board.  No  extra connections need to be made.  However, be careful when disconnecting and connecting the blue board to the green board.  The busses can be easily confused.  

### Schematics

There should be a set for both the blue and green boards available for download in the resources folder.  I would recommend getting familiar with the main components.  The FPGA and ADC are the biggest things on each schematic.  Most of the connectors and ports on the circuit board can be found in the schematic by looking up the connector label.  

It is important to note that we have the second edition green board and only the first edition blue board. If you get blue board v2 schematics they won't match up properly.  

Please note that the actual Captan boards vary from the schematic slightly.  

## Software

### Captan Control Software

The software suite for communicating with the Captan board is available for download in the resources folder.  It contains three separate programs that work together to communicate and control the Captan board.  It contains three programs that allow the user to control multiple Captan boards from multiple users and machines on the same network.  

### Custom Control Software

For the NanoCam project, I have written a basic Python program that can also be found in the resources folder.  This script sets the proper registers and communicates with the FPGA to capture data and save it for later viewing.  

