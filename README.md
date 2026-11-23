# Autonomous Robot — dsPIC33EP512GM306

School project at SeaTech: a small two-wheeled mobile robot that avoids obstacles with
infrared rangefinders. It runs on a **dsPIC33EP512GM306** microcontroller and talks over a
serial link to a Windows PC monitoring app (WPF / C#).

## Contents

```
firmware/       C code for the microcontroller (MPLAB X, XC16 compiler)
pc-interface/   Windows WPF app (.NET 6) for serial monitoring
```

### Firmware

| File | Role |
|---|---|
| `main.c` / `main.h` | Main loop and robot state machine (wait, forward, turn, spin, back up…) |
| `ChipConfig.c` | dsPIC configuration bits and clock |
| `IO.c` | Pin mapping (LEDs, motors, UART) |
| `timer.c` | Timers 1, 3 and 4 (PWM refresh, timestamp, ADC trigger) |
| `PWM.c` | Motor control with speed ramps |
| `ADC.c` | Reads the 5 infrared rangefinders |
| `UART.c` | UART1 at 115200 baud |
| `CB_TX1.c` / `CB_RX1.c` | Circular buffers for UART transmit / receive |
| `UART_Protocol.c` | Frame encoding for the serial protocol |
| `ToolBox.c` | Utility functions |

### PC interface

WPF app that opens the serial port (`COM21`, 115200 baud), decodes incoming frames and
shows the rangefinder distances, the LED states and the motor speeds.

It depends on the `ExtendedSerialPort` library provided during the course, which is not
included here. The `.csproj` expects it in `..\C#\ExtendedSerialPort .NET6\`.

## Serial protocol

```
0xFE | Function (2 bytes) | Payload length (2 bytes) | Payload | Checksum
```

The checksum is the XOR of `0xFE`, the function, the length and every payload byte.

| Function | Payload |
|---|---|
| `0x0080` | Text |
| `0x0020` | LED state |
| `0x0030` | IR rangefinder distances (left, center, right) |
| `0x0040` | Motor speeds (right, left) |

## Build

- **Firmware**: create an MPLAB X project for the dsPIC33EP512GM306 (XC16), add the files
  from `firmware/`, then build and flash with a PICkit or an ICD.
- **PC interface**: open `pc-interface/Courtois_Carpentier_WPF.sln` in Visual Studio
  (Windows, .NET 6).

## Authors

Courtois & Carpentier, SeaTech.
