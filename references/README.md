# Datasheet and Technical-Reference Register

I will keep links to the manufacturer datasheets and technical references used in this project here. I will prefer official manufacturer documents and record the exact part or circuit decision supported by each source.

Last reviewed: 21 July 2026

## Component datasheets

| ID | Component | Manufacturer | Document | Used for |
|---|---|---|---|---|
| REF-001 | FQP47P06 P-channel MOSFET | onsemi | [FQP47P06 datasheet](https://www.onsemi.com/download/data-sheet/pdf/fqp47p06-d.pdf) | Drain-source rating, gate rating, on-resistance, package and pinout |
| REF-002 | SMCJ TVS series, including SMCJ18A | Littelfuse | [SMCJ Series datasheet](https://www.littelfuse.com/assetdocs/tvs-diodes-smcj-datasheet?assetguid=37388813-0d6d-4329-969b-1aa8b7614ac1) | Standoff, breakdown, clamp, pulse power, polarity and package ratings |
| REF-006 | RSS060P05HZG P-channel MOSFET | ROHM | [RSS060P05HZG product page and datasheet](https://www.rohm.com/products/mosfets/automotive/single-pch/rss060p05hzg-product) | Active automotive-grade physical candidate, voltage, current, on-resistance and SOP8 package |
| REF-007 | EDZV12B 12 V Zener diode | ROHM | [EDZV12B product page and datasheet](https://www.rohm.com/products/diodes/zener-diodes/standard/edzv12b-product) | Gate-clamp voltage, test current, power and SOD-523 package |
| REF-008 | EDZVFH12B automotive 12 V Zener diode | ROHM | [EDZVFH12B product page and datasheet](https://www.rohm.com/products/diodes/zener-diodes/automotive/edzvfh12b-product) | Automotive-grade physical gate-clamp candidate |
| REF-014 | LT8610 synchronous buck regulator | Analog Devices | [LT8610 product page](https://www.analog.com/en/products/lt8610.html) and [Rev. D datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/lt8610.pdf) | Input range, output capability, 5 V reference circuit, feedback, soft-start, switching frequency, power-good and layout requirements |
| REF-015 | ATmega4809 microcontroller | Microchip | [ATmega4808/4809 complete datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/ATmega4808-09-DataSheet-DS40002173C.pdf) | Supply range, memory, peripherals, package, clock and UPDI programming requirements |

## Design references

| ID | Topic | Publisher | Document | Used for |
|---|---|---|---|---|
| REF-003 | P-channel MOSFET reverse-battery protection | Texas Instruments | [Basics of Ideal Diodes](https://www.ti.com/lit/an/slvae57b/slvae57b.pdf) | MOSFET body-diode start-up and reverse-polarity operating principle |
| REF-004 | Automotive reverse-battery protection | Texas Instruments | [Addressing Automotive Reverse Battery Protection](https://www.ti.com/lit/SNOAA94) | Automotive input-protection architecture and limitations |
| REF-005 | Denso relay family data | Denso Electronics | [Relay product specifications](https://www.denso-electronics.com/en/basic-performance.php) | Initial relay-package, coil-current and contact-current reference values |
| REF-009 | LTspice software and learning resources | Analog Devices | [Official LTspice page](https://www.analog.com/en/resources/design-tools-and-calculators/ltspice-simulator.html) | Simulator overview, downloads and official learning material |
| REF-010 | Transient simulation | Analog Devices | [LTspice Basics: Transient Simulations](https://www.analog.com/en/resources/media-center/videos/6368296727112.html) | Pulse stimulus, transient directives and waveform probing |
| REF-011 | Third-party SPICE models | Analog Devices | [Importing third-party models](https://www.analog.com/en/resources/technical-articles/ltspice-how-to-import-third-party-models.html) | `.MODEL`, `.SUBCKT`, directives and model linking |
| REF-012 | Parameter sweeps | Analog Devices | [Using the `.STEP` directive](https://www.analog.com/en/resources/technical-articles/ltspice-using-the-step-command-to-perform-repeated-analysis.html) | Repeated analysis over component and parameter values |
| REF-013 | Piecewise-linear sources | Analog Devices | [Defining PWL voltage and current sources](https://www.analog.com/en/resources/technical-articles/ltspice-piecewise-linear-functions-for-voltage-current-sources.html) | Controlled supply ramps and time-voltage waveforms |
| REF-016 | Arduino Nano Every development platform | Arduino | [Nano Every hardware documentation](https://docs.arduino.cc/hardware/nano-every/) | Development-board capabilities and the prototype-to-custom-PCB controller plan |

## Reference policy

I will not treat an online marketplace listing as a component datasheet. Marketplace listings may help me identify a relay or obtain a test sample, but I will verify electrical ratings, pin assignments and mechanical dimensions from a manufacturer document or a physical sample before finalising the design.
