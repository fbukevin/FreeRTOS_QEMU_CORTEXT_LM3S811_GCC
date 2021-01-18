
## Name of projecct

Demo project CORTEX_LM3S811_GCC is ARM CORTEXT-M3 for evaluate board LM3S811 and building by GCC.

## Driver Libraries

Driver is platform dependent. The evaluate board factory develop and provide the driver source to a demo project. In this demo project, the driver libraries are located at **hw_include**.

## Building and executing the demo application – GCC
These instructions assume the Sourcery G++ tools are already installed, configured and included within the PATH environment variable.
1. Open a command prompt and navigate to the FreeRTOS/Demo/CORTEX_LM3S811_GCC directory.
2. Type “Make”, the project should build with no errors or warnings with the resultant binary placed in a directory called gcc
Example output:

```
C:/E/Dev/FreeRTOS/Demo/CORTEX_LM3S811_GCC>make
  CC    init/startup.c
  CC    main.c
  CC    ../../Source/list.c
  CC    ../../Source/queue.c
  CC    ../../Source/tasks.c
  CC    ../../Source/portable/GCC/ARM_CM3/port.c
  CC    ../../Source/portable/MemMang/heap_1.c
  CC    ../Common/Minimal/BlockQ.c
  CC    ../Common/Minimal/PollQ.c
  CC    ../Common/Minimal/integer.c
  CC    ../Common/Minimal/semtest.c
  CC    hw_include/osram96x16.c
  LD    gcc/RTOSDemo.axf

C:/E/Dev/FreeRTOS/Demo/CORTEX_LM3S811_GCC>
``` 

3. Enter the GCC directory.
4. Type “arm-stellaris-eabi-gdb RTOSDemo.axf” to start the debugger.
5. Type “target extended-remote | arm-stellaris-eabi-sprite -r -s 2 -f lmi stdio” at the GDP prompt to connect to the board.
6. Type “load” to program the microcontroller flash.
7. Type “continue” to start the program executing.
```
Example output:

C:/E/Dev/FreeRTOS/Demo/CORTEX_LM3S811_GCC>cd gcc

C:/E/Dev/FreeRTOS/Demo/CORTEX_LM3S811_GCC/gcc>arm-stellaris-eabi-gdb RTOSDemo.axf
GNU gdb (Sourcery G++ 4.1-23) 6.5.50.20060822-cvs
Copyright (C) 2006 Free Software Foundation, Inc.
GDB is free software, covered by the GNU General Public License, and you are
welcome to change it and/or distribute copies of it under certain conditions.
Type "show copying" to see the conditions.
There is absolutely no warranty for GDB.  Type "show warranty" for details.
This GDB was configured as "--host=i686-mingw32 --target=arm-stellaris-eabi".
For bug reporting instructions, please see:
.
..
(no debugging symbols found)
(gdb) target extended-remote | arm-stellaris-eabi-sprite -r -s 2 -f lmi stdio
Remote debugging using | arm-stellaris-eabi-sprite -r -s 2 -f lmi stdio
0x000000c4 in ResetISR ()
(gdb) load
Loading section .text, size 0x241c lma 0x0
Loading section .data, size 0x4 lma 0x241c
Start address 0xc5, load size 9248
Transfer rate: 73984 bits/sec, 840 bytes/write.
(gdb) continue
Continuing.
```

### RTOS port specific configuration
Configuration items specific to this port are contained in FreeRTOS/Demo/CORTEX_LM3S811_[compiler]/FreeRTOSConfig.h, where [compiler] is Keil, IAR or GCC. The constants defined in this file can be edited to suit your application. In particular –

- **configTICK_RATE_HZ** 
This sets the frequency of the RTOS tick. The supplied value of 1000Hz is useful for testing the RTOS kernel functionality but is faster than most applications require. Lowering this value will improve efficiency.

- **configKERNEL_INTERRUPT_PRIORITY and configMAX_SYSCALL_INTERRUPT_PRIORITY**
See the RTOS kernel configuration documentation for full information on these configuration constants.

Attention please!: Remember that ARM Cortex-M3 cores use numerically low priority numbers to represent HIGH priority interrupts, which can seem counter-intuitive and is easy to forget! If you wish to assign an interrupt a low priority do NOT assign it a priority of 0 (or other low numeric value) as this can result in the interrupt actually having the highest priority in the system – and therefore potentially make your system crash if this priority is above configMAX_SYSCALL_INTERRUPT_PRIORITY.
The lowest priority on a ARM Cortex-M3 core is in fact 255 – however different ARM Cortex-M3 vendors implement a different number of priority bits and supply library functions that expect priorities to be specified in different ways. Use the supplied examples as a reference.

Each port #defines ‘BaseType_t’ to equal the most efficient data type for that processor. This port defines BaseType_t to be of type long.

Note that `vPortEndScheduler()` has not been implemented.



### Interrupt service routines
The interrupt vector table is contained within FreeRTOS/Demo/CORTEX_LM3S811_KEIL/startup_rvmdk.s, FreeRTOS/Demo/CORTEX_LM3S811_IAR/startup.c or FreeRTOS/Demo/CORTEX_LM3S811_GCC/init/startup.c (for the Keil, IAR and GCC demos respectively) and can be populated as required. In the demo applications the vector table remains in flash.
Unlike most ports, interrupt service routines that cause a context switch have no special requirements and can be written as per the compiler documentation. The macro portEND_SWITCHING_ISR() can be used to request a context switch from within an ISR. See the UART ISR definition within the Keil/ARM LM3S102, GCC LM3S102 or IAR LM3S316 demo applications for an example.



### Switching between the pre-emptive and co-operative RTOS kernels
Set the definition configUSE_PREEMPTION within FreeRTOSConfig.h to 1 to use pre-emption or 0 to use co-operative. The demo application will only execute correctly with configUSE_PREEMPTION set to 0 if configIDLE_SHOULD_YIELD is set to 1.


### Compiler options
As with all the ports, it is essential that the correct compiler options are used. The best way to ensure this is to base your application on the provided demo application files.


### Memory allocation
Source/Portable/MemMang/heap_2.c is included in the ARM Cortex-M3 demo application project to provide the memory allocation required by the RTOS kernel. Please refer to the Memory Management section of the API documentation for full information.


### Serial port driver
It should also be noted that the serial drivers are written to test some of the real time kernel features – and they are not intended to represent an optimized solution.

## Reference

https://www.freertos.org/portlm3s811keil.html