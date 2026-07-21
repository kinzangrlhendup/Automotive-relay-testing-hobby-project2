# Design-Decision Log

I will use this document to record the reasoning behind the important technical decisions in this project. I will update a decision if testing shows that an assumption or component choice needs to change.

## DD-001 — Use interchangeable socket adapters

**Decision:** I will use one main tester with keyed, interchangeable relay-socket adapters.

**Reason:** Automotive relays use several packages and terminal arrangements. A modular adapter lets me add or correct a relay footprint without redesigning the protected power, measurement and controller circuits.

**Alternative considered:** Placing every socket permanently on the main enclosure would be quicker to use, but it would make the main PCB larger and would make future package changes difficult.

**Status:** Accepted for the first prototype.

## DD-002 — Limit the first version to nominal 12 V relays

**Decision:** The first version will operate from a nominal 12 V battery and test nominal 12 V electromechanical relays.

**Reason:** Supporting both 12 V and 24 V relays would change the voltage ratings, coil drive, power regulation and test thresholds. I want to validate one safe and understandable design before expanding the operating range.

**Status:** Accepted. A 24 V version remains a possible future development.

## DD-003 — Use a controlled contact load of approximately 1 A

**Decision:** I will test relay contacts with a brief controlled current of approximately 1 A.

**Reason:** A continuity test using only a few milliamps can miss significantly resistive contacts. A 1 A test provides a measurable contact voltage drop while keeping the tester compact and limiting heat.

**Limitation:** This test will not certify the relay at its full advertised contact rating.

**Status:** Provisional until the load circuit and thermal response are tested.

## DD-004 — Use a P-channel MOSFET for reverse-polarity protection

**Decision:** I will use a high-side P-channel MOSFET rather than a series power diode for the first prototype.

**Reason:** A correctly connected P-channel MOSFET can provide reverse-battery protection with much less voltage drop and power loss than a conventional series diode. It is also easier for me to understand and prototype than an ideal-diode controller with external N-channel MOSFETs.

**Selected simulation device:** RSS060P05 model. The RSS060P05HZG automotive-grade SOP8 version is the active physical candidate, subject to thermal, layout and bench validation.

**Status:** Provisional.

## DD-005 — Start with a 2 A input fuse

**Decision:** The first prototype will use a replaceable 2 A fuse close to the positive battery clip.

**Reason:** The estimated normal peak current is approximately 1.4 A, including the 1 A contact test, a relay coil drawing up to 0.3 A and approximately 0.1 A for the controller and measurement circuits. A 2 A fuse provides initial operating margin while protecting the test leads and PCB from a sustained short circuit.

**Status:** Provisional. I will confirm the fuse time-current behaviour after selecting a specific fuse series and measuring the real load pulses.

## DD-006 — Use an 18 V unidirectional TVS on the protected rail

**Decision:** I will initially evaluate an SMCJ18A transient-voltage-suppression diode.

**Reason:** Its 18 V reverse standoff is above the intended 10 V to 16 V operating range, while its breakdown and clamping behaviour can limit positive transients before they reach the downstream circuitry.

**Limitation:** Selecting a TVS part does not by itself prove compliance with an automotive transient standard. The PCB layout, source impedance, pulse energy and downstream component ratings also matter.

**Status:** Provisional until the transient model and test conditions are defined.

## DD-007 — Use available ROHM models for the first LTspice schematic

**Decision:** I will use the RSS060P05 model for Q1 and the EDZV12B model for the MOSFET gate clamp in the first LTspice simulation.

**Reason:** These models are available in my LTspice installation and have the required device types. The RSS060P05 is a P-channel MOSFET rated at −45 V and −6 A. The EDZV12B is a 12 V Zener diode, so it is suitable for evaluating the gate-clamp function.

**Important distinction:** The EDZV12B is only the low-energy gate-protection diode. It is not a substitute for the SMCJ18A input TVS, which must absorb much more transient energy.

**Physical-part note:** The active automotive-grade MOSFET option is RSS060P05HZG in an SOP8 package. The automotive-grade Zener option is EDZVFH12B in a very small SOD-523 package. I will confirm that these packages are practical to assemble before freezing the PCB bill of materials.

**Status:** Accepted for simulation. Physical parts remain provisional.


## DD-008 — Use the ATmega4809 with a 5 V logic rail

**Decision:** I will prototype the firmware with an Arduino Nano Every and use the bare ATmega4809 in a TQFP-48 package on the final custom PCB.

**Reason:** The controller operates at 5 V, provides enough analogue inputs, timers, GPIO, program memory and SRAM for relay measurements, timing, the display and the user interface. The Nano Every gives me USB programming during development, while the final board can use the ATmega4809's single-wire UPDI interface without reproducing the complete development board.

**Implementation note:** The final PCB will require local 100 nF decoupling at each supply pair, bulk rail capacitance, analogue-supply treatment, UPDI programming access and a verified safe state during reset and power-up.

**Status:** Accepted as the controller direction. Final pin allocation and firmware validation remain open.

## DD-009 — Generate the 5 V rail with an LT8610 buck regulator

**Decision:** I will develop the logic supply around the LT8610 using the manufacturer's 5 V, approximately 700 kHz reference values.

**Reason:** The protected input can reach automotive charging voltage and the logic load is provisionally estimated at 250 mA. A linear 7805 would dissipate approximately 1.75 W at 12 V input and 250 mA output, while the synchronous buck converter should be substantially more efficient. The LT8610 accepts up to 42 V and provides more current capability than the tester presently needs.

**Selected simulation network:** 60.4 kΩ timing resistor, 10 nF soft-start capacitor, 4.7 µH inductor, 47 µF output capacitor, 1 MΩ/243 kΩ feedback divider, 10 pF feed-forward capacitor and 100 kΩ power-good pull-up.

**Important limitation:** The high current rating is design headroom, not a reason to omit current and thermal checks. I still need to select a real inductor and capacitors, confirm derating and layout, and determine whether the final physical regulator ordering code meets the qualification level I want for this hobby project.

**Status:** Nominal 12 V to 5 V start-up simulation passed with a temporary 100 Ω load. Input-range, load-step, ripple, tolerance and bench tests remain open.
