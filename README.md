# esp8266-scheduler

## Notes for patching ESP8266 core

The current scheduler library supports esp8266 Arduino core '2.6.3'.

Apply the path `core_esp8266_2.6.3.patch` for esp8266 Arduino core '2.6.3'. First, unpack git repository to the home directory and then apply the patch into the current availabe esp8266 core. Currently, the esp8266 core is available at `~/.arduino15`. Use the following commands to apply the path and adapt them accordingly if needed.
```
cd ~
git clone https://github.com/anmaped/esp8266-scheduler.git
cd ~/.arduino15/packages/esp8266/hardware/esp8266/
patch -s -p0 < ~/esp8266-scheduler/libraries/scheduler/core_esp8266_2.6.3.patch
```

### Old instructions for esp8266 core version 2.3.0:
 
We have to chose the Arduino 1.6.8 with esp8266 core version 2.3.0. This patched runtime is available in `https://github.com/srmq/Arduino/tree/scheduler_branch`. The Arduino 1.6.8 for windows is availalbe in `https://www.arduino.cc/download_handler.php?f=/arduino-1.6.8-windows.zip` and for Linux 32 in `https://www.arduino.cc/download_handler.php?f=/arduino-1.6.8-linux32.tar.xz`.

Instructions: Replace the esp8266 core inside the Arduino folder `~/.arduino15/packages/esp8266/hardware` with the patched version provided in the branch above.

- Install esp8266 2.3.0 using the usual arduino esp8266 instructions (add http://arduino.esp8266.com/stable/package_esp8266com_index.json to preferences, and install it using the board manager).
- Create the folder 'hardware/esp8266com/esp8266' and extract inside it the content available in the branch `https://github.com/srmq/Arduino/tree/scheduler_branch`. Simply download the zip file from GitHub and rename it locally.
- Add the Scheduler library to Arduino.

Note that the esp8266 core will replace the installed version 2.3.0.


## Introduction

This library implements an extended sub-set of the Arduino Scheduler
class. Multiple loop() functions, tasks, may be started and run in a
collaborative multi-tasking style. The tasks are run until they call
yield() or delay(). The Arduino yield() function is replaced by an
implementation in the library that allows context switching.

Tasks should be viewed as static and continuous. This implementation
does not allocate tasks on the heap. They are allocated on the normal
stack and linked into a cyclic run queue. One-shot tasks are not
supported. Instead the task start function is extended with a setup
function reference. Tasks are started with:

````
Scheduler.start(taskSetup, taskLoop [,taskStackSize]).
````
The tasks will start execution when the main task yields. The
_taskSetup_ is called first and once by the task followed by repeated
calls to _taskLoop_. This works just as the Arduino setup() and loop()
functions. There is also an optional parameter, _taskStackSize_. The
default value depends on the architecture (128 bytes for AVR and 512
bytes for SAM/SAMD/Teensy 3.1).

The Scheduler is a single-ton and the library creates the single
instance.

This library also includes support for task synchronization and
communication; Semaphores, Queues and Channels.

## Install

Download and unzip the esp8266-scheduler library into your sketchbook
libraries directory. Rename from esp8266-scheduler-master to esp8266-scheduler.

The Scheduler library and examples should be found in the Arduino IDE
File>Examples menu.

## Performance

There are several benchmark sketches in the examples directory. Below
are some of the results.

### Context Switch

Board | us | cycles
------|----|-------
Arduino Mega 2560 (16 MHz) | 12.64 | 203
Arduino Uno, Nano, Pro-Mini, etc (16 MHz) | 11.00 | 176
Sparkfun SAMD21 (48 MHz) | 2.60 | 125
Arduino Due (84 MHz) | 1.36 | 115
Teensy 3.1 (72 MHz) | 1.10 | 80

### Max Tasks (default stack size)

Board | Tasks | Stack (bytes)
------|-------|--------------
Arduino Uno, Nano, Pro-Mini, etc (16 MHz) | 9 | 128
Sparkfun SAMD21 (48 MHz) | 26 | 512
Teensy 3.1 (72 MHz) | 26 | 512
Arduino Mega 2560 (16 MHz) | 48 | 128
Arduino Due (84 MHz) | 52 | 512


### Memory Footprint

Board | PROGMEM | SRAM (bytes)
------|---------|-------------
Arduino Due (84 MHz) | 224 | NA
Arduino Uno, Nano, Pro-Mini, etc (16 MHz) | 546 | 42
Arduino Mega 2560 (16 MHz) | 548 | 44
Sparkfun SAMD21 (48 MHz) | NA | NA
Teensy 3.1 (72 MHz) | NA | NA



