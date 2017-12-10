# Serial Communication API for LakeWest DevDAC

get command returns push. set command returns push to confirm new value. push command to DevDAC is not a valid cmd, its only pushed back by DevDAC to return/confirm set value.

Terms definition:

Streamer Software - any software running on lakewest DevDAC hardware using this open protocol

DevDAC - LakeWest hardware - visible as USB composite device to the Streamer Software as:

            *USB Audio Class 2 device (suport up to 768kHz 32 bits, DSD512 native, DoP256)
            *CDC USB device COM port - 115200, 8, N, 1 - active with this protocol
            *HID USB device for playback control
            *DFU USB device to update DevDAC onboard firmware 
            

#### Playback Controls 

Uses standart HID controls for next, previous track, pause/play, stop

#### VOLUME

```shell
[getVolume]{}
```

```shell
[pushVolume]{volumeA:255,volumeB:255,mute:0}
```

```shell
[setVolume]{volumeA:255,volumeB:255,mute:0}
```

volumeA: 
		
	left channel
	0 - 255; 0 = 0dB; 0.5dB step
	
volumeB: 
	
	right channel
	0 - 255; 0 = 0dB; 0.5dB step
	
mute:
	
	0 - not muted
	1 - digital mute

#### INPUT

```shell
[getInput]{}
```

```shell
[pushInput]{activeInput:2}
```

```shell
[setInput]{activeInput:2}
```

DevDAC specific
activeInput:

	0   "Streamer",	
	1   "USB",	
	2   "COAX 1",	
	3   "OPT 1",	
	4 - 255 reserved for future use.

#### PLAYBACK INFO

```shell
[getAudioFormat]{}
```

```shell
[pushAudioformat]{formatType:1,sampleRate:9}
```

fileType list:

	0   "PCM",	
	1   "DSD",	
	2   "MQA-G",	
	3   "MQA-B,
	4   "MQB"
	5   "HDCD"
	
sampleRate:

	0    "44100",	
	1    "88200",	
	2    "176400",	
	3    "352800,
	4    "705600",
	5    "1441200",
	6    "2822400",
	7    "5644800",
	
	8    "48000",
	9   "96000",
	10   "192000",
	11   "384000",
	12   "768000",
	13   "1536000",
	14   "3072000",
	15   "6144000",
	
	16   "64000",
	17   "128000",
	18   "256000",
	19   "512000",
	20   "1024000",
	21   "2048000",
	22   "4096000",
	23   "8192000",
	
	0   "DSD64",
	1   "DSD128",
	2   "DSD256",
	3   "DSD512",
	4   "DSD1024"
	
	
#### FILE FORMAT

```shell
[getFileFormat]{}
```

```shell
[pushFileFormat]{fileFormat:1}	
```

```shell
[setFileFormat]{fileFormat:1}	
```

fileFromat is used for hardware screens. Reserved for future use.

fileFormat:

	1   "mp3",	
	2   "acc",	
	3   "ogg",	
	4   "flac",
	5   "alac",
	6   "wav",
	7   "wma"
	
	--please add more supported formats


#### DIGITAL FILTER

```shell
[getFilter]{}
```

```shell
[pushFilter]{filter:0}
```

```shell
[setFilter]{filter:0}
```

filters:

	0   "Optimal Transient",	
	1   "Linear Phase Fast roll-off",	
	2   "Linear Phase Slow roll-off",	
	3   "Minimum Phase Fast roll-off",	
	4   "Minimum Phase Slow roll-off",
	5   "Apodizing Fast roll-off",
	6   "Corrected Minimum Phase Fast roll-off",
	7   "Brick Wall"
	
#### THD COMPENSATION

```shell
[getTHDcomp]{state:0}
```

```shell
[pushTHDcomp]{state:0}
```

```shell
[setTHDcomp]{state:0}
```

state:

	0   "THD compenstation disabled - listening mode",		
	1   "THD compenstation enabled  - measurement mode	


#### DPLL BANDWIDTH
only aplicable when SPDIF input is selected. Lower DPLL BW means lower jitter, but its harder to lock to sources. Default is 5 for PCM and 10 for DSD (DoP over SPDIF).

```shell
[getDPLL]{}
```

```shell
[pushDPLL]{levelPCM:0, levelDSD:0}
```

```shell
[setDPLL]{levelPCM:0, levelDSD:0}
```

levelPCM:

	0 - 16; 0 - DPLL OFF, 16 - highest BW	
	
levelDSD:

	0 - 16; 0 - DPLL OFF, 16 - highest BW	
	
#### DEVDAC BOOT CONTROL

```shell
[pushBoot]{boot:0}
```

```shell
[setBoot]{boot:0}
```

boot:

	0   note defined,	
	1   puts CM3 into USB bootloader mode where new Streamer Software image can uploaded in over USB	
	
TODO:
define exit from bootloader mode thru I2C-1 command from Streamer Software to DevDAC as USB CDC channel has been disconected in bootloader mode. 

	
#### POWER OFF CONTROL

Is pushed from the DevDAC to Streamer Software to signal user has switched off the unit with hardware front panel button. Streamer Software then needs to start shutting down. After its mostly ready to cut the power, it sets turn off with cmd set to 1. DevDAC confirms with push cmd:1 and then cuts the CM3 power in 10 seconds.

```shell
[getTurnOff]{}
```

```shell
[setTurnOff]{cmd:0}
```


```shell
[pushTurnOff]{cmd:0}
```
cmd:

0 - start shutting down - either user pressed a hardware power button or is shutting it down thru the Streamer Software
1 - unit is ready to cut the power in 10 seconds

#### DEVDAC VERSION
(from Streamer Software to DevDAC)

```shell
[getBoardVersion]{}
```

```shell
[pushBoardVersion]{boardRev:12,firmwareXMOS0:11,firmwareXMOS1:11}
```

#### STREAMER VERSION
(from DEVDAC to Streamer Software)

```shell
[getStreamerVersion]{}
```

```shell
[pushStreamerVersion]{version:1223}
```
