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

- A usbip_usb_device struct is sent through to usbip_net_xmit in the recv_request_import function in usbipd.c
- The usbip_usb_device struct is defined in usbip_common.h 

struct usbip_usb_device {
	char path[SYSFS_PATH_MAX];
	char busid[SYSFS_BUS_ID_SIZE];

	uint32_t busnum;
	uint32_t devnum;
	uint32_t speed;

	uint16_t idVendor;
	uint16_t idProduct;
	uint16_t bcdDevice;

	uint8_t bDeviceClass;
	uint8_t bDeviceSubClass;
	uint8_t bDeviceProtocol;
	uint8_t bConfigurationValue;
	uint8_t bNumConfigurations;
	uint8_t bNumInterfaces;
} __attribute__((packed));

- Also a struct for usbip_exported_device defined in usbip_host_common.h

struct usbip_exported_device {
    struct udev_device *sudev;     // Pointer to the udev device structure representing the USB device
    int32_t status;                // Status of the exported USB device
    struct pdu_udev udev;          // Nested struct containing detailed information about the USB device
    struct list_head node;         // List node for managing a list of exported devices
    struct usbip_usb_interface uinf[]; // Array of USB interfaces supported by the device
};

- The stub_recv_cmd_submit deals with an USBIP_CMD_SUBMIT which sends the usb traffic from the exported device 
# To do
- Build and run an example with two virtual machines on top of seL4
- Set up a memory region which the two virtual machines can read and write to
- Set up a circular buffer to send the USB traffic between the two VM's -> can use the locking mechanism's outlined in the case study

# To do 16/07/2024
- Write a USB device (OP_REP_IMPORT) from one vitual guest 

# USB routing 

- Replace the socket functions sys/socket.h with implementations to write and read from the memory mapped io
- Can't change the send prototype in the socket.h file however can change the implementations in socket.c to write and read to the memory mapped io  

# Problem with unpacking root filesystem

- "Unpacking initramfs.." is the log line where the unpacking of the filesystem
- "Initramfs unpacking failed: write error" is the failure line
- Can build without initramfs however don't know the implications of doing this.