# Nexgo-K300

An investigation of the Nexgo (Shenzhen Technology Co.) K300. The K300 is a handheld POS terminal, integrating smartcard/mag-stripe/NFC readers, with GSM/Wifi connectivity and touchscreen/keypad interfaces providing a portable device to process customer transactions.

## Internals

The main core of the device appears to be a Quectel EC25 module which contains a Qualcomm mdm9607(?) and 256M RAM & 256M Flash.
Also:
* NXP TDA8035 smart card interface.
* C*Core CCM4202S (Fingerprint device). 

## Debug Console
The Quectel EC25 has a debug console UART. Test points are available for Rx/Tx, see pics. Console is 1.8V, running at 115200 baud. Unfortunately it's not that helpful, output stops after SBL jumps to Android bootload. 
```
Format: Log Type - Time(microsec) - Message - Optional Info
Log Type: B - Since Boot(Power On Reset),  D - Delta,  S - Statistic
S - QC_IMAGE_VERSION_STRING=BOOT.BF.3.1.2-00091
S - IMAGE_VARIANT_STRING=LAATANAZA
S - OEM_IMAGE_VERSION_STRING=SH-SW-S-CarltonP
S - Boot Config, 0x000002e1
B -      1216 - PBL, Start
B -      3723 - bootable_media_detect_entry, Start
B -      4433 - bootable_media_detect_success, Start
B -      4437 - elf_loader_entry, Start
B -      6680 - auth_hash_seg_entry, Start
B -     30134 - auth_hash_seg_exit, Start
B -     78742 - elf_segs_hash_verify_entry, Start
B -    122592 - PBL, End
B -    109647 - SBL1, Start
B -    158783 - pm_device_init, Start
B -    175832 - PM_SET_VAL:Skip
D -     15707 - pm_device_init, Delta
B -    176839 - boot_config_data_table_init, Start
D -    174917 - boot_config_data_table_init, Delta - (420 Bytes)
B -    355294 - CDT version:3,Platform ID:8,Major ID:1,Minor ID:0,Subtype:0
B -    361486 - sbl1_ddr_set_params, Start
B -    365298 - Pre_DDR_clock_init, Start
D -       244 - Pre_DDR_clock_init, Delta
D -         0 - sbl1_ddr_set_params, Delta
B -    578951 - pm_driver_init, Start
D -      4666 - pm_driver_init, Delta
B -    583678 - cpr_init, Start
D -       122 - cpr_init, Delta
B -    586393 - cpr_cx_mx_apc_vol_update, Start
D -        61 - cpr_cx_mx_apc_vol_update, Delta
B -    601124 - sbl1_qhsusb_al_do_fast_enum, Start
D -         0 - sbl1_qhsusb_al_do_fast_enum, Delta
B -    604235 - clock_init, Start
D -       152 - clock_init, Delta
B -    610030 - boot_flash_init, Start
D -     27206 - boot_flash_init, Delta
B -    641171 - Image Load, Start
D -    108366 - QSEE Image Loaded, Delta - (489460 Bytes)
D -       213 - boot_pm_post_tz_device_init, Delta
B -    754478 - boot smem flash uid, sucess!!!!! 

B -    757467 - sbl1_efs_handle_cookies, Start
D -         0 - sbl1_efs_handle_cookies, Delta
B -    765733 - Devcfg Partition does not exist
B -    770003 - Image Load, Start
D -       427 - SEC Image Loaded, Delta - (2048 Bytes)
B -    777933 - Image Load, Start
D -     61091 - RPM Image Loaded, Delta - (153520 Bytes)
B -    839604 - Image Load, Start
D -    138165 - APPSBL Image Loaded, Delta - (729880 Bytes)
B -    977799 - QSEE Execution, Start
D -       213 - QSEE Execution, Delta
B -    983503 - SBL1, End
D -    876204 - SBL1, Delta
S - Throughput, 3000 KB/s  (1375392 Bytes,  355959 us)
S - DDR Frequency, 240 MHz
Android Bootloader - UART_DM Initialized!!!
```
In case you're wondering about the picture, I haven't got a USB<->UART capable of operating at 1.8V, what I do have is a Bus Pirate and a level convertor for my ROM programmer so it can do 1.8V SPI ROMS, a little hacky but it works! :)

## USB_BOOT
The Quectel EC25 module 'pin' USB_BOOT is connected to another test point. Tied to VddEXT, the SOC stops booting in the PBL and awaits communication from a USB connected host. Running a suitable program the SOC Flash contents can be rewritten. One such program is Sahara:

```https://github.com/openpst/sahara.git```

To reflash a device a some code needs to be downloaded to the SOC first to handle the actual flash writes (and possibly other functions), and if the SOC has encryption enabled then this to must be encrypted. :( No luck so far finding one that works.
