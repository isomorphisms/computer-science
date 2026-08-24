# Hardwired industrial-control primitives

## Scope

This folder is intentionally here beside the software and communications material. A useful control system can be a switch, two wires, a coil and a contactor. Complexity is not a virtue by itself.

This is a conceptual vocabulary, **not a safety wiring recipe**. Emergency-stop, machine guarding and motor-power circuits must be designed to the applicable machine/electrical safety standards and the actual equipment ratings.

Useful manufacturer references: Rockwell motor starters: https://www.rockwellautomation.com/en-us/products/hardware/motor-control/open-type-starters.html and Siemens example control circuits including emergency stop: https://support.industry.siemens.com/dl/files/095/38752095/att_1310811/v1/Manual_softstarter_3RW30_3RW40_en-US.pdf

## control power
Control circuits commonly use a lower or otherwise separate supply from the motor/load power circuit. Keeping the control-energy path visible makes many machines easier to understand and troubleshoot.

## normally-open contact
A normally-open contact does not conduct in its unactuated state and closes when its device is actuated.

## normally-closed contact
A normally-closed contact conducts in its unactuated state and opens when actuated. Stop and fault chains often exploit this behavior so an open wire tends toward stopping rather than starting.

## momentary pushbutton
A momentary button changes contact state only while pressed. START and STOP stations are the canonical examples.

## selector switch
A maintained selector chooses a state such as HAND/OFF/AUTO, LOCAL/REMOTE or direction/mode.

## relay coil
Energizing a coil mechanically changes one or more associated contacts. The coil is a simple electrical-to-mechanical state primitive.

## auxiliary relay
An auxiliary/control relay provides contacts for logic, isolation and fan-out without directly switching the main motor load.

## contactor
A contactor is a power switching device whose coil controls contacts sized for loads such as motors. It is often the final actuator between a low-power control circuit and substantial electrical power.

## auxiliary contact
A mechanically linked auxiliary contact reports or reuses a contactor/relay state in the control circuit.

## seal-in / holding circuit
A momentary START can energize a coil whose own normally-open auxiliary contact then keeps the coil energized after the button is released. Opening any series stop/fault contact breaks the hold.

## start circuit
The start path is the set of conditions that allows a machine or motor command to become active. A physical start button can be only one contact in that path.

## stop circuit
The stop path removes the run command. Hardwired stop paths remain valuable because their behavior can be understood without booting software or inspecting a network stack.

## remote start / remote stop
Additional contacts can place start/stop stations away from the machine. The control problem can remain nothing more complicated than correctly arranged contacts and conductors.

## interlock
An interlock prevents incompatible states, for example two reversing contactors being energized together. Interlocks may be electrical, mechanical or both.

## limit switch
A limit switch converts physical position or travel into contact state. It is one of the simplest ways to close the loop between mechanism and control circuit.

## overload relay
An overload device protects a motor from sustained overcurrent/overload conditions and commonly opens a normally-closed control contact so the contactor drops out.

## fuse / circuit breaker
Overcurrent protection interrupts fault current and protects conductors/equipment within its rated application. It is not interchangeable with motor overload protection.

## motor starter
A starter packages the functions needed to switch and protect a motor, commonly including a contactor plus overload protection and coordinated branch protection.

## control transformer / control power supply
A transformer or power supply provides the voltage used by coils, buttons, sensors and logic. Its isolation, grounding and protection are part of the actual machine design.

## pilot light / indicator
An indicator gives immediate local state: power available, run, stopped, fault, overload, E-stop active and so on.

## emergency-stop device
An E-stop is a safety function intended for emergency stopping, not an ordinary process-control button. The required architecture depends on risk assessment and applicable standards.

## safety relay / safety controller
Safety-rated logic monitors devices such as E-stops and guards and controls safety outputs with diagnostic/redundancy properties appropriate to the required safety level.

## safe torque off
STO disables a drive's ability to produce motor torque through a safety-related function. It does not automatically provide every kind of stopping behavior; coast time, brakes and risk assessment still matter.

## main disconnect / isolation
A disconnect provides a deliberate means to isolate equipment from power for service or emergency purposes. This is different from merely commanding a controller to stop.
