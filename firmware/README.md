# Firmware

I will use the firmware to control the test sequence, protect the hardware and calculate the reported measurements.

The initial firmware will:

- Start with every driver disabled
- Verify battery voltage and adapter status
- Read the four-pin/five-pin selector
- Check the de-energised contacts
- Energise the coil safely
- Record current and switching time
- Apply the controlled contact load
- Repeat the test for ten cycles
- Display the measurements and final result
- Shut down safely if it detects excessive current or an invalid state
