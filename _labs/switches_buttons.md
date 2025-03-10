---
layout: lab
toc: true
title: "Lab 2: Writing Driver Software for the Slide Switches and Push Buttons"
short_title: Switches/Buttons Driver
number: 2
---

## Overview 
In this lab you will write and test driver software for the slide switches and push buttons that are on the 330 board. The schematic for these switches and push-buttons is shown on Page 22 of the [ZYBO reference manual]({% link media/lab1/zybo_rm_b_v6.pdf %}). The switches and push-buttons are items 9 and 13, respectively, shown in the photo of the Zybo board (Page 3 of the Zybo reference manual and duplicated below).

<img src="{% link media/lab2/zyboannotated.jpg %}" width="500" alt="Zybo schematic">

One you have completed the driver software, you will create test programs to verify that your driver code works correctly.

The switch test program will turn on LEDs corresponding to the switches that are in the UP position.  The test will end once all four switches are UP.
<iframe width="560" height="315" allow="fullscreen" src="https://www.youtube.com/embed/PaIXUnRUg-4"> </iframe>

The button test program will draw colored squares to the LCD dependent on which button is pressed, as shown in the following video. The test will end when all four buttons are pressed simultaneously.  On the emulator, you can press multiple buttons at once using the SHIFT key.
<iframe width="560" height="315" allow="fullscreen" src="https://www.youtube.com/embed/QYQlwwn_CJs"> </iframe>


## Objectives 
  - Gain experience with commercial documentation.
  - Understand how the GPIO IP block functions and write low-level code to communicate with it (instead of using the functions provided by *xgpio.h*).
  - Learn how to write low-level software to interface with buttons and switches.
  - Practice applying the class coding standard.
  - Write reusable code that you may use in later labs.
  - Write test code that demonstrates the correctness of your implementation and that demonstrates how to use the button/switch software driver that you wrote.
  - Gain additional practice with the graphic library and the LCD.

## Preliminary 

### Schematic 
The schematic for the switches is also shown below (Page 22 of Zybo reference manual).
<img src="{% link media/lab2/zyboswitchbuttonschematic.png %}" width="500" alt="Zybo buttons and switches schematic">

**Question:** What is the logic value of the pin attached to the push-button when the button is pushed? When it is released?


### New Terminology 
  * [memory map](https://en.wikipedia.org/wiki/Memory_map)
  * [address bus](https://en.wikipedia.org/wiki/Address_bus)
  * data bus
  * IP
  * Register

### Programming Stack  

We often refer to *stacks* when we talk about computing systems. A stack is a conceptual term that refers to multiple layers of software stacked one upon the other. See [Programming Stack]({% link _pages/programming_stack.md %}) for more information on how this terminology applies to this lab.

### GPIO and Register-Based Access 

When using programmable processors such as microprocessors, etc., we access low-level hardware via registers. See [the GPIO and Registers page]({% link _pages/gpio_registers.md %}) to understand how you will access the registers in the GPIO (General Purpose Input/Output) module.

### Source Code 

Review the [buttons.h](https://github.com/byu-cpe/ecen330_student/blob/master/drivers/buttons.h) and [switches.h](https://github.com/byu-cpe/ecen330_student/blob/master/drivers/switches.h) files which are provided in your repository.

### Compiling Lab2 

You are already provided a [CMakeLists.txt](https://github.com/byu-cpe/ecen330_student/blob/master/lab2/CMakeLists.txt) file for this lab that will compile the Lab 2 executable.  You will see that it is almost the same as Lab 1 file, except that line 2 also includes the *buttons_switches* library.

The software you write for this lab will be drivers for the buttons and switches.  This software will be reused in later labs, so it will be compiled into a library that can be used by multiple applications.  This library, called *buttons_switches*, will be located in the [drivers](https://github.com/byu-cpe/ecen330_student/tree/master/drivers) directory.  You are already given a [CMakeLists.txt](https://github.com/byu-cpe/ecen330_student/blob/master/drivers/CMakeLists.txt) file in this directory that will compile your *buttons.c* and *switches.c* code into the ''buttons_switches'' library.

The only changes you need to make for this lab is to instruct the top-level [CMakeLists.txt](https://github.com/byu-cpe/ecen330_student/blob/master/CMakeLists.txt) file to enter both of these directories and process those CMakeLists.txt files.  Add  `add_subdirectory(lab2)` and `add_subdirectory(drivers)` statements to the top-level CMakeLists.txt, after the `add_subdirectory(lab1)` statement.

You should read the page [About CMake files]({% link _documentation/cmake.md %}) to help understand the provided CMake files.


## Implementation 

The majority of your code for this lab will be placed in two files you create: *buttons.c*, *switches.c*. The "buttons" files will contain the code necessary to access the push-buttons while the "switches" will contain the code necessary to access the slide switches.  **These files must be placed in your ''drivers'' folder.**

## Requirements 
  - You must use the provided [.h files](https://github.com/byu-cpe/ecen330_student/tree/master/drivers) as is (these files are already in your repository). **No modifications are necessary or allowed.** Make sure you have reviewed the page on [including header files in your code]({% link _documentation/headers.md %}).
  - You are provided a simple [main.c](https://github.com/byu-cpe/ecen330_student/blob/master/lab2/main.c) file that will test your buttons and switches code. **No modifications are necessary or allowed.**
  - You must follow the [coding standard]({% link _other/coding_standard.md %}).
  - You must implement the required functions for the buttons and switches drivers (see below).
  - *switches_runTest()* will illuminate the LEDs on the ZYBO board above the slide switches.  Make sure to *#include "[leds.h](https://github.com/byu-cpe/ecen330_student/blob/master/include/leds.h)"* to access the necessary LED helper functions. Use these functions to control the LEDs. *switches_runTest()* will run until all of the slide switches are slid up at which point it will terminate.
  - *buttons_runTest()* will use the LCD display to demonstrate that the buttons are working (see the video referenced below). *buttons_runTest()* will run until all of the push-buttons are pressed simultaneously, similar to switches. When the 4 push-buttons are simultaneously pressed, this function returns.
  - In *buttons_test()* you must only draw the rectangle once for each button press and erase it once for each button release. It is OK if there is some button bouncing but continuously drawing or erasing a rectangle while in an infinite while-loop is not allowed. TAs will inspect your code to verify this. Also, continuously drawing rectangles at a high rate will often cause flashing on all but the fastest VMs.
  - Write helper functions for accessing the GPIO registers.  These functions should NOT be declared in your .h files, but rather at the top of the appropriate .c file:
    * `buttons_readGpioRegister(int32_t offset)`,
    * `buttons_writeGpioRegister(int32_t offset, int32_t value)`, 
    * `switches_readGpioRegister(int32_t offset)`, 
    * `switches_writeGpioRegister(int32_t offset, int32_t value)`.
  - Watch the provided videos to see what your test functions should look like once implemented (see above). For the button-test note that the rectangles that are drawn on the display must not flicker during the test.

##  Submission and Pass Off

For each lab, you will follow the [instructions on submitting source code]({% link _documentation/submission.md %}) to submit your code.

The TAs will compile your code and run the provided [main.c](https://github.com/byu-cpe/ecen330_student/blob/master/lab2/main.c) program, which will run both of your *runTest()* functions.  Do not modify main.c.



### Grade Breakdown 
  * 30% Code quality and adherence to the coding standard. You are allowed 10 freebies for this lab.
  * 70% Pass off. If your code works well and shows no bugs, you should get the full 70%.

## Resources

  * [GPIO documentation]({% link media/lab2/ds744_axi_gpio.pdf %})
  * [ZYBO Reference Manual]({% link media/lab1/zybo_rm_b_v6.pdf %})
  * [ZYBO Board Schematics]({% link media/lab2/zybo_sch.pdf %})

