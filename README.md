# CVG Footpedal

This device transforms a usb footpedal into a wireless (bluetooth) mouse.
The usb footpedal reports as a keyboard with keys: ["a", "b", "c"]).
The device communicates with the keyboard and when connected to a
bluetooth host identifies as an HID compliant mouse sends pedal presses
as mouse clicks (left, middle, and right).

## Reading refernece 
- [Dev Board](https://documentation.espressif.com/projects/esp-dev-kits/en/latest/esp32s3/esp32-s3-usb-otg/user_guide.html?title=ESP32-S3-USB-OTG%20User%20Guide#getting-started)

## Software/Enviornment Setup for CLI 
### Linux packages needed 
```bash
sudo apt-get install git wget make libncurses-dev flex bison gperf python python-serial
```


### Loading software onto the ESP device 
- On your host machine/laptop/workstation, use the following to install `ESP-IDF Installation Manager (EIM)` when using 
```bash
# Add the Espressif APT repository
echo "deb [trusted=yes] https://dl.espressif.com/dl/eim/apt/ stable main" | sudo tee /etc/apt/sources.list.d/espressif.list
sudo apt update
# Install CLI only
sudo apt install eim-cli   
eim install
```
- To activate ESP-IDF virtual enviornment 
	- Note : replace `/home/utsw-bmen-laptop` with  
```bash
source "$HOME/.espressif/tools/activate_idf_v6.0.2.sh"
```

- To ensure ESP-IDF submodules are updated (OPTIONAL) 
	- Enter ESP-IDF repo: Example `cd~/.espressif/<version>/esp-idf`
	- Check sudmobudle status by running `git submodule status`
		- Note : The EIM install already conducts a `git pull` on all the submodules
			- Any lines starting with `-` means the submodule HASN'T been initialized 
			- A leading `+` means it's checked out but not at the commit the parent repo expects 
		- Any steps taken after this is to ensure the submodules are updated 
	- To update submodules explicitly 
		- Run : `git submodule update --init --recursive`  




## Device Setup

### Software Required

> See the [Getting Started Guide](https://idf.espressif.com/) for full
steps to configure and use ESP-IDF to build projects.

### Hardware Required

- ESP32 SoC DEV BOARD [ESP32-S3-USB-OTG](https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32s3/esp32-s3-usb-otg/index.html)
- A micro-usb cable for power supply and programming

| Components             | Functions                                                                            | Cost   | Purchase Link                                                                                                                                                                                              |
| ---------------------- | ------------------------------------------------------------------------------------ | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| USB Foot Pedal         | A hands-free input device that medical staff are familiar with                       | ~$40   | [https://www.amazon.com/Programmable-Multifunctional-Ergonomic-Equipment-photoelectric/dp/B0B4SP6KCF](https://www.amazon.com/Programmable-Multifunctional-Ergonomic-Equipment-photoelectric/dp/B0B4SP6KCF) |
| ESP32-S3-USB-OTG           | Microcontroller with integrated peripherals for USB host, BLE, and battery charging. | $32.45 | [https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-USB-OTG/15822449](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-USB-OTG/15822449)                           |
| Li-Ion Polymer battery | 3.7V, 1200mAh battery to power the device                                            | $9.95  | [https://www.adafruit.com/product/258](https://www.adafruit.com/product/258)                                                                                                                               |
| 3D printed enclosure   | device protection                                                                    | \-     | n/a                                                                                                                                                                                                        |

### TODO: Assembly

1. assembly instructions
2. picture of assembled device

### Build and Flash

To determine ESP32 device path on `LINUX`  
Step 1) Unplug ESP32 device from host machine
Step 2) Use the following to determine ESP32 device path
	- In this instance, the device path is : `/dev/ttyACM0` 
	```bash
	dmesg: read kernel buffer failed: Operation not permitted
	(venv) utsw-bmen-laptop@utswbmenlaptop-UX330UAK:~/.espressif/v6.0.2/esp-idf$ sudo dmesg | tail -20
	[sudo] password for utsw-bmen-laptop: 
	[426303.336237] usb 1-2: SerialNumber: F4:12:FA:9B:65:C0
	[426303.339900] cdc_acm 1-2:1.0: ttyACM0: USB ACM device
	[426307.432695] usb 1-2: USB disconnect, device number 12
	[429267.513829] workqueue: delayed_fput hogged CPU for >10000us 4 times, consider switching to WQ_UNBOUND
	[432465.339809] usb 1-2: new full-speed USB device number 13 using xhci_hcd
	[432465.467314] usb 1-2: New USB device found, idVendor=303a, idProduct=1001, bcdDevice= 1.01
	[432465.467320] usb 1-2: New USB device strings: Mfr=1, Product=2, SerialNumber=3
	[432465.467323] usb 1-2: Product: USB JTAG/serial debug unit
	[432465.467325] usb 1-2: Manufacturer: Espressif
	[432465.467327] usb 1-2: SerialNumber: F4:12:FA:9B:65:C0
	[432465.471070] cdc_acm 1-2:1.0: ttyACM0: USB ACM device
	[432466.455287] usb 1-2: USB disconnect, device number 13
	[433163.939741] usb 1-2: new full-speed USB device number 14 using xhci_hcd
	[433164.068572] usb 1-2: New USB device found, idVendor=303a, idProduct=1001, bcdDevice= 1.01
	[433164.068584] usb 1-2: New USB device strings: Mfr=1, Product=2, SerialNumber=3
	[433164.068586] usb 1-2: Product: USB JTAG/serial debug unit
	[433164.068588] usb 1-2: Manufacturer: Espressif
	[433164.068590] usb 1-2: SerialNumber: F4:12:FA:9B:65:C0
	[433164.072040] cdc_acm 1-2:1.0: ttyACM0: USB ACM device
	[433164.627732] usb 1-2: USB disconnect, device number 14
	```

## Reprogramming
- Take ESP32 out 
- Hold BOOT button
- Then plug in ESP32 board
- Use the following as a sanity check : `(venv) utsw-bmen-laptop@utswbmenlaptop-UX330UAK:~/esp32s3-footpedal-mouse$ idf.py -p /dev/ttyACM0 monitor`  
```sh
idf.py -p PORT flash monitor
idf.py -p /dev/ttyACM0 flash
```

> (To exit the serial monitor, type ``Ctrl-]``.)

### TODO: pairing instructions

## Troubleshooting

### TODO: any tips if the device is not connecting

### From Espressif

1. When using NimBLE stack, some iOS devices do not show the volume pop up.
To fix this, please set CONFIG_BT_NIMBLE_SM_LVL to value 2.
iOS needs Authenticated Pairing with Encryption to show up the pop ups.

## Example Output
### TODO: update with current debug log

```
I (353) BLE_INIT: Using main XTAL as clock source
I (363) BLE_INIT: Feature Config, ADV:1, BLE_50:1, DTM:1, SCAN:1, CCA:0, SMP:1, CONNECT:1
I (373) BLE_INIT: Bluetooth MAC: f4:12:fa:9b:64:2a
I (373) phy_init: phy_version 701,f4f1da3a,Mar  3 2025,15:50:10
I (413) HID_DEV_DEMO: setting ble device
I (423) HID_DEV_DEMO: BLE Host Task Started
I (423) NimBLE: GAP procedure initiated: stop advertising.

I (423) HID_DEV_BLE: START
I (423) main_task: Returned from app_main()
I (423) NimBLE: GAP procedure initiated: advertise; 
I (433) NimBLE: disc_mode=2
I (433) NimBLE:  adv_channel_map=0 own_addr_type=0 adv_filter_policy=0 adv_itvl_min=48 adv_itvl_max=80
I (443) NimBLE: 

I (5293) HID_DEV_BLE: CONNECT
I (5293) ESP_HID_GAP: connection established; status=0
I (5483) NimBLE: encryption change event; status=0 
########################################################################
BT hid mouse demo usage:
This auto clicks the left, right, and mouse wheel buttons
########################################################################

I (5503) main task: LEFT CLICK

I (5503) NimBLE: GATT procedure initiated: notify; 
I (5513) NimBLE: att_handle=37

I (5513) NimBLE: notify_tx event; conn_handle=1 attr_handle=37 status=0 is_indication=0
I (5523) ESP_HID_GAP: subscribe event; conn_handle=1 attr_handle=8 reason=3 prevn=0 curn=0 previ=0 curi=1

I (5533) main task: RELEASE
I (5533) NimBLE: GATT procedure initiated: notify; 
I (5533) NimBLE: att_handle=37

I (5543) NimBLE: notify_tx event; conn_handle=1 attr_handle=37 status=0 is_indication=0
I (5543) ESP_HID_GAP: subscribe event; conn_handle=1 attr_handle=18 reason=3 prevn=0 curn=1 previ=0 curi=0

I (5553) ESP_HID_GAP: subscribe event; conn_handle=1 attr_handle=37 reason=3 prevn=0 curn=1 previ=0 curi=0

I (5583) ESP_HID_GAP: mtu update event; conn_handle=1 cid=4 mtu=256
I (6543) main task: RIGHT CLICK

I (6543) NimBLE: GATT procedure initiated: notify; 
I (6543) NimBLE: att_handle=37

I (6543) NimBLE: notify_tx event; conn_handle=1 attr_handle=37 status=0 is_indication=0
I (6553) main task: RELEASE
I (6553) NimBLE: GATT procedure initiated: notify; 
I (6553) NimBLE: att_handle=37

I (6553) NimBLE: notify_tx event; conn_handle=1 attr_handle=37 status=0 is_indication=0
I (7563) main task: MIDDLE CLICK

I (7563) NimBLE: GATT procedure initiated: notify; 
I (7563) NimBLE: att_handle=37

I (7563) NimBLE: notify_tx event; conn_handle=1 attr_handle=37 status=0 is_indication=0
I (7573) main task: RELEASE
I (7573) NimBLE: GATT procedure initiated: notify; 
I (7573) NimBLE: att_handle=37
...

I (14713) NimBLE: notify_tx event; conn_handle=1 attr_handle=37 status=0 is_indication=0
I (15093) ESP_HID_GAP: subscribe event; conn_handle=1 attr_handle=8 reason=2 prevn=0 curn=0 previ=1 curi=0

I (15093) ESP_HID_GAP: subscribe event; conn_handle=1 attr_handle=18 reason=2 prevn=1 curn=0 previ=0 curi=0

I (15103) ESP_HID_GAP: subscribe event; conn_handle=1 attr_handle=37 reason=2 prevn=1 curn=0 previ=0 curi=0

I (15113) ESP_HID_GAP: disconnect; reason=531
I (15113) HID_DEV_BLE: DISCONNECT: UNKNOWN
I (15113) NimBLE: GAP procedure initiated: advertise; 
I (15123) NimBLE: disc_mode=2
I (15123) NimBLE:  adv_channel_map=0 own_addr_type=0 adv_filter_policy=0 adv_itvl_min=48 adv_itvl_max=80
I (15133) NimBLE: 
```


