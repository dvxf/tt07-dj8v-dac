<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

# How it works

DJ8 is a 8-bit CPU featuring:
* 8 x 8-bit register file
* 3-4 cycles per instruction
* 15-bit address bus
* 8-bit data bus
* 8-bit DAC based on [Tiny Tapeout Analog R2R DAC](https://github.com/mattvenn/tt06-analog-r2r-dac)

## Memory Map

| From | To | Description
|--|--|--|
| 0x0000 | 0x7fff | External memory
| 0x8000 | 0xffff | Internal Test ROM (256 bytes, mirrored)
| 0xff00 | 0xff00 | DAC_OUT (8-bit unsigned, write-only)

#### External memory map if using the recommended setup (see [pinout](#pinout))

| From | To | Description
|--|--|--|
| 0x2000 | 0x3fff | External RAM (32 bytes)
| 0x4000 | 0x5fff | External Flash ROM (16KB)

## Pinout
Due to TT07 IO constraints, pins are shared between *Address bus LSB* and *Data bus OUT*. It means that during memory write instructions, the address space is only 64 bytes.

| Pins | Standard mode | During memory write execute+writeback cycles
|--|--|--|
| ui[7..0] | Data bus IN | Data bus IN 
| uio[7..0] | Address bus LSB (7..0) | ***Data bus OUT***
| uo[6..0] | Address bus MSB (14..8) | Address bus MSB (14..8)
| uo[7] | Write Enable | Write Enable
| ua[0] | DAC output | DAC output

You can connect a 8KB parallel Flash ROM + 32b SRAM without 
external logic and use uo[6] for RAM OE# and uo[5] for Flash ROM OE#.

To get a bidirectional data bus (needed for SRAM), uio bus must be connected to ui bus with resistors. To be tested!

## Reset

At reset time, PC is set to 0x4000.

All other registers are set to 0x80.

# How to test

An internal test ROM with two demos is included for easy testing. Just select the corresponding DIP switches at reset time to start the demo (technically, a ***jmp GH*** instruction will be seen on the data bus thanks to the DIP switches values, with GH=0x8080 at reset).

## Demo 1: Rotating LED indicator
| SW1 | SW2 | SW3 | SW4 | SW5 | SW6 | SW7 | SW8 |
|--|--|--|--|--|--|--|--|
| 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 |

No external hardware needed. This demo shows a rotating indicator on the 7-segment display. Its speed can be changed with DIP switches, the internal delay loop is entirely deactivated when all switches are reset.

## Demo 2: Bytebeat Synthetizer

| SW1 | SW2 | SW3 | SW4 | SW5 | SW6 | SW7 | SW8 |
|--|--|--|--|--|--|--|--|
| 0 | 0 | 0 | 0 | 0 | 1 | 1 | 0 |

![](bytebeat.png)

Modem handshakes sound like music to your hears? It's your lucky day! Become a bit-crunching DJ thanks to 256 lo-fi glitchy settings.

Connect ua[0] -> amp(TBD?) -> speaker. Play with the DIP switches to change the loop settings. Suggested frequency/amp/passives TBD. 

# External hardware

* No external hardware for Demo 1
* Speaker (+ amp?) for Demo 2
* Otherwise: Parallel Flash ROM + optional SRAM

