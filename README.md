# DOS - DuncanOS

`dos` is simply configurations for my Klipper-based 3D printer firmware.  Overall, the purpose is to simplify setup and usage of the machines by centralizing everything into a common set.

## Installation

First, you want to review the release notes of the specific printer you want to use.  For example, to install for the Qidi Plus-4, you would browse to [printers/qidi_plus4/README.md](printers/qidi_plus4/README.md).  

Next, connect to the printer from terminal or PowerShell by using `ssh <username>@<printer-ip>`.

Once logged in, here's a simple one-liner that does a non-distructive installation:

```
curl -fsSL https://raw.githubusercontent.com/eduncan911/dos/main/install.sh | bash
```

Alternatively, for the security-concious of us, here are the manual steps:

```
# change your password!
passwd

# clone the repo and setup the configs
git clone https://github.com/eduncan911/dos.git
ln -sf ~/dos ~/printer_data/config/
echo "[include dos/*.cfg]" >> ~/printer_data/config/printer.cfg

# enable the [dos] klipper plugins
ln -sf ~/dos/common/extras/dos.py ~/klipper/klippy/extras/
ln -sf ~/dos/common/extras/dos ~/klipper/klippy/extras/
```

The above is harmless as it will not actually load anything just yet.  

## How to enable DOS

    cp ~/dos/printer.cfg.example ~/dos/printer.cfg  
    nano dos/printer.cfg
    
    # uncomment the line for qidi-plus4
    #

Now your system should be active after a restart.

## How to disable features

Rename any file to be suffix with `.disabled`.  For example, to disable the CarbonFiber bed option, rename `carbonfiber_bed.cfg` to `carbonfiber_bed.cfg.disabled`.

## How to upgrade DOS

Let's enable automatic updates from Moonraker to be controlled via Fluidd.  SSH into your printer again, and run this command.

```
# modify moonraker to keep DOS up-to-date
cat << EOF >> ~/printer_data/config/moonraker.cfg
[update_manager]
refresh_interval: 24
enable_auto_refresh: True

[update_manager dos]
type: git_repo
channel: dev
path: ~/dos
origin: https://github.com/eduncan911/dos.git
primary_branch: main
is_system_service: False
managed_services: klipper
info_tags:
  desc=DOS Firmware
```
