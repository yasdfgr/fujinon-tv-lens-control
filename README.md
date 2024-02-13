# fujinon-tv-lens-control

## CAM TO LENSE

### Telegram structure

`<cmd1> <cmd2> <special> <value?> <value?> <crc?>`

### Iris open / close

FE E2

### Focus far / near

FE E6
FE EE

### Zoom wide / tele

FE F2

bereits begonnen Telegramme der Kamera werden mit einem neuen Command unterbrochen. Der Rest vom alten Kommando hinten dran gehängt?!
0x00 kann man wohl ignorieren/unterdrücken

## LENS TO CAM

solange keine Kommunikation läuft
FB 03

Focus kleinster Wert 3EFF = 16.127

## Credits

This research is done with a couple of very helpful tools:  

+ PicoScope 2204A (a USB oscilloscope) with corresponding software: <https://www.picotech.com/downloads>
+ Pulseview, the signal and logic analyzer software of sigrok project: <https://sigrok.org/wiki/Downloads>
+ csv2vcd, a tool to convert PicoScope output to Pulseview: <https://github.com/feecat/csv2vcd>
+ USBProg v4.0, USB programmer and serial device (no more available), working with 3.3V and 5.0V UART signal levels
+ YAT (yet another terminal), a very good terminal application, especially for binary protocols: <https://sourceforge.net/projects/y-a-terminal/>
