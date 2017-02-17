#Getting Started With OtS Ethernet Interface

In this tutorial we will begin learning how to interface with our FPGA through the OtS Ethernet Interface.  

##Setting Everything Up  

To start out, we need to power up the board and ping it.  The powering part is pretty basic.  3.3v to the power connector near the JTag connector.  Keep in mind that the basic project runs at around 3 amps so not any simple power supply will work.  Once the board is powered, some of the lights on the ethernet interface chip should light up.  

## Connecting to the Board

The default IP address for the ethernet module is 192.168.133.2 .  To connect to the board, we need to verify that we have the right settings for our computer.  To check these, go to: Network and Sharing Center => \<network with captan\> => properties => select IPv4 => properties.  The IP address in the IP address field should be something like: 192.168.133.199 . You can choose the last number just as long as it isn't 2.  The subnet mask should be set to 255.255.255.0 . Once everything is right, click OK.  

## Flashing Firmware

Before we go any further, we need to know that the right firmware has been flashed to the board.  We will flash the firmware using the JTag connector from Xilinx.  The JTag socket on the Captan board is right by the power connector.  Once the Jtag cable is placed, we are good to go.  

There are two ways to flash the firmware to the captan board.  To get a good idea about the differences between the two, it is helpful to knowy a little bit more about FPGAs.  On startup, the FPGA will lay out the ligic circuit that is contained in an external flash memory.  If you program this external flash memory, the circuit will be persistent through a power cycle.  While the FPGA is on, it can be programmed through the JTag connector.  The FPGA will write the circuit, but it will not be stored in the flash memory.  Because of this, the logic circuit will revert to whatever circuit is written in the flash memory after a power cycle.  

There are two different programs to use when flashing firmware to the Captan.  The first uses ChipScope which is normally used for looking at signals inside the FPGA while it is running.  ChipScope can only be used to temporarily flash firmware to the FPGA while The second method, iMPACT, can be used for both.  Impact is a more dedicated piece of software meant for flashing firmware to Xilinx chips.  
### ChipScope

ChipScope is a logic analyzer meant for visualizing signals inside of the FPGA during runtime.  However, there is a convienient little feature that lets you program the FPGA for testing.  You cannot use ChipScope to program the flash memory.  To program the FPGA using ChipScope, ensure that your JTag connector is plugged in and that your device is powered on, and open the ChipScope analyzer. In the top left corner of the window, click the "open cable" button.  

![alt text](https://github.com/CollinBradford/IntroducingCaptan/blob/master/ConnectingEverything/Images/Open_Cable.png "Open Cable")

This results in a window that displays the devices connected to the JTag connection.  Select the FPGA from the list.  Next, from the top menue, select Device=>\<your device\>=>Configure.  This will open a window that allows you to select the bit file and CDC file.  The bit file will normally be in the same directory as your FPGA project.  You have the opportunity to import the CDC file asscociated with the pgoject, but it is not nessassary.  Importing it helps by naming all the signals you are analyzing.  Press the "OK" button, and ChipScope will flash the FPGA.  

### iMPACT

iMPACT is useful for flashing fimrware when you want to program the flash memory as well as the FPGA itself.  To use iMPACT, first ensure that your device is powered on and connected to the JTag cable and launch iMPACT.  In the left side menue, double click the top option titled "Boundry Scan"  

## Ping Test

Now we will try to connect to the captan board to verify that everything is working well.  Please note that the ping test will only work if you have the right firmware currently running on the Captan board.  If the FPGA board has not been flashed with a version of the firmware we are using, then this test will likely fail and you will have to flash the firmware before you can continue.  

First, open Wireshark and start monitoring the connection with your captan board.  This will let us see everything being sent and received to the board.  Next, open the command line and type `ping 192.168.133.2` .  If you get a response, the board is working and you can connect to it. Go ahead and look at Wireshark.  You can see the ping requests and the responses from the captan board.

If the ping test does not work, check your connections and IP addresses.  If that does not work, the next thing to try is flashing the firmware to the FPGA.  

## Setting up the Control Software

Now that we can connect to the captan board, we need to set up the control software which makes it easier to control the captan board.  The windows software can be downloaded from the resources folder.  

The software comes in three parts: captan master, captan controller, and captan gui.  This makes it easy to control multiple captan boards on multiple computers all on the same network.  They each need to be started in order with different settings.  I will walk you through them here.  

#### Captan Master

1. Launch the application. (captan_software_1_8_1\CAPTAN_SOFTWARE_\CAPTAN_Global_Master\CAPTAN_Global_Master) 

#### Captan Controller

1. Launch application. (captan_software_1_8_1\CAPTAN_SOFTWARE_\CAPTAN_Controller\CAPTAN_Subnet_Master)
2. In the Global Master IP field put the IP address of your computer (or of the computer running the global master software.  For the purpose of this tutorial, this should be your computer.  )
3. Click GM Connect to connect to the global master.  
4. In the CAPTAN Address field put the last byte of the IP address of your board.  The default for your board is 2.  
5. Click CAPTAN Connect to connect the captan board.  
6. Verification:  
    1. GM Communication Thread Status should contain the line: GM Connection Established.  
    2. Captan Receive Thread Status  should contain the line: Captan Read Socket Initialized.  
  
If the connections return errors, double check the addresses and make sure that the global master software is running.  If it cannot connect with the captan board, try pinging it to make sure everything is working.  If that doesn't work, reset the board using the onboard reset button located just to the outside of the J3 connector.  

#### Captan GUI

1. Launch application.  (captan_software_1_8_1\CAPTAN_SOFTWARE_\CAPTAN_Gui\CAPTAN_Gui)
2. In the Global Master IP field, put the IP address of your computer (or whatever computer the global master software is running on).
3. Click Connect as Master.  This gives you control over the captan board.  Clicking Connect to Chat will let you see what is going on and communicate with other operators using the gui on other computers but you won't be able to send commands to the captan board.
4. Select the captan board from the list.  The name of the board can be found from the captan controller software by looking at the text field labeled "CAPTAN Controller Alias".
5. You should see a line on the console in the bottom left corner that says "Connected as Master".  

#### Setting Registers

Alright, we have all that setup done.  Let's set some registers.  Go to the web resource for the address space. It's the second resource at the bottom of the page.  This defines the address space in the ethernet module and says what each register does.  Now, open wireshark so we can see what's going on behind the scenes.  Once everything is setup, we are going to read from the version register.  On the address space page, it's the second to last one with the field name "ETH_INTERFACE_VERSION".  We see that it is in block one at address 64.  To read from this register, go to the captan gui application.  On the right side of the page in the Basic Commands tab under CAPTAN Controls there is a an address field.  Type in the block number and the address so it says "0000000100000064".  Click Read.  In the console window, it should print the data that was read from the captan board.  Open wireshark and look at the recent transmissions.  There should be two UDP packages in addition to all the other stuff.  One should be from your computer to the captan board, the other from the captan board to the computer.  If you open these and look at the data, you will see what was transmitted to the board and what it sent back.  

Now, we are going to set the burst_mode register and read it back.  Change the address to reflect this register "0000000100000064" and put 1 in the data field.  Write the value and then read it back.  In the console you should receive a one back.  Change the one back to a zero and repeat the process and the register should change back to a zero.  

### Resources

1. OtS Ethernet Interface User's Guide:  https://docs.google.com/document/d/1i3Z07n8Jq78NwgUFdjAv2sLGhH4rWjHeYEScAWBzSyw/edit
2. OtS Interface Address Space:  http://otsdaq.fnal.gov/docs/oei_address_space.html


