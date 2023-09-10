# LAB 3: Interrupts & Timers

**OBJECTIVES**
-	Learn the difference between polling and interrupts
-	Configure and implement hardware Interrupts
-	Configure and implement various Timer
-	Understand how to configure the IR-based sensor & ultrasonic sensor


**EQUIPMENT** 
1.	A laptop that has the Pico C/C++ SDK installed
2.	Raspberry Pico W
3.	Micro-USB Cable
4.	F-F Jumper wire (at least 4)

> [NOTE]
> Only students wearing fully covered shoes are allowed in the SR6A lab due to safety.

## **INTRODUCTION** 

Embedded systems can interact with their surrounding environment in many ways. Whether sensing or actuating, there are two primary mechanisms initiating actions: event-triggered and time-triggered. In this lab, we will first learn how to use interrupts which are the building blocks for event-triggered functionality. Lastly, we will built code that uses interrupts that can be used by the IR sensor as a wheel encoder.

Subsequently, we shall learn how to use time-triggered events, also known as Timers. A timer is a specialised type of clock that is used to measure time intervals. A timer that counts from zero upwards for measuring the time elapsed is often called a stopwatch. It is a device that counts down from a specified time interval and generates a time delay. A timer can also be used as a counter that keeps track of the number of times a particular event or process occurred regarding a clock signal. It is used to count the events happening outside the microcontroller. 

## **HARDWARE INTERRUPTS** 

A hardware interrupt is an electronic signal that alerts the microprocessor of an event. An interrupt can be triggered by either an internal peripheral (e.g. timer) or an external device (e.g. button).

In the previous lab session, polling was used to detect when a button was pressed. When polling is used, the microprocessor repeatedly checks whether the event has occurred. In the case of a button, the value of the GPIO pin is read to determine if it is high (unpressed) or low (pressed). Once the button is pressed, the microprocessor detects it quickly since it is always active and does nothing but check this single condition, as shown in Figure 1 (bottom). 

While polling is a simple way to check for state changes, there's a cost. If the checking interval is too long, there can be a long lag between occurrence and Detection, and you may fail to see the change entirely if the state changes back before you check. A shorter interval will get faster and more reliable Detection and consume much more processing time and power since many more checks will come back negative.

An alternative is to configure an interrupt on the button's GPIO pin such that when a pre-configured trigger condition is met, an interrupt is generated. With this approach, the microprocessor can enter a low-power sleep state and be woken up with the interrupt. The MSP432 can detect signal changes (e.g. rising or falling edges) to generate an interrupt, as shown in Figure 1 (top). If a GPIO pin is configured to be pulled up, a falling edge will occur when the button is pressed, pulling the pin to the ground. Interrupts are thus better suited to handle asynchronous events.

![Screenshot of polling](https://www.renesas.com/sites/default/files/inline-images/fig1-interrupts-vs-polling-en.jpg)

A dedicated or grouped interrupt is triggered, depending on the source of the interrupt. For peripherals like GPIO ports, multiple pins could produce the same interrupt. In these cases, it is necessary to query the pin's interrupt vector register to identify the interrupt's exact source. Typically, this is done inside the ISR. Once an ISR identifies the source of the interrupt, it can react accordingly. Typically, ISRs execute in a privileged mode that can mask other interrupts. Hence, ISR should be as short as possible and only set application-specific flags to indicate to the microprocessor's main thread to execute the corresponding task in response to an interrupt.


