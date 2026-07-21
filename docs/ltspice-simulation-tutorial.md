# LTspice Simulation Tutorial and Project Notes

## 1. Purpose of this guide

I am keeping this guide as my practical LTspice learning record for Project 2: the Universal 12 V Automotive Relay Tester. I will update it whenever I learn a new simulation method or solve a problem that may be useful later.

This guide records both **how I perform an action** and **why I perform it**. It is not a substitute for a component datasheet, a manufacturer SPICE model or physical testing. A simulation only represents the assumptions and models that I give it.

The menu names and dialog appearance may vary slightly between LTspice versions. These notes are based on the current Windows interface and the controls I have used during this project.

---

## 2. What LTspice is doing

LTspice converts my schematic into a mathematical circuit description called a netlist. It then solves the circuit for the analysis that I request.

| Analysis | What it calculates | Typical use |
|---|---|---|
| Operating point, `.op` | DC voltages and currents at one steady state | Checking bias points before a time simulation |
| DC sweep, `.dc` | Response while a source or parameter changes | Checking operation from 10 V to 16 V |
| Transient, `.tran` | Voltages and currents as functions of time | Relay switching, capacitor charging and transient clamps |
| AC analysis, `.ac` | Small-signal magnitude and phase against frequency | Filters and control-loop response |
| Parameter step, `.step` | Repeats another analysis for several values | Comparing tolerances, battery voltages or loads |

Project 2 mainly needs **transient analysis** because the relay coil, MOSFET switching, capacitors, Zener diode and TVS diode all behave differently during switching events than they do at a fixed DC operating point.

---

## 3. My basic schematic workflow

### 3.1 Start and save the schematic

1. I select **File → New Schematic**.
2. I immediately save the file inside the correct Project 2 simulation folder.
3. I use a descriptive filename and keep numbered revisions when a major circuit block changes.

Example:

```text
hardware/ltspice/01_protected_power_input.asc
```

Saving early matters because model files included with relative paths are normally resolved from the schematic folder.

### 3.2 Place components

I use:

- **F2** to open the component chooser;
- **R** to place a resistor;
- **C** to place a capacitor;
- **L** to place an inductor;
- **V** to place a voltage source;
- **G** to place ground;
- **F3** to draw a wire;
- **F4** to add a net label;
- **Delete** to remove an item.

A circuit must normally have a node named **0**, represented by the ground symbol. Without it, LTspice has no voltage reference and commonly reports a floating-node or singular-matrix error.

### 3.3 Select, move, drag, rotate and mirror components

Good placement is not only cosmetic. A clear schematic helps me see wrong connections, reversed diode polarity and missing junctions before I run the simulation.

#### Move a component before wiring it

1. I press **M** in the current shortcut set, **F7** with the classic shortcut set, or select the **Move** tool.
2. I click the component.
3. I move the pointer to the new position.
4. I click again to place it.
5. I press **Esc** when I am finished.

The Move command relocates the selected object without deliberately stretching its existing wires. I mainly use it before the component is wired or when I intend to redraw the affected wires.

#### Move a wired component while keeping its connections

1. I press **S** for **Stretch** in the current shortcut set, **F8** for **Drag** with the classic shortcut set, or select the named toolbar/menu tool.
2. I click the wired component.
3. I move it slowly to the new location.
4. I click to place it.
5. I inspect every connected wire and junction dot.

Stretch/Drag keeps the wires attached and stretches or reroutes their segments. This is normally the safer choice after a component has been connected.

#### Rotate a component

I select or begin moving the component and press **Ctrl+R**. Each press rotates it by 90 degrees. I can also rotate a floating component before its first placement.

I rotate parts to make signal flow easy to follow. For this project, I generally draw the battery input on the left, protected output on the right and ground returns downward.

#### Mirror a component

I select or begin moving the component and press **Ctrl+E**. Mirroring is useful when a diode, MOSFET or connector needs to face the opposite direction without rotating the entire drawing arrangement.

Mirroring or rotating only changes the symbol orientation. I still check its pin names, pin numbers, diode cathode marking and MOSFET source/drain connections.

#### Move labels and directive text

I can use **Move** to relocate a net label, component reference, component value or SPICE directive. I click the specific text item rather than the component body when I only want to reposition the text.

#### Copy a component

I use the **Copy/Duplicate** command, normally **Ctrl+C** in the current shortcut set or **F6** with the classic shortcut set, click the component and place the copy. I then give the copy the correct value and verify that LTspice has assigned a unique reference such as R2 or C3.

#### Final check after rearranging

After moving or rotating anything, I zoom in and verify:

- wires terminate exactly on pins;
- required junction dots are present;
- wires that only cross do not have an unintended junction;
- no short wire stub was left behind;
- polarity-sensitive parts still face the correct way;
- model names and values remain attached to the correct component.

The precise toolbar icon or shortcut can vary between LTspice releases. If a shortcut behaves differently, I use the named **Move**, **Stretch/Drag**, **Rotate** or **Mirror** command and check **Settings → Operation → Keyboard Shortcuts**. LTspice can also restore the classic shortcut set.

### 3.4 Wire and label the important nodes

I wire components with **F3** and use **F4** to name important nodes such as:

```text
VBAT
PROTECTED_12V
Q1_GATE
Q1_SOURCE
Q1_DRAIN
```

A label electrically joins every node with the same name, even if I do not draw one long wire across the page. Clear labels also make waveform names easier to understand.

---

## 4. Changing component values

### 4.1 Resistors, capacitors and inductors

For a normal passive component:

1. I right-click the component body or its displayed value.
2. I edit the **Value** field.
3. I click **OK**.

Examples:

```text
10k       = 10 kilohms
100n      = 100 nanofarads
47u       = 47 microfarads
1m        = 1 millihenry
```

LTspice accepts engineering suffixes:

| Suffix | Meaning |
|---|---:|
| `T` | tera, 10^12 |
| `G` | giga, 10^9 |
| `Meg` | mega, 10^6 |
| `k` | kilo, 10^3 |
| `m` | milli, 10^-3 |
| `u` | micro, 10^-6 |
| `n` | nano, 10^-9 |
| `p` | pico, 10^-12 |
| `f` | femto, 10^-15 |

A very important LTspice convention is that `M` or `m` means **milli**, not mega. I must write `Meg` for megaohms.

Unit letters are optional. For example, `10k` is safer and simpler than typing `10kΩ`.

### 4.2 Voltage and current sources

For a fixed DC source:

1. I right-click the source.
2. I enter the DC value, such as `12`.
3. I use **Advanced** when I need `PULSE`, `PWL`, `SINE` or another time-dependent waveform.

A fixed 12 V source is useful for a steady operating-point check. A time-dependent source is needed when I want to observe power-up or switching.

### 4.3 Why a diode may open a selection window

In recent LTspice versions, an ordinary right-click on a diode can open the **diode-selection window** instead of a simple editable Value field. This is useful when I want to choose an installed diode model.

If I need to link a generic diode or Zener symbol to my own model:

1. I close the selection window.
2. I hold **Ctrl** and right-click the diode symbol to open the detailed attribute editor.
3. If that does not work, I right-click directly on the visible value text beside the diode.
4. I change the value to the exact model name used in my `.model` statement.
5. I keep the symbol prefix as `D`.

The model name is case-insensitive in SPICE, but I keep the spelling and capitalization identical to avoid mistakes.

---

## 5. What a SPICE directive is

A SPICE directive is an instruction placed as text on the schematic. It can tell LTspice:

- which analysis to run;
- how long to simulate;
- how a custom component behaves;
- which external model file to load;
- which values to sweep;
- which measurements to calculate;
- which numerical options to use.

In the current Windows interface, I can add a directive by:

1. selecting **Edit → SPICE Directive**, or
2. pressing the **period key `.`**;
3. typing or pasting the instruction;
4. clicking **OK**;
5. placing the text in an empty area of the schematic.

Some LTspice versions or keyboard configurations may also open the schematic-text dialog with **S**. I check that **SPICE directive** is selected rather than ordinary comment text.

A directive normally begins with a period:

```spice
.tran 0 100m 0 10u
.model D_TEST D(BV=12 IBV=5m)
.include my_model.lib
.param VBAT=12
.step param VBAT list 10 12 14.4 16
```

Text added only as a comment is visible to me but is not executed by the simulator.

---

## 6. Component value versus component model

These two ideas are different.

- The **value** of a resistor or capacitor directly sets a simple electrical quantity, such as `10k` or `100n`.
- The **model name** on a diode, transistor or MOSFET tells LTspice which set of equations and parameters to use.

For example:

```spice
.model D_TEST D(BV=12 IBV=5m)
```

means:

- `.model`: I am defining a model;
- `D_TEST`: the name I chose;
- `D`: this is a diode model;
- `BV=12`: nominal reverse-breakdown parameter;
- `IBV=5m`: current at which that breakdown parameter is specified.

The diode symbol's displayed value must then be `D_TEST`.

This simple statement is useful for learning and early circuit behaviour, but it is **not automatically an accurate model of a real manufacturer part**. Dynamic resistance, leakage, capacitance, tolerances, temperature behaviour and pulse-energy limits may be missing.

### Project 2 correction and distinction

Project 2 uses two different suppression functions:

- **D2, the MOSFET gate clamp:** the current simulation choice is the ROHM **EDZV12B 12 V Zener** model.
- **D1, the protected-rail transient suppressor:** the design candidate is the **SMCJ18A TVS diode**.

These parts are not interchangeable. A small 12 V gate Zener only protects the MOSFET gate and cannot absorb the energy intended for the input TVS.

I should not use a generic 36 V Zener model as though it represents either of these selected parts. If the exact installed model is available, I select it from LTspice. If it is unavailable, I import the manufacturer's SPICE model or use a clearly labelled simplified model only for an initial behavioural check.

---

## 7. Adding an external model

### 7.1 Model supplied as a `.MODEL` statement

If the manufacturer provides one or more `.model` lines, I can:

1. copy the statement into a SPICE directive on the schematic; or
2. save it in a project model file and load it with `.include`.

Example:

```spice
.include models/gate_zener.lib
```

I then set the symbol value to the model name defined in that file.

### 7.2 Model supplied as a `.SUBCKT`

A `.SUBCKT` model represents a device using a subcircuit with several internal elements. I must:

1. keep the model file with the project;
2. add an `.include` directive;
3. use a compatible generic symbol;
4. set the symbol prefix to `X`;
5. set its value to the exact subcircuit name;
6. verify that the symbol pin order matches the `.SUBCKT` pin order.

A model can simulate without an obvious error while still giving wrong results if its pins are mapped incorrectly. I therefore check the manufacturer's model notes and test the model in a small standalone circuit before trusting it in the full design.

### 7.3 Keeping the simulation portable

I prefer project-relative paths such as:

```spice
.include models/RSS060P05.lib
```

I avoid absolute paths tied to one Windows user folder. Keeping the schematic, symbols and permitted model files together makes the simulation easier to reproduce on another computer.

Before committing a third-party model to GitHub, I must check its redistribution licence. A link to the official download page may be safer when redistribution is not permitted.

---

## 8. Transient simulation

### 8.1 What transient analysis does

Transient analysis calculates how voltage and current change with time. It includes energy-storage and switching effects such as:

- capacitor charging and discharging;
- relay-coil current rise and collapse;
- MOSFET turn-on and turn-off;
- Zener or TVS conduction during a pulse;
- start-up overshoot;
- contact-drive timing.

This is why it is the main analysis for the Project 2 protected input and relay driver.

### 8.2 Adding the transient command

I select **Simulate → Configure Analysis → Transient**, enter the required times and place the generated command on the schematic.

A typical command is:

```spice
.tran 0 100m 0 10u
```

Its practical meaning is:

| Entry | Meaning |
|---|---|
| First `0` | Let LTspice choose the normal output interval |
| `100m` | Stop the simulation at 100 ms |
| Second `0` | Save results from time zero |
| `10u` | Do not let the internal timestep exceed 10 µs |

The stop time must be long enough to include the complete event and recovery. The maximum timestep must be short enough to show the fastest important transition.

A small maximum timestep produces more detail but increases simulation time and data size. A large timestep can miss a narrow spike or make switching time appear inaccurate.

As a starting rule, I choose a maximum timestep much smaller than the fastest event I need to measure, often around one-twentieth to one-fiftieth of that event. I then repeat the run with a smaller maximum timestep. If the result changes materially, the earlier timestep was too large.

### 8.3 Creating a controlled supply ramp with `PWL`

A real supply does not always jump from 0 V to 12 V in zero time. An ideal vertical step can also make convergence harder and may create unrealistic current spikes.

For a controlled 10 ms rise to 12 V, I can set a voltage source to:

```spice
PWL(0 0 1m 0 11m 12 100m 12)
```

This means:

| Time | Voltage |
|---:|---:|
| 0 | 0 V |
| 1 ms | 0 V |
| 11 ms | 12 V |
| 100 ms | 12 V |

LTspice draws straight lines between these points. I use this when I want to define the actual ramp rate myself.

For a more complex automotive waveform, such as a battery dip followed by recovery, I can add more time-voltage pairs. The waveform must still be justified; it should not be called an ISO automotive test pulse unless it actually reproduces the required standard source impedance, timing and energy.

### 8.4 Using `PULSE` for switching

A `PULSE` source is useful for a MOSFET gate or a repeated test command.

Example:

```spice
PULSE(0 5 10m 1u 1u 40m 100m)
```

The entries are:

```text
PULSE(Vinitial Von Delay RiseTime FallTime OnTime Period)
```

For this example:

- initial voltage = 0 V;
- on voltage = 5 V;
- delay before the first pulse = 10 ms;
- rise time = 1 µs;
- fall time = 1 µs;
- on-time = 40 ms;
- period = 100 ms.

I avoid zero rise and fall times unless I have a specific reason. Finite edge times are more realistic and usually easier for the numerical solver.

### 8.5 The `startup` modifier

I can add `startup` to a transient command:

```spice
.tran 0 100m 0 10u startup
```

This starts independent sources at zero and ramps them on briefly at the beginning of the analysis. It can help circuits that do not have a useful normal DC operating point.

However, `startup` is mainly a simulator initialisation aid. It is not automatically a realistic vehicle-battery ramp. When ramp rate affects the engineering result, I define the source explicitly with `PWL`.

### 8.6 The `uic` modifier

`uic` means “use initial conditions” and skips the normal DC operating-point solution.

I do not use `uic` as a routine fix for convergence. Skipping the operating point can create an artificial starting condition or hide a design problem. I use it only when I deliberately want that behaviour and record why.

---

## 9. Running and viewing results

1. I save the schematic.
2. I select **Simulate → Run**.
3. When the waveform viewer opens, I move the probe over a wire and click to plot its voltage.
4. I click a component to plot its current.
5. I hold **Alt** and click a component when I want its instantaneous power, where supported.
6. I use **Plot Settings → Add Trace** for expressions and signals that are not convenient to click.

Useful expressions for Project 2 include:

```text
V(PROTECTED_12V)
V(Q1_GATE,Q1_SOURCE)
I(D1)
I(D2)
I(C2)
```

`V(Q1_GATE,Q1_SOURCE)` is the gate voltage relative to the MOSFET source. This is more meaningful for checking the MOSFET's gate limit than measuring the gate only relative to ground.

The sign of current depends on the component's defined pin direction. A negative plot does not automatically mean a fault; I check the symbol orientation or the generated netlist.

I use waveform cursors to read exact voltage, current, time difference and slope. I do not rely only on the apparent shape of the trace.

---

## 10. Parameter sweeps

Instead of manually changing the battery voltage several times, I can define a parameter:

```spice
.param VBAT=12
```

I set the source value to:

```text
{VBAT}
```

Then I add:

```spice
.step param VBAT list 10 12 14.4 16
```

LTspice repeats the selected analysis at each voltage and overlays the results. This is useful for checking the complete intended operating range.

I use `.step` for:

- minimum, nominal and maximum battery voltage;
- component tolerances;
- several relay-coil resistances;
- alternative gate resistors;
- different capacitor values.

A sweep shows sensitivity, but it does not replace worst-case engineering judgment. I must select the swept limits from datasheets and the project specification.

---

## 11. Automatic measurements

A `.meas` directive calculates a number from the waveform. Examples are:

```spice
.meas tran VPROT_MAX MAX V(PROTECTED_12V)
.meas tran VGS_MIN MIN V(Q1_GATE,Q1_SOURCE)
.meas tran IQ1_MAX MAX I(Q1)
```

After the simulation, I open **View → SPICE Error Log** to see the measurement results.

Measurements help me compare design revisions consistently. I always define an appropriate time window when start-up behaviour should be separated from steady-state behaviour.

Example:

```spice
.meas tran VPROT_MIN MIN V(PROTECTED_12V) FROM 20m TO 100m
```

---

## 12. Project 2 protected-input simulation plan

For the protected 12 V input, I will test one condition at a time so that I understand each result.

### Test 1 — Normal power-up

I will apply a defined 0 V to 12 V ramp and check:

- protected-rail rise;
- Q1 drain-to-source voltage drop;
- Q1 gate-to-source voltage;
- inrush current into 47 µF;
- absence of unwanted TVS conduction.

### Test 2 — Operating-voltage range

I will step the supply through 10 V, 12 V, 14.4 V and 16 V and verify normal conduction and device margins.

### Test 3 — Load steps

I will switch representative loads and observe:

- protected-rail droop;
- capacitor current;
- MOSFET loss;
- recovery time.

### Test 4 — Reverse connection

I will deliberately apply the defined reverse-polarity condition in simulation and verify that the protected rail remains safely blocked. I will check voltage stress across Q1, not only the output voltage.

### Test 5 — Energy-limited positive transient

I will use an appropriate transient source with realistic source impedance and verify:

- TVS voltage and current;
- peak protected-rail voltage;
- Q1 voltage stress;
- D2 gate-clamp voltage and current;
- instantaneous and pulse energy where the model supports it.

An ideal voltage source connected directly across a TVS can demand unlimited current. A meaningful transient test therefore requires source resistance or a proper standardised pulse network.

### Test 6 — Numerical repeat check

I will rerun critical cases with a smaller maximum timestep. I will accept a result only when the important peaks and timing no longer change materially.

---

## 13. Common errors and what they usually mean

| Message or symptom | Likely cause | My first check |
|---|---|---|
| “Unknown subcircuit” | Symbol value does not match a `.SUBCKT` name | Check spelling, `.include` and symbol prefix `X` |
| “Cannot find definition of model” | Diode/transistor value is not linked to a loaded model | Check the Value field and model statement |
| “Singular matrix” | Floating node, missing ground or no DC path | Add ground and inspect capacitor- or gate-only nodes |
| “Timestep too small” | Abrupt ideal switching, conflicting ideal sources or model problem | Add realistic resistance and finite edge times |
| Flat or missing waveform | Wrong node, simulation too short or source not switching | Plot the source and check time units |
| Unexpected negative current | Reference direction is opposite to expected flow | Check symbol orientation and netlist direction |
| Huge current spike | Ideal source charging a capacitor or clamping a transient with no impedance | Add realistic source/wiring resistance |
| Diode chooser keeps opening | Normal right-click is selecting a library diode | Use Ctrl+right-click or right-click its value text |
| Result changes with timestep | Earlier timestep was too large | Reduce maximum timestep and compare again |

I use **View → SPICE Error Log** before randomly changing solver options. The error log usually gives the best clue.

---

## 14. What I record for every accepted simulation

For each circuit block, I will record:

- schematic filename and revision;
- LTspice version;
- model name and source for every important semiconductor;
- model file licence or redistribution restriction;
- input waveform and its source impedance;
- analysis directive;
- component values and tolerance assumptions;
- load condition;
- plotted traces;
- measured peak and steady-state values;
- expected behaviour before the run;
- whether the result matched that expectation;
- timestep-convergence check;
- limitations of the models;
- bench tests required before accepting the design.

A successful run only proves that the mathematical model converged. It does not prove that the physical circuit is safe or compliant.

---

## 15. Quick reference

### Move, drag or rotate a component

- **M** (current) or **F7** (classic): move an object;
- **S** (current Stretch) or **F8** (classic Drag): move a wired object while keeping wires attached;
- **Ctrl+R:** rotate by 90 degrees;
- **Ctrl+E:** mirror;
- **Esc:** leave the current tool.

### Add a component value

Right-click the component or its visible value, edit **Value**, and click **OK**.

### Link a generic diode to a custom model

Set the symbol value to the model name and place the matching directive:

```spice
.model D_TEST D(BV=12 IBV=5m)
```

### Add a directive

Use **Edit → SPICE Directive** or press `.`, enter the command, click **OK**, and place it on the schematic.

### Run a 100 ms transient analysis

```spice
.tran 0 100m 0 10u
```

### Ramp a 12 V supply over 10 ms

```spice
PWL(0 0 1m 0 11m 12 100m 12)
```

### Repeat at several battery voltages

```spice
.param VBAT=12
.step param VBAT list 10 12 14.4 16
```

I set the voltage-source value to `{VBAT}`.

---

## 16. Official learning references

- [LTspice product page and official learning resources](https://www.analog.com/en/resources/design-tools-and-calculators/ltspice-simulator.html)
- [LTspice Basics: Transient Simulations](https://www.analog.com/en/resources/media-center/videos/6368296727112.html)
- [Importing third-party models](https://www.analog.com/en/resources/technical-articles/ltspice-how-to-import-third-party-models.html)
- [Using the .STEP directive](https://www.analog.com/en/resources/technical-articles/ltspice-using-the-step-command-to-perform-repeated-analysis.html)
- [Defining PWL voltage and current sources](https://www.analog.com/en/resources/technical-articles/ltspice-piecewise-linear-functions-for-voltage-current-sources.html)

I will create a separate KiCad simulation and PCB-learning guide when I begin that stage. That guide will record schematic capture, symbol and footprint selection, ERC, SPICE model assignment, simulation setup, PCB layout, DRC, fabrication outputs and the reason for each step.
