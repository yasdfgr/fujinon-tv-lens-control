# fujinon-tv-lens-control

# CAM TO LENSE

`<cmd1> <cmd2> <special> <value?> <value?> <crc?>`

## Iris open / close

FE E2

## Focus far / near

FE E6
FE EE

## Zoom wide / tele

FE F2

bereits begonnen Telegramme der Kamera werden mit einem neuen Command unterbrochen. Der Rest vom alten Kommando hinten dran gehängt?!
0x00 kann man wohl ignorieren/unterdrücken

# LENS TO CAM

solange keine Kommunikation läuft
FB 03

Focus kleinster Wert 3EFF = 16.127

# Credits

This research is done with a couple of very helpful tools:  
+ 