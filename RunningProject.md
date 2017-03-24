## Software

Once the board is powered on, the Captan control software should be launched in accordance with the instructions in the "Setting Everything Up" tutorial.  In addition to the Captan software, the Python script for receiving the data should be started.  The Python script should take you through a few setup commands and then start taking data one window at a time.  These setup commands can be run by simply pressing enter.  Skipping any command can be accomplished by pressing 's' before running the command.  To exit the application, type e before a command, and to set the software to take data continuously, simply press 'c' before running any command.  

## Adjusting the Parameters

If everything has been successful thus far, then there should be some signal in the output window, even if this signal doesn't make much sense.  To set the sampling parameters on the FPGA, one needs to modify the runtime parameters register in the FPGA.  This is found at block 0, address 500. (so, type "0000000000000500" into the address field of the Captan Gui software) If you happen to be looking at the Captan Control software and it says that the Captan response timed out, don't worry.  The FPGA is simply too busy sending data and the registers should still be set right.  The runtime parameters register controls three aspects of the data sampling.  The data is formatted as follows: 

XXXXXXXXDDSSSSTT

X = Unused

D = The number of clock cycles between sending a set of samples Please note that several sets are combined in a packet before that packet is actually sent.  This is just a value used to slow down the speed of the data transmission because most computers, even though they are equipped with gigahertz ethernet, can't actually process the data with the Python script fast enough.  

S = Number of sample sets taken after a trigger.  There are 8 samples in each sample set.  

T = Trigger level.  This is the level at which the samples are compared with.  All the samples are compared and once the trigger level is exceeded by one of the samples, it triggers and starts recording data.  

### Parameter Example

 0000000020002540

 D = 20 (32 decimal) - The firmware will delay 32 clock cycles between sending each sample set to the buffer to be sent. 

 S = 25 (37 decimal) - After each trigger, 37 * 8 = 296 samples will be taken. 

 T = 40 (64 decimal) - When the signal reaches 64, it will trigger a read operation.  

 At these settings, the FPGA will analyse the signal until it reaches 65.  At this point, 296 samples will be recorded.  They will be sent off in groups of 4 with 32 clock cycles between each group.  

