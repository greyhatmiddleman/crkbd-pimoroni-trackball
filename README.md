# Crkbd with Pimoroni Trackball
Portable keyboard and mouse companion.

![alt fullview][fullview]

This was inspired by [foureight84](https://github.com/foureight84/sofle-keyboard-pimoroni)'s sofle and [vlukash](https://github.com/vlukash/corne-trackpad)


## Materials
- [crkbd classic](https://www.littlekeyboards.com/collections/corne-pcb-kits/products/crkbd-classic-essentials-kit)
- [corne analyst case](https://www.littlekeyboards.com/collections/corne-cases/products/corne-analyst-case)
- [mbk choc low profile keycaps](https://www.littlekeyboards.com/collections/keycaps/products/mbk-choc-low-profile-keycaps)
- [kailh choc low profile switches (brown)](https://www.littlekeyboards.com/collections/keyboard-switches/products/kailh-choc-low-profile-switches)
- [mill max ultra lowprofile sockets](https://www.littlekeyboards.com/collections/miscellaneous/products/mill-max-ultra-low-profile-sockets)
- [sparkfun pro micro usb-c](https://www.sparkfun.com/products/15795)
- [pimoroni trackball](https://shop.pimoroni.com/products/trackball-breakout)


## Build
I won't go over the details of the crkbd base build there is a lot of youtube clips and the orginal [foostan](https://github.com/foostan/crkbd) build is detailed.

The only thing special is I used the sparkfun pro micro usb-c controller and qwiic port to connect the pimoroni trackball, shown below.


### Qwiic Connection
Pimoroni Trackball with qwiic connection:
![alt trackball-qwiic][trackball-qwiic]

Trackball connected to the controller:
![alt controller-with-trackball-qwiic][controller-with-trackball-qwiic]

Controller connected to the board:
![alt connection][connection]

Right Hand Master:
![alt right-hand-master][right-hand-master]


## QMK firmware
I'm in no means a pro C/C++ developer and I'm sure once you see the code you could probably find improvements, but most of the development was originally from [foureight84](https://github.com/foureight84/qmk_firmware/tree/sofle_foureight84/keyboards/sofle/keymaps/foureight84), [drashna](https://github.com/drashna) and [sevanteri](https://github.com/sevanteri/qmk_firmware/tree/master/users/sevanteri).

Currently the common crkbd doesn't support i2c for the slip keyboard communication, so I had to build the firmware similar to [vlukash](https://github.com/qmk/qmk_firmware/tree/master/keyboards/crkbd/keymaps) trackpad approach by having a firmware for the right and left controllers. Basically the master keyboard, the one that has the usb connected, will have the working code for the pimoroni trackball. So you have to explicitly say which on is the master and enable the pimoroni trackball for that side only. The firmware does not include the OLED driver mainly because I haven't tested it yet. The LED in the trackball will change indicating which layer you are currently at.

Clone my firmware here:
[https://github.com/greyhatmiddleman/qmk_firmware.git](https://github.com/greyhatmiddleman/qmk_firmware.git)

```
git clone --branch greyhatmiddleman https://github.com/greyhatmiddleman/qmk_firmware.git
```

The firmware is located in ```keyboards/crkbd/rev1/common/keymaps/greyhatmiddleman_trackball_[left|right]```.

### Firmware Customization
For this example the trackball will be connected to the right hand which will be the master side.

Here you want to make sure the following:

- __Important__: Make sure that both the ```config.h``` and ```keymap.c``` are identical under the right and left folders
- In the ```config.h``` file make sure to define the master:
```
...
#define MASTER_RIGHT
...
```
- Change your keymaps accordingly, no need to change the code under ```pointing_device_task```, unless you added layers or change the names of the layers.
- Set the ```rules.mk``` based on the which hand is master and that has the trackball connected.
 -- For the __right hand__ ```rules.mk``` set the following:
 ```
 ...
 PIMORONI_TRACKBALL_ENABLE = yes
 ...
 ```
 -- For the __left hand__ ```rules.mk``` set the following:
 ```
 ...
 PIMORONI_TRACKBALL_ENABLE = no
 ...
 ```
- Verify all your changes and confirm that the ```config.h``` and ```keymap.c``` are the same in the right and left holders.
- Now you ready to flash the firmware.

### Flashing Firmware
Connect the usb to right hand controller and run the following:
```
qmk flash -kb crkbd/rev1/common -km greyhatmiddleman_trackball_right
```

Next connect the usb to the left hand controller and run the following:
```
qmk flash -kb crkbd/rev1/common -km greyhatmiddleman_trackball_left
```

You should be all set!


## Troubleshooting
- Confirm that the ```config.h``` and ```keymap.c``` are the same under the right and left folders.
- Ensure that you connected the trackball to the master hand, which would be defined under the ```config.h```
- The ```rules.mk``` should be different between the right and left folders. It should dictate which has the Pimoroni enabled which the other side should have it disabled.


## Future Improvements
- Convert the controllers using the Nice!Nano ones for wireless connectivity
- Add an OLED on the opposite side of trackball



[connection]: https://raw.githubusercontent.com/greyhatmiddleman/crkbd-pimoroni-trackball/main/images/connection.jpg
[controller-with-trackball-qwiic]: https://raw.githubusercontent.com/greyhatmiddleman/crkbd-pimoroni-trackball/main/images/controller-with-trackball-qwiic.jpg
[fullview]: https://raw.githubusercontent.com/greyhatmiddleman/crkbd-pimoroni-trackball/main/images/fullview.jpg
[right-hand-master]: https://raw.githubusercontent.com/greyhatmiddleman/crkbd-pimoroni-trackball/main/images/right-hand-master.jpg
[trackball-qwiic]: https://raw.githubusercontent.com/greyhatmiddleman/crkbd-pimoroni-trackball/main/images/trackball-qwiic.jpg
