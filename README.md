# CurieBLEServiceData
Modified Arduino 101 CurieBLE library components to support BLE Service Data.  This repo is OBSOLETED by [my support-eddystone-url pull request](https://github.com/01org/corelibs-arduino101/pull/146).

The 1.0.4 version of the CurieBLE library for Arduino 101 doesn't support [Eddystone](https://github.com/google/eddystone) beacon advertising for two reasons:

1. It uses the BLE code for Incomplete Service UUID list instead of the Eddystone-required Complete Service UUID list (0x02 instead of 0x03).
2. It doesn't support BLE Service Data, which is required by Eddystone.

This repository contains changed files necessary for the 1.0.4 CurieBLE library to support BLE Advertising of Eddystone URL format.  I apologise in advance to folks who expect a properly-constructed Fork and mods.
The modifications from the original code are marked with the initials BHN.

Modifications are Copyright (c) 2016 Bradford Needham, North Plains, Oregon
@bneedhamia, [https://www.needhamia.com](https://www.needhamia.com)
Licensed under the LGPL 2.1 license, a copy of which should have been provided with these files.

To install:

1. Find the folder of the CurieBLE library on your system.  On Linux, it's ~/.arduino15/packages/Intel/hardware/arc32/1.0.4/libraries/CurieBle
2. Backup the existing keywords.txt, src/BLEPeripheral.h, and src/BLEPeripheral.cpp in case these modified versions don't do what you want.
2. Copy keywords.txt from this repo to that folder, overwriting the existing keywords.txt
3. Copy BLEPeriperal.h and BLEPeripheral.cpp to the src subfolder of that folder, overwriting the existing BLEPeripheral.h and BLEPeripheral.cpp
4. Restart your Arduino IDE.

**NOTE**: This code has no warranty. I have done basic testing of the modifications, but nowhere near the testing that would be required of a product change to the CurieBLE library.  These modifications are a hobby project of mine, for my own use. I hope others find the changes useful.

The changes:
* I added BLEPeripheral functions to read the loaded Advertising block.  This feature is useful for debugging BLE Advertising problems.
* I added a BLEPeripheral function to put a BLE Service Data block into the Advertising block.
* I modified BLEPeripheral::_advDataInit() in two ways:
  * It uses BLE_ADV_TYPE_COMP_16_UUID instead of BLE_ADV_TYPE_INC_16_UUID, because Eddystone uses the former.  I don't know the impact of this change on non-Advertising CurieBLE functionality.
  * It supports an optional Service Data Block in the Advertising block.

## Added BLEPeripheral functions

**void setAdvertisedServiceData(const char* serviceDataUuid, uint8_t* serviceData, uint8_t serviceDataLength);**

Sets the Service Data that the BLE Peripheral Device advertises.

*serviceDataUuid* 16-bit Service UUID for this Service Data (in string form). Must match the UUID parameter of setAdvertisedServiceUuid(). To fit into BLE_MAX_ADV_SIZE, the UUID must be a 16-bit UUID.

*serviceData* binary array of Service Data.

*serviceDataLength* length (bytes) of serviceData[]

NOTE: the entire advertising packet must be no more than BLE_MAX_ADV_SIZE bytes, which is currently 31. This likely means that if you use Service Data there will not be room for a Local Name.

NOTE: if serviceDataUuid isn't 16-bits long, or if serviceDataLength won't fit in the advertising block, the service data will silently not be copied into the advertising block.

**uint8_t getAdvertisingLength();**

Returns the number of bytes in the advertising block. Useful for debugging advertising problems.

NOTE: Call only after calling BLEPeripheral.begin().

**uint8_t* getAdvertising();**

Returns a pointer to the advertising block, as an array of bytes of length getAdvertisingLength(). Useful for debugging advertising problems.

NOTE: Call only after calling BLEPeripheral.begin().

## Example code
See [my Curie Eddystone URL repo](https://github.com/bneedhamia/CurieEddystoneURL) for an example of how to use this modified CurieBLE library to create an Arduino 101 Eddystone beacon.


