# fujinon-tv-lens-control

## Purpose

This is a collection of knowledge about the control interface of B4 tv lenses.  

This interface could be found on lenses from Sony and Fujinion. Also Canon produces B4 lenses, but i could not find out if there is such a interface - seems it's not.  

If you searching around you find only some few contributions in a bunch of different platforms. This is the result of my own research about this topic.

I'm focusing on Fujinion lenses in the further description.

## Foreword

During my research i've found that there are two different types of serial lens-control are on the market.  

One group (A) of fujinion lenses are using a "real" rs232-like serial control interface. This group can be identified if there are some signal pins beside the TX and RX pins (e.g. CTS, DTE ...).  

On the other hand there is a group (B) of lenses which are using B4 digital serial control within the widely used 12-pin B4 lens connector. TX and RX are on pins 11 and 12, beside old style analog signals.  

Also some older models of B4 lenses can be found (C), which are only supporting analog control over the 12-pin B4 lens connector.

### Fujinon lens model naming

The last part is the name of the drive unit.


### Group A

Demo Software to control this kind of lens. I've monitored the sent commands from that software.
It fits to the specification of DIGI POWER L10 Protocol.  
<https://imagelabs.com/imaging-system-components/security/fujinon-lens-controller-software/>

### Group B

### Group C

## Pinout 12-pin B4 lens connector

This connector is a Hirose ***TODO***

|         | old style analog only (C)                                    | with serial digital control (B)                                                     |             |
| :----:  | ---------------                                           | -----------------------------------                                             | ----------- |
| **Pin**     | **Function**                                          | **Function**                                                                    | **Direction**   |
| 1       | RET SW (Return Video Select) (OFF: open ON: 0V)           | RET SW (Return Video Select) (OFF: open ON: 0V)                                 | Lens -> Cam |
| 2       | VTR SW (VTR Start/Stop) (NO ACTION: open START/STOP: 0V)  | VTR SW (VTR Start/Stop) (NO ACTION: open START/STOP: 0V)                        | Lens -> Cam |
| 3       | GND (0V)                                                  | GND (0V)                                                                        | Cam -> Lens |
| 4       | Iris Servo (OFF: 0V ON: 5V)                               | IRIS ENF AUTO (OFF: 0V ON: 5V)                                                  | Cam -> Lens |
| 5       | IRIS CONT SIG (Iris Control Signal)                       | IRIS CONT SIG (Iris Control Signal) (F2.8: 6,2V F16: 3,4V CLOSE: 2,5V)          | Cam -> Lens |
| 6       | POWER (+12V)                                              | POWER (+12V)                                                                    | Cam -> Lens |
| 7       | IRIS POSITION (F2.8: 6,2V F16: 3,4V CLOSE: 2,5V)          | IRIS POSITION (F2.8: 6,2V F16: 3,4V CLOSE: 2,5V)                                | Lens -> Cam |
| 8       | IRIS MODE CONTROL (AUTO: 0V REMOTE: 5V)                   | IRIS MODE CONTROL (AUTO: 0V REMOTE: 5V) AUTO=lens itself REMOTE=from camera     | Cam -> Lens |
| 9       | EXT SIG (Extender Signal) (OUT: open IN: 0V)              | EXT SIG (Extender Signal) (OUT: open IN: 0V)                                    | Lens -> Cam |
| 10      | ZOOM POSITION (WIDE: 2V TELE: 7V)                         | ZOOM POSITION (WIDE: 2V TELE: 7V)                                               | Lens -> Cam |
| 11      | FOCUS POSITION (NEAR: 2V INFINITY: 7V)                    | TXD (Transmit) (FALSE: 0V TRUE: 5V)                                             | Lens -> Cam |
| 12      | --                                                        | RXD (Receive)  (FALSE: 0V TRUE: 5V)                                             | Cam -> Lens |

## Serial Communication Parameters

|Parameter    | Value|
|---------    | -----|
|Speed [baud] | 78400 |
|Databits     | 8|
|Paritybit    | none |
|Stopbits     | 1|

normale Pegelwandlung
TTL = 0 = 0V, RS232 = 0 = +12V
TTL = 1 = 5V, RS232 = 1 = -12V


hier ist das schon TTL seitig invertiert
invertiert! +5V -> logic 0
TTL = 1 = 5V

ich kann als Workaround mal probieren ein RS232 Interface anzuschließe ob der das 5V Signal akzeptiert und invertiert

short: `78400 8N1`

### CRC

alle bytes inklusive länge addieren
100h - sum = crc -> letztes byte

## CAM TO LENS

### Telegram structure

`<cmd1> <cmd2> <special> <value?> <value?> <crc?>`

### Iris open / close

`FE C2`  
gesichert

### Focus far / near

`FE CE`
gesichert  
`FE EE`  
??

wenn man auf push autofocus in der app drückt
`FE 9E`  
??
`FE 82`  
??
`FE CE`

`FE C1`
??  

Richtung unendlich
wird das 4. Byte groß

Richtung nah
wird eher das 5. Byte groß

Linse steht auf min
Kommandao auf min
Byte 4 groß, Byte 5 klein

linse steht auf max
Kommando auf min
Byte 4 klein, Byte 5 groß

### Zoom wide / tele

`FE F2`  
gesichert

bereits begonnen Telegramme der Kamera werden mit einem neuen Command unterbrochen. Der Rest vom alten Kommando hinten dran gehängt?!
0x00 kann man wohl ignorieren/unterdrücken

## LENS TO CAM

solange keine Kommunikation läuft  
`FB 03`  

`FF E3  93 00 63 00`  
zyklisch

`F3 F3  93 00 63 00`  
zyklisch

an Stelle 7 + 8 vermutlich die blende
geschlossen (großer wert) 6C 8C
offen (kleiner wert) 1B 6C

an Stelle 9 + 10 vermutlich der Zoom
tele max. F0 6F
weit min. 1C 6C ??

im zyklischen F3F3 kommt an Stelle 11 + 12 vermutlich der aktuelle Fokuswert min FF E3 = nah = 0,90m
aktuelle Fokuswert max 00 7C = unendlich

vermutlich Bytes getauscht?
Focus kleinster Wert 3EFF = 16.127

## Credits

This research is done with a couple of very helpful tools:  

+ PicoScope 2204A (a USB oscilloscope) with corresponding software: <https://www.picotech.com/downloads>
+ PicoScope serial decoding: <https://www.picotech.com/library/oscilloscopes/rs-232-serial-protocol-decoding>
+ Pulseview, the signal and logic analyzer software of sigrok project: <https://sigrok.org/wiki/Downloads>
+ csv2vcd, a tool to convert PicoScope output to Pulseview: <https://github.com/feecat/csv2vcd>
+ USBProg v4.0, USB programmer and serial device (no more available), working with 3.3V and 5.0V UART signal levels
+ YAT (yet another terminal), a very good terminal application, especially for binary protocols: <https://sourceforge.net/projects/y-a-terminal/>

## Links

### Know-How

#### RS232

+ <https://www.best-microcontroller-projects.com/how-rs232-works.html>
+ <https://www.youtube.com/watch?v=sTHckUyxwp8>  
+ <https://www.youtube.com/watch?v=JuvWbRhhpdI>  

### Collection of links to similar contributions:  

#### BMD Forum

+ B4 lens communication protocol: <https://forum.blackmagicdesign.com/viewtopic.php?p=343280>
+ https://forum.blackmagicdesign.com/viewtopic.php?t=165607

#### Reddit

+ B4 tv lens digital control protocol: <https://www.reddit.com/r/videography/comments/1av053z/b4_tv_lens_digital_control_protocol/>

#### Arduino Forum


https://www.reddit.com/r/VIDEOENGINEERING/comments/14dpj38/b4_lens_database/

https://en.wikipedia.org/wiki/B4-mount

https://www.arib.or.jp/english/std_tr/broadcasting/desc/bta-s-1005.html
https://www.arib.or.jp/english/std_tr/broadcasting/bta-s-1005.html


https://forum.arduino.cc/t/inverted-serial-signals/12020/8

https://en.wikipedia.org/wiki/9-Pin_Protocol
https://ftp.zx.net.nz/pub/archive/ftp.sgi.com/sgi/video/rld/vidpage/s9pin.html

https://en.wikipedia.org/wiki/B4-mount


https://www.esbroadcast.com/product/used-fujinon-ha18x7-6berd-s48-hd-eng-lens/
https://forum.blackmagicdesign.com/viewtopic.php?f=2&t=164165&p=866679&hilit=serial+lens+control#p866679
https://forum.blackmagicdesign.com/viewtopic.php?f=4&t=134929&p=730986&hilit=serial+lens+control#p730986
https://forum.blackmagicdesign.com/viewtopic.php?f=4&t=84205
https://forum.blackmagicdesign.com/viewtopic.php?t=165607
https://forum.blackmagicdesign.com/viewtopic.php?f=2&t=171757&p=904192&hilit=fujinon+digital+analog#p904192
https://forum.blackmagicdesign.com/viewtopic.php?f=2&t=155504&p=952285&hilit=serial+lens+control#p952285
https://forum.blackmagicdesign.com/viewtopic.php?f=4&t=21610

https://support.cyanview.com/docs/Integrations/Lens/B4Lens

https://www.canon-europe.com/broadcast/tv_lenses/remote_control_lenses/

https://www.canon-europe.com/pro/broadcast/eng-efp-pro/

https://forum.blackmagicdesign.com/viewtopic.php?f=4&t=75905

https://www.dyxum.com/dforum/emount-electronic-protocol-reverse-engineering_topic119522.html
https://www.dpreview.com/forums/thread/3872069

## other Solutions
https://vimeo.com/738206774
https://www.linkedin.com/posts/levitezer_levitezer-b4-control-box-demonstration-activity-6963089260321099776-IrMd/
https://wiki.skaarhoj.com/books/device-core-articles/page/eth-b4-link-lens-control