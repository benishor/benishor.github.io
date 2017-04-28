---
layout: post
title: How to mill a SMD PCB with a CNC
tags:
    - electronics
    - pcb
    - cnc
excerpt: "After quite a lot of fiddling around with settings like spindle speed, feedrate, bits and what not, I have found the golden settings which allow me to mill PCBs suitable for smd work."
---
A while ago I acquired a small CNC for PCB prototyping and been playing with it on and off due to time constraints. One thing that annoyed me was that my machining skills were close to none and the results I got etching PCBs were volatile and non-repeatable. I set up on a quest to find a repeatable way for etching PCBs suitable for SMD work.

I started by creating a KiCad project containing a test pattern that would allow me to make comparative measurements of different etching settings. [Here](https://github.com/benishor/cnc-test-pattern) is the test patern that was used for milling. It contains SOT-23, SOIC and TQFP footprints and tracks with widths between 0.125mm and 1.000mm.

After a lot of fiddling around with settings like spindle speed, feedrate, bits and what not, I believe I have found the golden settings which allow me to mill PCBs suitable for SMD work.

## Tests

Here are my two most successful attempts' milling parameters along with the results and some general tips.

### 30&deg; V-Bit with 0.1mm tip

#### Settings

|   Parameter   |     Value     |
| ------------- | ------------- |
| tool          | vbit 30&deg;, 0.1mm tip  |
| milling depth | 0.1mm          |
| effective tool width | 0.15359mm |
| feedrate      | 200            |
| spindle speed | 20000          |
| passes        | 2              |
| pass overlap  | 13% (0.13 in flatcam) |

#### Result

Here is a picture with the milling result. You can click on the image in order to obtain a higher resolution version. The mill result for the current settings can be seen in the right side of the image.
[![](/assets/vbit_30deg_0.1mm-thumb.jpg)](/assets/vbit_30deg_0.1mm.jpg)

### 10&deg; V-Bit with 0.1mm tip

|   Parameter   |     Value     |
| ------------- | ------------- |
| tool          | vbit 10&deg;, 0.1mm tip  |
| milling depth | 0.1mm          |
| effective tool width | 0.1175mm |
| feedrate      | 200            |
| spindle speed | 20000          |
| passes        | 2              |
| pass overlap  | 10% (0.10 in flatcam) |

#### Result

Here is a picture with the milling result. You can click on the image in order to obtain a higher resolution version of this attempt. You can note the differences around the TQFP footprint compared to the previous attempt. Overlap was possibly too thin and this can be seen in certain places around the footprint.
[![](/assets/vbit_10deg_0.1mm-thumb.jpg)](/assets/vbit_10deg_0.1mm.jpg)

## Conclusion

After countles attempts, success at last! I think I am going to go with the 30&deg; Vbit tool, 0.1mm etching depth and tracks with widths equal or greater than 0.250mm (or ~10mils for you imperials). 

Here are some hints to help out with CNC etching of PCBs:

- the thinner the tool tip, the slower the feedrate should be, because the thinner the bits, the more fragile they become when feedrate remains constant
- it's normal to have burs after etching; those can be removed using a fine sand paper (1000 granularity)
- 0.1mm etching depth is ok (I'm sure you could go with less but I'm being conservative here). It is very important to use auto-leveling and make sure the board is tightly glued to the bed. I used double sided tape and performed auto-level probing on a 5x5mm grid.
