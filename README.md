# Nexgo-K300

An investigation of the Nexgo (Shenzhen Technology Co.) K300. The K300 is a handheld POS terminal, integrating smartcard/mag-stripe/NFC readers, with GSM/Wifi connectivity and touchscreen/keypad interfaces providing a portable device to process customer transactions.

## Internals

The main core of the device appears to be a Quectel EC25 module which contains a Qualcomm mdm9607(?) and 256M of RAM & Flash.
Also:
* NXP TDA8035 smart card interface.
* C*Core CCM4202S (Fingerprint device). 

## Debug Console
The Quectel EC25 has a debug console UART. Test points are available for Rx/Tx, see pics. Console is 1.8V, running at 115200 baud. Output stops after SBL jumps to (presumably) Android bootload.

## USB_BOOT
The Quectel EC25 module 'pin' USB_BOOT is connected to another test point. Tied to VddEXT, the SOC stops booting in the PBL and awaits communication from a USB host. Running a suitable program the SOC Flash contents can be rewritten. One such program is Sahara:

```https://github.com/openpst/sahara.git```

To reflash a device a small sliver of code needs to be downloaded to the SOC first to handle the actual flash writes (and possibly other functions), and if the SOC has encryption enabled then this to must be encrypted.
