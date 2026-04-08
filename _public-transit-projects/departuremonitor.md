---
layout: project

author: Jana
title: Departure Monitor for Vienna's metro
last_updated: 2022-04-14 
template-image: /assets/images/DepartureMonitor/view-finished.jpg
---

While working on a monitor displaying the Vienna metro system in real time I was asked by a friend of mine, if it would be possible to just track one part of a single line. 
Some time has passed and I finally got the free time to work on that.
The preliminary version is viewable on <a href="https://github.com/jananica/DepartureMonitorViennaPublicTransit"> GitHub</a>.
### Goal
Replicate the look of the monitors in Vienna's metro and show live departure times. 

### Project structure

```batch
├── micropython_ssd1322
│   ├── ssd1322.py
│   ├── xgcl_font.py
│   └── mono_palette.py
├── lib
│   └── requests
│   │   ├── __init__.mpy
│   ├── datetime.mpy
│   └── urequests.mpy
├── fonts
│   └── default_font.c
├── img
│   ├── Gleis1.mono
│   ├── Gleis2.mono
│   └── Gleis3.mono
├── boot.py
├── Program.py
├── DataConversion.py
└── Monitors.py
```

``boot.py`` starts the program by initializing the class ``WienerLinienMonitor`` in ``Program.py``.


### Tools used
#### Board
I wanted to use an Arduino board again. Hence I chose the _Arduino Nano ESP32_.
It has builtin WI-FI capabilities and enough RAM to process the .json replies.
Coding in MicroPython seemed like a natural choice and I resorted to the following tools from the Arduino community:

- MicroPython Installer: to install MicroPython firmware on board
- MicroPython-Package-Installer: to install required packages
- Arduino Lab MicroPython: to connect to the board and test the program

#### Display
The display I settled for was a 256x64px yellow OLED 
controlled by an SSD1322 driver.
There was some sample code on the manufacturer's site detailing the communication protocol with the displays.
However I found the following [micropython library](https://github.com/rdagger/micropython-ssd1322/), which made the job much more easy.

The displaying font (10x16 px) was created rather vibe-based to mimic in some sense the original font.
It was created with the help of [**MikroElektronika GLCD Font Creator 1.2.0.0**](http://www.mikroe.com/)
Note: I had to run the program in _Windows XP compatibility mode_, otherwise it wouldn't let me save the progress.
Exporting was fairly straightforward: 'Export for GLCD' -> select 'mikroC' and save.

The platform numbers on the side of the monitors are displayed as a monochrome image (size 35x64px).
To convert the ``.png`` files into ``.mono`` with the right encoding, I used [__ImageMagick__](https://imagemagick.org/) with the following command:
`batch
magick Gleis1.png -monochrome Gleis1.mono
`

### Versatility
While working on this project, I took into account numerous possibilities for adapting the design and functionality of the program. 
#### in `Program.py`:
Displaying modes may be the most prominent of them. There exist two variables.
- `flag_show_platform_nr`: if true, the Platform number will be shown on one side of the display. Note: it also prevents the displaying algorithm from merging departures which are in the same direction, but from different platforms.
- `flag_show_line`: if true, the corresponding line will be printed before the destination of the departures. 

Note to this: not every monitor on the Metro does show either one.

Any two combinations of the above can be preset and conviniently switched via the 10bit parallel input.
`Platform_Sides` enhances the feature by the possibility to set the side the platform number should be displayed.

Any (preferably 10x16px) X-GLCD font can be used instead of the default font.
If needed, `display_text_spacing` can be increased. 

The constants `TIME_BETWEEN_API_REQUESTS` and `MIN_TIME_BETWEEN_API_REQUESTS` configure the waiting period between API requests. While lowering time between requests help with more up-to-date data, it also takes some time to fetch and convert.

There is another feature, which switches from displaying the second and third departure, called _advanced preview_.
`ADVANCED_PREVIEW_ANIMATION_PERIOD` controls the period (in seconds) between switching.

`UPDATE_PERIOD` then sets the time interval in which every monitor should be updated once. 
Note: It should be at least 0.5*#(monitors), my meassured time for update of one monitor is ~300ms

All monitors? Yes the number of monitors is not fixed, and can be any number >= 1.

And even the station the display shows can be set to be any station (doesn't have to be metro) in Vienna. But we will get to that.
Preprogrammed are all (currently 110) stations for the metro.
The stations are first grouped into the line they serve. 
```
LINES = ['U1','U2','U3','U4','U5','U6']
Pin_in_selectLine = [5,6,7] #select line
Pin_in_selectStation = [8,9,10,17,18] #select station
Pin_in_selectAdvancedPreview = 21
Pin_in_select_displaymode = 44
```

The connection to the displays is specified by the `SCK`-pin, one pin for `COPI` (Contoller Out, Peripheral In) and `CS`-, `DC`-, `RST`-pin for each display.
```
Pin_SCK = 48
Pin_COPI = 38
Pins_CS = (1,2) #the i-th component refers to the i-th monitor
Pins_DC = (4,12)
Pins_RST = (3,11)
```

The `stop-id` (see [API documentation](https://www.wienerlinien.at/ogd_realtime/doku/)) is one way to request data for one station with specified line and departure direction.
The `stop-id`-values of each station is stored in `DataConversion.py`. This code can be replaced by any list of `stop-id`'s and thus any station can be displayed. As an example, the line U1 with it's `stop-id`'s.
I obtained the data from the [API documentation website](https://www.wienerlinien.at/ogd_realtime/doku/).

```
#index 0,0 is Oberlaa ->Leopoldau
stop_IDs_U1 = [[4134,4133], [4135,4132], [4136,4131], [4137,4130], [4138,4129], [4101,4128], [4103,4126], 
               [4105,4124], [4107,4122], [4109,4120], [4111,4118], [4113,4116], [4115,4114], [4117,4112], 
               [4119,4110], [4121,4108], [4123,4106], [4125,4104], [4127,4102], [4181,4186], [4182,4187], 
               [4183,4188], [4184,4189], [4185,4190]]
```









