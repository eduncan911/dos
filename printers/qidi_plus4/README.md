# DOS - QIDI X-Plus 4 Firmware

The goal of this custom firmware is to adjust the Plus4 to a printer that "Just Works."

Compatible with Qidi firmware version: `1.7.0`

## Installation notes

[Install V1.7.0 firmware](https://github.com/QIDITECH/QIDI_PLUS4/releases/tag/Plus4_v1.7.0) by following [QIDI's offline instructions here](https://wiki.qidi3d.com/en/PLUS4/Manual/firmware-update#plus4-firmware-update-offline).  QIDI's upgrade backs up your `printer.cfg` and `gcode_macro.cfg` for diffing later.

Next, connect to the printer from terminal or PowerShell by using `ssh mks@<printer-ip>` and password `makerbase`.  Then following the rest of the instructions in the root README.md.

## Printer is modified !!

My Plus4 bed's loadcells (nozzle probing) are disabled in firmware due to high-chamber temp errors/always triggered. Therefore, we are using the mag-probe as a standard Virtual EndStop configuration.  This does mean you will need to use the "paper" method to set z-offset and z-end-stop with `probe_calibrate`.

1. Open Fluidd in a web browser and in Klipper's terminal run `PROBE_CALIBRATE`.  A calibration screen pops up.
2. Use a piece of printer paper and making sure the nozzle is clear of any debris, slide it under the nozzle and use the calibration pop-up to lower Z until you can just barely feel some dragging between the nozzle and bed with the paper.  Press ACCEPT, but do not save!
3. Still within Fluidd, goto the Configuration files, browse to and open the file: `dos/overrides/z_offset.cfg`
4. Scroll to the very bottom of the file, and modify these two variables to match the Probe number you recorded in number 2. above.

```
[stepper_z]
position_endstop = 1.71

[smart_effector]
z_offset = 1.71
```

That's it.  The firmware also enables "baby stepping" and saves the offset for you, if under 0.5mm in adjustments.  So feel free to print a 280mm x 280mm x 0.2mm box and live-adjust the Z to perfection, it should be saved after a completed print (cancelling the print will never save Z offsets).

## Don't modify.  Use overrides.

The modification concept to use here is called overrides: we do not want to modify any of this code (or QIDI's firmware files!).  Instead, you want to take the approach of creating *overrides* that *you* change to your liking.  `dos` is already setup for exactly this.

Once you know what file you want to change, copy it or a portion to the directory `dos/overrides/<any_name>.cfg`.  The key is that the file must end with `.cfg` to be picked up.  For example, my overrides looks like:

```
$ tree ~/printer_data/config/dos/overrides/
/home/mks/printer_data/config/dos/overrides/
├── box1.cfg
├── carbonfiber_bed.cfg
├── [shaketune].cfg
└── z_offset.cfg
```

This is a good habit to pickup because if you stick to overloading configuration files, and not modifying existing, then you are able to perform Updates to both the QIDI firmware and this `dos` firmware without any conflicts.  

### Optional components

I have a number of features and changes to my personal Plus4.  They are all disabled by default here in the repository.  The components are disabled by being suffixed with the word `disabled`, e.g. `feature_name.cfg.disabled`.  To enable any feature, copy and rename the file to `dos/overrides/feature_name.cfg`.  Copying is better than renaming the primary file so you can keep using the Updates feature without loosing any changes.

A few built-in options:

- An internal Nevermore replaces the exhaust fan configuration to run at all times.  Copy and rename `dos/printers/qidi_plus4/nevermore.cfg.disabled` to `dos/overrides/nevermore.cfg`.  Current only a simple Micro V5 Duo (single fan).  I am working on a StealthMax V2 and custom PCB for a future upgrade.
- A prototype carbon-fiber bed is installed in my printer.  

I also have an 8mm carbon-fiber bed that I had to change the sensor type.  I only note as it is disabled, along with the QidiBox1 if you do not have one.  But if you do, you would want to copy and rename that one.

## Credits

None of this would be possible without the great contributions from the Qidi Community.  Their work made for a very capable printer.  
