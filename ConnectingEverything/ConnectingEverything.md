# Connecting Everything

This tutorial will cover basic setup of the 

## Setting Everything Up

The Captan board is powered using 3.3v on the connector near the JTag socket.  The board will usually pull some 3-4 amps, so it needs a benchtop power supply.  If the board has been successfully powered on, some of the lights on the ethernet chip will blink.  

## Connecting to the Board

The default IP address for the ethernet module is 192.168.133.2 .  To connect to the Captan board, the first four numbers of the IP address of your computer must match up with the Captan's address.  To check this, go to: Network and Sharing Center => \<network with captan\> => properties => select IPv4 => properties.  The IP address in the IP address field should be something like: 192.168.133.199 . You can choose the last number just as long as it isn't 2.  The subnet mask should be set to 255.255.255.0 . Once everything is right, click OK.  

## Flashing Firmware

To connect to the FPGA to flash firmware, you need to use a USB to JTag cable.  Xilinx sells one called the Platform Cable USB II that works really well.  

There are two ways to flash the firmware to the captan board.  To get a good idea about the differences between the two, it is helpful to know a little bit more about FPGAs.  On startup, the FPGA will lay out the logic circuit that is contained in an external flash memory.  If you program this external flash memory, the circuit will be persistent through a power cycle.  While the FPGA is on, it can be programmed through the JTag connector.  The FPGA will write the circuit, but it will not be stored in the flash memory.  Because of this, the logic circuit will revert to whatever circuit is written in the flash memory after a power cycle.  

There are two different programs to use when flashing firmware to the Captan.  The first uses ChipScope which is normally used for looking at signals inside the FPGA while it is running.  ChipScope can only be used to temporarily flash firmware to the FPGA.  This firmware is not persistent.  The second method, using iMPACT, can be used for temporarily reprogramming the FPGA and programming the external flash memory.  Impact is a more dedicated piece of software meant for flashing firmware to Xilinx chips.  

### ChipScope

ChipScope is a logic analyzer meant for visualizing signals inside of the FPGA during runtime.  However, there is a convenient little feature that lets you program the FPGA for testing.  You cannot use ChipScope to program the flash memory.  To program the FPGA using ChipScope, ensure that your JTag connector is plugged in and that your device is powered on, and open the ChipScope analyzer. In the top left corner of the window, click the "open cable" button.  

![alt text](https://github.com/CollinBradford/IntroducingCaptan/blob/master/ConnectingEverything/Images/Open_Cable.png "Open Cable")

This results in a window that displays the devices connected to the JTag connection.  Select the FPGA from the list.  Next, from the top menu, select Device=>\<your device\>=>Configure.  This will open a window that allows you to select the bit file and CDC file.  The bit file will normally be in the same directory as your FPGA project.  You have the opportunity to import the CDC file associated with the project, but it is not necessary.  Importing it helps by naming all the signals you are analyzing.  Press the "OK" button, and ChipScope will flash the FPGA.  

### iMPACT

iMPACT is useful for flashing firmware when you want to program the flash memory as well as the FPGA itself.  To use iMPACT, first ensure that your device is powered on and connected to the JTag cable and launch iMPACT.  In the left side menu, select "Boundary Scan."  In the top toolbar, click the "Open Chain" button.  After everything loads, the main area of the iMPACT window should populate with a representation of all the devices connected to the JTag connection.  They should be labeled with different logos depending on the device.  For the Captan board, the first device should be the flash memory, and the second device will be labeled "V4" for Vertex 4.  

![alt text](https://github.com/CollinBradford/IntroducingCaptan/blob/master/ConnectingEverything/Images/iMPACT%20Main%20Window.png "iMPACT Main Window")

#### Flashing FPGA

To program the FGPA, select the device icon from the boundary scan and click the "Assign New Configuration File" button from the toolbar at the top.  A file viewer will open where you can select the bit file.  Once this is selected, from the lower left menu, double click "Program" and it should program the FPGA.  

#### Programming Flash Memory

To program the flash memory, you have to create a PROM file using the bit file generated from the Xilinx compiler.  From the top left menu, double click "Create PROM File."  A window should open allowing you to create the file in three steps.  First, select "Xilinx Flash/PROM" and hit the green arrow.  Second, add the storage device by selecting "Platform Flash" and "xcf32p [32M]" from the dropdown menus and click "Add Storage Device".  Lastly, change the title of the PROM file and select the location to put it.  At this point, your window should look something like this: 

![alt text](https://github.com/CollinBradford/IntroducingCaptan/blob/master/ConnectingEverything/Images/PROM%20Generator.PNG "PROM File Generator Window")

After selecting "OK," you will be greeted with a window instructing you to add files.  Click "OK," and select your bit file from the file menu.  This is the only file we need to add.  All we have to do now, is go to the bottom left menu and double click "Generate File."  

After going back to the boundary scan, select the first device, the flash memory device, and click the "Assign New Configuration File" and select the PROM file you just made.  From the bottom left menu, double click Program, and it should program the flash memory.  

IMPORTANT: The FPGA will NOT run the newly programmed file automatically!  You must power cycle the FPGA for it to use the new configuration file!  

## Ping Test 

To ping the Captan, simply type `ping 192.168.133.2` into the console window.  Please note that the ping test will likely fail if the current version of the software is not properly flashed to the FPGA.  

## Setting up the Control Software

The Captan control software comes in three parts: captan master, captan controller, and captan gui.  This makes it easy to control multiple captan boards on multiple computers all on the same network.  They each need to be started in order with different settings.  I will walk you through them here.  

### Captan Master

1. Launch the application. (captan_software_1_8_1\CAPTAN_SOFTWARE_\CAPTAN_Global_Master\CAPTAN_Global_Master) 

### Captan Controller

1. Launch application. (captan_software_1_8_1\CAPTAN_SOFTWARE_\CAPTAN_Controller\CAPTAN_Subnet_Master)
2. In the Global Master IP field put the IP address of your computer (or of the computer running the global master software.  For the purpose of this tutorial, this should be your computer.  )
3. Click GM Connect to connect to the global master.  
4. In the CAPTAN Address field put the last byte of the IP address of your board.  The default for your board is 2.  
5. Click CAPTAN Connect to connect the captan board.  
6. Verification:  
    1. GM Communication Thread Status should contain the line: GM Connection Established.  
    2. Captan Receive Thread Status  should contain the line: Captan Read Socket Initialized.  
  
If the connections return errors, double check the addresses and make sure that the global master software is running.  If it cannot connect with the captan board, try pinging it to make sure everything is working.  If that doesn't work, reset the board using the onboard reset button located just to the outside of the J3 connector.  

### Captan GUI

1. Launch application.  (captan_software_1_8_1\CAPTAN_SOFTWARE_\CAPTAN_Gui\CAPTAN_Gui)
2. In the Global Master IP field, put the IP address of your computer (or whatever computer the global master software is running on).
3. Click Connect as Master.  This gives you control over the captan board.  Clicking Connect to Chat will let you see what is going on and communicate with other operators using the gui on other computers but you won't be able to send commands to the captan board.
4. Select the captan board from the list.  The name of the board can be found from the captan controller software by looking at the text field labeled "CAPTAN Controller Alias".
5. You should see a line on the console in the bottom left corner that says "Connected as Master".  

## Setting Registers

From here on out, most of the setup should be done.  As a last check to make sure everything is verified, it is a good idea to set some registers in the FPGA.  To do this, open the Captan GUI application and select the name of your captan board from the menu on the left.  In the bottom left corner of the window, there should be a field for the address space, and one for the data.  The address space resource below gives the address space for the ethernet interface on the captan board.  The block number and address both go in the address space, and, if writing data, the data goes in the data field.  For example, to read the firmware version, type 0000000100000064 into the address space and click read.  The result should show up in hex in the console in the bottom left part of the window.  

![alt text](https://github.com/CollinBradford/IntroducingCaptan/blob/master/ConnectingEverything/Images/Captan%20GUI.png "Captan GUI")


### Resources

1. OtS Ethernet Interface User's Guide:  https://docs.google.com/document/d/1i3Z07n8Jq78NwgUFdjAv2sLGhH4rWjHeYEScAWBzSyw/edit
2. OtS Interface Address Space:  http://otsdaq.fnal.gov/docs/oei_address_space.html


