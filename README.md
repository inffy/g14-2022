# Forked from https://gitlab.com/marcaux/asus-g14-2022

## status suspend

### s0ix

### Fedora
- Works with the patched asus-kernel from https://copr.fedorainfracloud.org/coprs/lukenukem/asus-kernel/
- Or you can use 6.1 (currently in rc) mainline kernel where all the patches are included

### Arch
- Use the linux-g14 kernel from the G14 repo: https://asus-linux.org/wiki/arch-guide/#repo
- Or use the linux-mainline 6.1 (currently rc) from AUR: https://aur.archlinux.org/packages/linux-mainline

### S3

You don't really need this anymore as s0ix is currently working fine and effect on the battery is almost identical

## S3 enablement
By default the system uses s0ix to suspend (modern standby).<br>
There might be some issues with it (speaker not coming back, dead fans).

This can be mitigated by enabling the older suspend method S3 via a modified DSDT table.<br>
It would be nice if you first try to help to fix those problems with our [community](https://www.asus-linux.org) if you have the time and the capacities.

As S3 is just commented out (like on the GA401Q) it is easy to enable it with the script in the following repo:

https://gitlab.com/marcaux/g14-2021-s3-dsdt



## scripts and quirks
- `sudo ./install` to install
- `sudo ./uninstall` to uninstall

### fan-curves
`etc/asusd/profile.conf`

The balanced profile by default has a somewhat silent fan profile resulting in throttlling when gaming.

So I made a quick balanced fan profile that it not too obstrusive, yet powerfull enough to not throttle in my use cases.

It represents my everyday profile while at the desk.

The fans are spinning all the time but on lower temps it is super quiet.
Bacause passive cooling is pretty efficient it does not hit the 70c mark often during my daily tasks and stays pretty quiet during the day.

When launching a game I setup the GPU fans to kick in a little earlier.
In my tests now with the Horizon Zero Dawn benchmark this makes a nice difference and keeps the CPU temps a little bit more in place.
When both hit 80c they run at the same rpm and the noise level is okay and more quiet than the performance profile.

With those values the temps in this benchmark at least are quite good, 85c to max 93c, but mostly below 90c.

By default the profiles are disabled. Enable them via `asusctl fan-curve -m balanced -e true`

I also created a more quiet profile for the power saver profile. The fans kick in a little later than usual so they don't spin up and down so often during my daily tasks.

### autopm
`etc/systemd/system/autopm.service`<br>
`usr/local/bin/autopm`

adjusts the CPU gov depending on the power profile and if on AC / BAT while on the power saver profile<br>
ryzenadj and iGPU profiles can also be adjusted if needed

### restart upower after suspending
`etc/systemd/system/upower_restart.service`

upower sometimes is stuck with the battery state before it entered suspend.
this services restarts upower while resuming.
