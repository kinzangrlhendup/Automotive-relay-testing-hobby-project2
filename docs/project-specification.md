# Project Specification

## 1. Project definition

I am developing a portable electronic tester for common nominal 12 V automotive relays. I want the instrument to do more than confirm that a relay clicks. It should measure the coil and contact behaviour, repeat the test automatically and report a useful reason when a relay fails.

## 2. Initial electrical requirements

| Parameter | Initial target |
|---|---:|
| Nominal supply | 12 V DC vehicle battery |
| Intended operating range | 10 V to 16 V DC |
| Relay coil voltage | Nominal 12 V DC |
| Maximum expected coil current | 300 mA during normal operation |
| Hardware coil-current shutdown target | Approximately 500 mA |
| Contact test current | Approximately 1 A |
| Default test cycles | 10 |
| Contact test duration | Short controlled pulses, to be calculated |
| Logic supply | 5 V or 3.3 V, subject to controller selection |

These values are design targets rather than final verified specifications. I will update them after simulation and prototype testing.

## 3. Relay configurations

I intend to support these configurations in the first version:

| Configuration | De-energised state | Energised state |
|---|---|---|
| Four-pin SPST-NO | 30–87 open | 30–87 closed |
| Five-pin SPDT | 30–87a closed | 30–87 closed |
| Five-pin dual-87 | Both outputs open | Both outputs closed |

I will use the conventional automotive terminal numbers 30, 85, 86, 87 and 87a as logical names. Each socket adapter will translate its physical terminal arrangement into these logical connections.

## 4. User interface

I plan to use:

- A four-pin/five-pin toggle switch
- A momentary TEST pushbutton
- A POWER indicator
- A green PASS indicator
- A red FAIL indicator
- A small OLED display
- An optional buzzer for completion and fault indication

## 5. Measurements

The tester should measure or determine:

- Battery voltage
- Coil current
- Estimated coil resistance
- Open or abnormally low-resistance coil
- Coil suppression component where practical
- Relay contact arrangement
- Pull-in time
- Release time
- Contact bounce or repeated transitions
- Loaded voltage drop across the active contact path
- Successful operations out of ten cycles

## 6. Socket strategy

I will use a universal main tester with interchangeable, keyed socket adapters. Each adapter will contain the physical relay socket, pin mapping and an identification component. This approach will let me add new relay packages without changing the main controller PCB.

My initial adapter targets are:

1. Denso low-height Micro four-pin
2. Standard Mini ISO four-pin and five-pin
3. Alternate Mini ISO terminal arrangement
4. Micro ISO four-pin and five-pin
5. Maxi ISO four-pin and five-pin

## 7. Protection objectives

The tester will include:

- A replaceable fuse close to the positive battery clip
- Reverse-polarity protection
- Automotive transient suppression
- Input overcurrent protection
- Coil-driver current limiting or rapid shutdown
- Protected voltage-sensing inputs
- Temperature monitoring for the contact load where required
- Firmware timeouts that return every output to a safe state

I will not claim formal ISO 7637 or commercial automotive qualification unless the finished hardware is tested to those standards.

## 8. Validation approach

I will validate the design progressively:

1. Simulate each analogue circuit block.
2. Test from a current-limited bench supply.
3. Verify all safe-state and reverse-connection behaviour.
4. Compare voltage, current and timing measurements with a multimeter and oscilloscope.
5. Test known-good relays from different packages.
6. Introduce controlled faults such as series contact resistance or an open coil.
7. Record repeatability, error and temperature data.
8. Connect to a vehicle battery only after the protected input passes bench testing.
