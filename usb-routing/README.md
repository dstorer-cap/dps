# USB routing notes
- Work out how the usbip protocol links into the usb routing 
- Do i still need to find out where the devices have been probed and put a hook on them 
- Replace usb/ip with memory mapped io 

# Notes on usbip code
- usbip_net_xmit is used to sent URB packets with these parameter:
sockfd: The socket file descriptor.
buff: The buffer containing the data to send or receive.
bufflen: The length of the buffer.
sending: A flag indicating whether to send (1) or receive (0) data.
- Need to work out how the usbip protocol makes the usbip packet 

# To do
- Build and run an example with two virtual machines on top of seL4
- Set up a memory region which the two virtual machines can read and write to
- Set up a circular buffer to send the USB traffic between the two VM's -> can use the locking mechanism's outlined in the case study
