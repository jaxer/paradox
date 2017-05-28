# Paradox/magellan protocol reversal, first steps.

## Why?

I have old alarm system which is not used but installed. So i decided to connect it to home automation.
There are few documents online describing how to set it up but nothing about wireless protocol. Also there were few projects reversing serial protocol from base unit which would not work for me as i dont have those units operational. 
So i decided to reverse wireless protocol and use sensors directly.

## MG-DCT1
This is a simple door open/close sensor. In addition to raid switch it has a separate wired input.

![img_1593](https://cloud.githubusercontent.com/assets/202096/26530298/42be1f6e-43db-11e7-89d0-552427b719a7.jpg)
![img_1594](https://cloud.githubusercontent.com/assets/202096/26530300/44dd6f52-43db-11e7-8871-5177bb701b77.jpg)

Main processor: PIC16LF648A
ASK Transmitter: TH 72012 (433.92 mhz)

At first i tried to use rtl_433. It got signal, but have not had a filter for it. Using analyze was not hugely helpful, except that it shown that packets are not encrypted anyhow and are repeatable.

Then i took a cheap 433mhz receiver and hooked it up to osciloscope. That resulted in lots of noise making it too hard to deduct whats going on.

At last i opned the unit and connected osciloscope to pin1 of TH 72012 (ASKDATA).

## Protocol

![img_1597](https://cloud.githubusercontent.com/assets/202096/26530316/810cec82-43db-11e7-8d7e-ea9f72d7f89a.jpg)

Each packet consist of synchronization header followed by 4 bytes of data and finished with small footer.

* Sync header: `800us high` -> (`200us low` -> `200us high`) x 13 times -> `400ms low` -> `1200ms high` -> `400ms low`
* Data part: 32 bits where each bit is one of:
  - `400us high` -> `200us low`
  - `200us high` -> `400us low`
* Footer: `200us high`

Each packet is repeated several (8?) times.

## Packets

There seem to be a separate packets on the follwoing events:
- Unit powered on
- Anti tamper switch up
- Reed closed
- Reed opened
