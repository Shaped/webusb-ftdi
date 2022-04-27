# webusb-ftdi.js
(C) 2021 Jai B / Shaped Technologies

This is a basic WebUSB FTDI driver. It works on Chrome and Chromium-based browsers (currently Firefox doesn't support WebUSB). It also works on Android devices under Chrome. This will allow you to communicate with existing FTDI devices in a browser using the WebUSB API (the device does NOT need to be WebUSB aware - any old FTDI works).

I wrote this because I wanted to interface with different automotive devices on my Android devices. On Windows, it's generally much easier to use the WebSerial API instead.

Under Windows, if any FTDI driver has been installed, you can use `zadig` to switch to the `WinUSB driver` in order for the browser to be able to access the USB device. This will prevent the device from being used as a serial (COM) port until you've switched the driver back.

Under Android, it should just work when a FTDI device is plugged into USB OTG. I haven't tested a ton of devices but it has worked everywhere I have tested it.

I tried to get details about the protocol from FTDI for this project (as the Linux driver states they helped them out for it) but when I mentioned that I planned on releasing the driver as open source, I stopped receiving emails from FTDI unfortunately. The Linux driver was a great inspiration and help for this so I want to give a shout out to the developers of the Linux FTDI driver.

This will allow you to send and receive TTL data over an FTDI device. This is not a complete driver and this advanced features are not implemented although I'm sure most use cases just simply need communication. By default, it currently sets the latency timer to 1 though you'll be able to change this in the future. I'm also planning on allowing changing of some other options if I find anyone needs it.

# usage

This is just some basic usage. This driver is `alpha` quality; thus the API is subject to change, I will probably rename and change some sh~t. Be warned!

    // get a device object
    var device = new WebUSBSerialDevice({
        overridePortSettings: false,
        // these are the defaults, this config is only used if above is true
        baud: 9600,
        bits: 8,
        stop: 1,
        parity: false
    });
    
    // get available ports (ftdi devices)
    console.log(device.getAvailablePorts());
    
    // shows browser request for usb device
    var port = await device.requestNewPort();
    
    try {
        // try to connect, connect receives two parameters: data callback and error callback
        await port.connect((data) => {
            // this is data callback, print data to console
            console.log(data);
            // send/repeat received data back to port after 10ms
            setTimeout(()=>{
                port.send(data);
            },10);
        }, (error) => {
	    // called if error receiving data
            console.warn("Error receiving data: " + error)
        });
    } catch (e) {
        // called if can't get a port
        console.warn("Error connecting to port: " + e.error)
    }
    


