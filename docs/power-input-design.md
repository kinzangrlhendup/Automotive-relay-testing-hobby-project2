# Protected 12 V Power-Input Design

## 1. Purpose

I am designing this block to connect the tester safely to a nominal 12 V vehicle battery. It must tolerate accidental reverse connection, limit damage from a sustained short circuit and reduce positive voltage transients before they reach the controller and measurement circuits.

This is a design-stage calculation. I will not claim automotive-standard compliance without the relevant laboratory testing.

## 2. Initial power path

The proposed sequence is:

1. Positive battery clip
2. Replaceable fuse close to the clip
3. High-side P-channel reverse-polarity MOSFET
4. Protected 12 V rail
5. TVS diode and local capacitors from the protected rail to ground

## 3. Current budget and fuse

My initial maximum normal-current estimate is:

| Load | Estimated current |
|---|---:|
| Controlled contact test | 1.0 A |
| Relay coil | 0.3 A |
| Controller, display and measurement circuits | 0.1 A |
| Total | 1.4 A |

I selected a provisional 2 A fuse because:

\[
\text{Current margin}=\frac{2.0}{1.4}=1.43
\]

This gives approximately 43% margin above the estimated peak normal current. The contact test will be pulsed rather than continuous, which should reduce fuse heating. I still need to select a specific fuse family and compare its time-current curve with the measured test pulse.

The fuse protects the wiring and PCB against sustained fault current. It does not act quickly enough to protect every semiconductor fault, so the coil and contact drivers will also require electronic current limiting or fast shutdown.

## 4. Reverse-polarity MOSFET

### Selected simulation part

I initially considered the FQP47P06, but it was not available in my LTspice component library. I selected the RSS060P05 model for the first simulation because it provides:

- P-channel operation for a simple high-side protection circuit
- 45 V drain-to-source rating
- Maximum on-resistance of 36 mΩ at a gate-to-source voltage of −10 V
- 6 A continuous drain-current rating under the manufacturer's stated conditions
- Maximum gate-to-source rating of ±20 V
- An available LTspice model

At the estimated 1.4 A operating current:

\[
V_{DS}=IR_{DS(on)}=1.4\times0.036=0.0504\text{ V}
\]

\[
P=I^2R=1.4^2\times0.036=0.071\text{ W}
\]

At the 2 A fuse rating:

\[
V_{DS}=2\times0.036=0.072\text{ V}
\]

\[
P=2^2\times0.036=0.144\text{ W}
\]

These calculations use the maximum stated on-resistance at the specified gate drive and room-temperature test conditions. The resistance will increase with junction temperature, so I will verify the real voltage drop and case temperature during testing.

For comparison, a series diode dropping an assumed 0.4 V at 1.4 A would dissipate:

\[
P=VI=0.4\times1.4=0.56\text{ W}
\]

The MOSFET should therefore produce much less voltage loss and heat in normal operation. Its voltage-rating margin relative to the SMCJ18A maximum stated clamp voltage is:

\[
\text{Voltage-rating margin}=\frac{45}{29.2}=1.54
\]

This is acceptable for the first controlled simulation, but it is less margin than the original 60 V candidate. I will not expose the physical prototype to unsuppressed vehicle transients until I have validated the protection arrangement.

The active automotive-grade physical candidate is RSS060P05HZG. It is an SOP8 surface-mount part rather than a TO-220 through-hole part, so I must also consider assembly method and PCB heat spreading before freezing the physical selection.

### Gate protection

I will initially use a 10 kΩ gate pull-down and an EDZV12B 12 V Zener diode between gate and source. The Zener cathode will connect to the MOSFET source and its anode to the gate. Its purpose is to keep the magnitude of the negative gate-to-source voltage below the MOSFET's ±20 V absolute maximum during positive input transients.

The EDZV12B is specified at 11.74 V to 12.24 V at a 5 mA test current and has a 150 mW power-dissipation rating. At the TVS maximum stated clamp voltage, a first-order estimate is:

\[
I_Z\approx\frac{29.2-12}{10\,000}=1.72\text{ mA}
\]

\[
P_Z\approx12\times0.00172=20.6\text{ mW}
\]

This estimated power is below 150 mW. The actual Zener voltage at 1.72 mA is not guaranteed by the 5 mA datasheet value, so I will inspect the simulated gate voltage and verify it on the bench. The automotive-grade EDZVFH12B has similar nominal electrical ratings and remains a candidate for the physical PCB.

## 5. TVS-diode selection

I selected the SMCJ18A for initial evaluation because its key nominal ratings are:

| Parameter | SMCJ18A value |
|---|---:|
| Reverse standoff voltage | 18 V |
| Breakdown-voltage range | 20.0 V to 22.1 V |
| Maximum clamping voltage at rated pulse current | 29.2 V |
| Peak pulse power for a 10/1000 µs waveform | 1500 W |

The reverse standoff voltage is higher than the intended 16 V maximum normal input, so the TVS should not normally conduct. An appreciably lower standoff voltage could begin conducting during legitimate high battery or charging voltage. A much higher standoff voltage would allow a larger transient to reach the downstream circuits.

Because the maximum specified clamp is 29.2 V under the stated pulse condition, I must rate downstream components for more than 29.2 V. The selected simulation MOSFET is rated at 45 V and the proposed capacitors are rated at 50 V rather than 16 V or 25 V. I will retain the option of a 60 V or higher MOSFET if transient testing shows that the 45 V margin is inadequate.

The TVS must be placed close to the protected input and connected to a short, low-inductance ground return. Long tracks would add inductive voltage during a fast transient and reduce the effectiveness of the clamp.

## 6. Input capacitors

### 100 nF ceramic capacitor

I selected 100 nF as a conventional local high-frequency bypass value. A small ceramic capacitor has lower parasitic inductance than a large electrolytic capacitor and can shunt fast, low-energy noise close to the circuit.

### 47 µF electrolytic capacitor

I selected 47 µF as a provisional local bulk-decoupling value. It can reduce slower supply movement caused by wiring inductance and load steps, but it is not large enough to power the 1 A contact test by itself.

For example, ignoring equivalent series resistance, the voltage change when 47 µF supplies 1 A for 1 ms would be:

\[
\Delta V=\frac{I\Delta t}{C}
=\frac{1\times0.001}{47\times10^{-6}}
=21.3\text{ V}
\]

This confirms that C2 is a local filter and not an energy-storage supply for the contact load. The battery remains the main energy source.

I selected a provisional 50 V capacitor rating because:

\[
\text{Voltage-rating margin}=\frac{50}{29.2}=1.71
\]

This gives approximately 71% margin above the TVS maximum clamp stated for its rated pulse condition. I will also check capacitance derating, ripple current, temperature rating, physical size and equivalent series resistance when selecting the exact part.

## 7. Provisional component list

| Reference | Provisional selection | Design purpose | Status |
|---|---|---|---|
| F1 | 2 A replaceable fuse | Sustained short-circuit protection | Fuse series not selected |
| Q1 | RSS060P05 model | Reverse-polarity protection | Selected for simulation; RSS060P05HZG is the active physical candidate |
| R1 | 10 kΩ | MOSFET gate pull-down | Provisional |
| D1 | SMCJ18A | Positive-transient suppression | Selected for evaluation |
| D2 | EDZV12B 12 V Zener model | MOSFET gate protection | Selected for simulation; physical package under review |
| C1 | 100 nF, 50 V ceramic | High-frequency filtering | Exact part not selected |
| C2 | 47 µF, 50 V electrolytic | Local bulk filtering | Exact part not selected |

## 8. Required validation

Before I accept this block, I will verify:

- Normal operation from 10 V to 16 V
- Voltage drop across Q1 at representative loads
- Reverse-battery blocking
- MOSFET gate-to-source voltage
- TVS behaviour using a safe, energy-limited transient test
- Start-up and disconnection waveforms
- Capacitor voltage and temperature margins
- Fuse behaviour during normal tests and controlled faults
- Safe output state after a short circuit or reversed connection
