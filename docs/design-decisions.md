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

**Selected prototype device:** FQP47P06, subject to simulation and bench validation.

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
