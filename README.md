# BLE03 - CC3301 based BLE 5.4 / Wi-Fi 6 Module 

BLE03 is an MLAB Bluetooth and Wifi connectivity module built around TI CC3301. It provides 2.4 GHz Wi-Fi 6 (802.11ax) and Bluetooth Low Energy 5.4 as a companion IC intended to be controlled by an external MCU/MPU host over SDIO (4-bit) (or SPI on the chip), plus UART HCI for BLE.  

![BLE03 top view](doc/gen/img/BLE03-top.png)
![BLE03 bottom view](doc/gen/img/BLE03-bottom.png)

## Key features

* **Wi-Fi**
  * 2.4 GHz Wi-Fi 6 (802.11ax), 20 MHz, 1×1 SISO 
  * Backward compatibility: 802.11 a/b/g/n/ac 
  * HW crypto: WPA2 / WPA3 
  * Up to **+20.5 dBm** TX power (integrated PA) 
  * Application throughput up to **50 Mbps** 
* **Bluetooth**
  * Bluetooth Low Energy **5.4** (CC3301) 
  * HCI transport via **UART** (or shared SDIO on the chip) 
* **Module implementation highlights**
  * Onboard **1.8 V regulator** (MIC5504-1.8) fed from **3.3–5 V** input 
  * Bidirectional level shifting to **3.3 V logic** using **TXS0104 / TXS0108** for host interfaces 
  * 2.4 GHz RF routed through a small front-end / filter to a MCX coax antenna connector

## Hardware overview

The design follows CC3301 reference integration principles: external 40 MHz fast clock (XTAL), external 32.768 kHz slow clock, SDIO host interface, and RF port brought out to an external antenna connector.  

### Power

* **Input**: `3.3V` to module power header **J2** 
* **Onboard regulation**: MIC5504-1.8 generates `+1V8` for CC3301 domains and level shifters 
* **CC3301 rails (chip-level reference)**:
  * VMAIN / VDDA / VIO / VPP: **1.8 V** nominal 
  * VPA: **3.3 V** nominal (PA supply) 

**Power-up sequencing note (chip requirement):** all supplies must be stable before releasing `nRESET` high; keep `nRESET` low at least 10 µs after supplies stabilize. 

## Interfaces

### SDIO host (Wi-Fi)

CC3301 supports **4-bit SDIO** (up to 52 MHz high speed) or SPI at chip level. BLE03 breaks out SDIO signals through a level shifter to the host connector.  

### UART HCI (BLE)

BLE HCI UART is routed through a level shifter to the host connector:

* `UART_TX`, `UART_RX`, `UART_CTS`, `UART_RTS`  

## Software integration notes

BLE03 is a **companion IC**: the host is responsible for overall application logic. Typical integration:

* **Wi-Fi**: host communicates over SDIO and runs the networking stack (Linux/RTOS use-cases are explicitly targeted by CC330x). 
* **BLE**: host uses UART HCI to a BLE stack/controller interface. 
    
