---
layout: post
title: Things about BLE
parent: IoT
nav_order: 1
date: 2019-12-27
---

# Things about BLE

## 1、Bluetooth

[(Bluetooth®)](https://en.wikipedia.org/wiki/Bluetooth) is a wireless technology standard used for exchanging data between fixed and mobile devices over short distances, and building personal area networks.

Bluetooth is managed by the Bluetooth Special Interest Group (SIG). The Bluetooth SIG oversees development of the specification, manages the qualification program, and protects the trademarks. A manufacturer must meet Bluetooth SIG standards to market it as a Bluetooth device.

The Bluetooth SIG completed the Bluetooth Core Specification version 4.0 (called Bluetooth Smart) and has been adopted as of 30 June 2010. It includes Classic Bluetooth, Bluetooth high speed and Bluetooth Low Energy (BLE) protocols. Bluetooth high speed is based on Wi-Fi, and Classic Bluetooth consists of legacy Bluetooth protocols.

### Bluetooth Low Energy

[BLE(Bluetooth Low Energy)](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) is a subset of Bluetooth v4.0 with an entirely new protocol stack for rapid build-up of simple links. As an alternative to the Bluetooth standard protocols that were introduced in Bluetooth v1.0 to v3.0, it is aimed at very low power applications powered by a coin cell.

General improvements in version 4.0 include the changes necessary to facilitate BLE modes, as well the Generic Attribute Profile (GATT) and Security Manager (SM) services with AES Encryption. Bluetooth Low Energy operates at same frequencies, from 2.402 to 2.480 GHz, and uses 2 MHz spacing, which accommodates 40 channels.

Compared to Classic Bluetooth, BLE is intended to provide considerably reduced power consumption and cost. Mobile operating systems including iOS, Android, Windows Phone and BlackBerry, as well as macOS, Linux, Windows 8 and Windows 10, natively support Bluetooth Low Energy.

Compared to Classic Bluetooth, Bluetooth Low Energy is intended to provide considerably reduced power consumption and cost while maintaining a similar communication range. In terms of lengthening the battery life of Bluetooth devices, BLE represents a significant progression.

- In a single-mode implementation, only the low energy protocol stack is implemented. Dialog Semiconductor, STMicroelectronics, AMICCOM, CSR, Nordic Semiconductor and Texas Instruments have released single mode Bluetooth Low Energy solutions.
- In a dual-mode implementation, Bluetooth Smart functionality is integrated into an existing Classic Bluetooth controller. As of March 2011, the following semiconductor companies have announced the availability of chips meeting the standard: Qualcomm-Atheros, CSR, Broadcom and Texas Instruments. The compliant architecture shares all of Classic Bluetooth's existing radio and functionality resulting in a negligible cost increase compared to Classic Bluetooth.

### Bluetooth BR/EDR

Bluetooth operates at frequencies between 2.402 and 2.480 GHz, or 2.400 and 2.4835 GHz including guard bands 2 MHz wide at the bottom end and 3.5 MHz wide at the top. This is in the globally unlicensed (but not unregulated) industrial, scientific and medical (ISM) 2.4 GHz short-range radio frequency band.

Bluetooth divides transmitted data into packets, and transmits each packet on one of 79 designated Bluetooth channels. Each channel has a bandwidth of 1 MHz. It usually performs 1600 hops per second, with adaptive frequency-hopping (AFH) enabled.

Originally, Gaussian frequency-shift keying (GFSK) modulation was the only modulation scheme available. Since the introduction of Bluetooth 2.0+EDR, π/4-DQPSK (differential quadrature phase-shift keying) and 8-DPSK modulation may also be used between compatible devices. Devices functioning with GFSK are said to be operating in basic rate (BR) mode where an instantaneous bit rate of 1 Mbit/s is possible.

The term Enhanced Data Rate (EDR) is used to describe π/4-DPSK and 8-DPSK schemes, each giving 2 and 3 Mbit/s respectively. The combination of these (BR and EDR) modes in Bluetooth radio technology is classified as a BR/EDR radio.

### Bluetooth Smart

BLE chip designs allow for two types of implementation, dual-mode, single-mode and enhanced past versions. In late 2011, new logos "Bluetooth Smart Ready" for hosts and "Bluetooth Smart" for sensors were introduced as the general-public face of BLE.

In 2011, the Bluetooth SIG announced the Bluetooth Smart logo so as to clarify compatibility between the new low energy devices and other Bluetooth devices.

- Bluetooth Smart Ready indicates a dual-mode device compatible with both classic and low energy peripherals.
- Bluetooth Smart indicates a low energy-only device which requires either a Smart Ready or another Smart device in order to function.

The Bluetooth SIG identifies a number of markets for low energy technology, particularly in the smart home, health, sport and fitness sectors. Cited advantages include:

- low power requirements, operating for "months or years" on a button cell
- small size and low cost
- compatibility with a large installed base of mobile phones, tablets and computers

## 2、Software model

To use Bluetooth wireless technology, a device must be able to interpret certain Bluetooth profiles, which are definitions of possible applications and specify general behaviors that Bluetooth-enabled devices use to communicate with other Bluetooth devices. These profiles include settings to parameterize and to control the communication from the start. Adherence to profiles saves the time for transmitting the parameters anew before the bi-directional link becomes effective. There are a wide range of Bluetooth profiles that describe many different types of applications or use cases for devices.

#### 2.1 GATT (Generic Attribute Profile)

- Generic Attribute Profile can be used by APP or other config files.
- Provide general interface for data exchange between server and client.

BLE applications are based on GATT, and it descripts how BLE works.

#### 2.2  ATT (Attribute Protocol)

- It defines the data structure
- Subset of GATT
- ATT is an optimization for BLE device, to use as few bytes as possible.
- Each attribute are identified by UUID

#### 2.3 Characteristic

- A data structure
- Identified by [UUID](https://www.bluetooth.com/specifications/gatt/characteristics/)
- Includes 0 ... N descriptors
- Configurable read and write permissions
- Optional notification or indication

Descriptor：attributes of Characteristic. (Range of value, unit of value, or descriptions...)

#### 2.4 Service

Device that provides data, should host a service(identified by UUID, SIG [definition](https://www.bluetooth.com/specifications/gatt/services/)). In the service there are a few Characteristics that hold data for other devices.

Device that cosumes data (client) connects to device that provides data (server). The client get the services list of server, and characteristics of service can be read or written then. Client could also enable notification of characteristics, and once characteristics change, client will be notified.

## 3. Central vs Peripheral

Bluetooth is a packet-based protocol with a master/slave architecture. One master may communicate with up to seven slaves in a piconet. All devices share the master's clock.

The Bluetooth Core Specification provides for the connection of two or more piconets to form a scatternet, in which certain devices simultaneously play the master role in one piconet and the slave role in another.

Pairing is MUST for Bluetooth BR/EDR, but OPTIONAL for Bluetooth Smart. Generally if an audio device(e.g. headset) is specified to support Bluetooth 4.x, 4.x BR/EDR is supported, not Bluetooth Smart. Generally Bluetooth headsets have to be paired before use.

General connection sequences:

- Peripheral device start advertising once started.
- Central device scans devices around, and find the peripheral device, get its MAC.
- Central device starts the connection by MAC.
- Connection done.

Comments:

Peripheral device is limited to one connection, but central device can keep limited number of connections.

## 4. Server vs Client

*Server*: data provider

*Client*: data consumer

Both central and peripheral devices can be server or client. Usually peripheral device plays server and central device plays client. After connected, client can get data from server.

To response to request of data, server should create GattServer once started.

Sequence of data exchange：

- Client gets the services list of server, and find the service requested by UUID.
- Get the Characteristic from service by UUID.
- Now client could read or write the value of specific Characteristic, and edit the  configuration (i.e. descriptors) of Characteristic.
- Once value changed，server will notify or indicate the connected and registered clients.

Comments：

1. Notification/Indication are enabled by clients.
2. Notification/Indication is a descriptor of Characteristic usually, and can be read or written.
3. Indication requires the client to reply after indication received, but notification not.

I created an Android Demo. Android phone(Central) connects an Android watch, and once user click the APP in watch, a message will be sent to Android phone. In case you're interested:

- [Central Demo](https://github.com/OkieLe/BtComm)
- [Peripheral Demo](https://github.com/OkieLe/BtWear)
