# GA402R on Fedora 36 (Silverblue)

## status suspend

### s0ix

- parts of the speaker do not come back on resume
- fans are dead after resume

### S3

- does not resume on 5.20-rc0

## S3 enablement
By default the system uses s0ix to suspend (modern standby).<br>
There might be some issues with it (speaker not coming back, dead fans).

This can be mitigated by enabling the older suspend method S3 via a modified DSDT table.<br>
It would be nice if you first try to help to fix those problems with our [community](https://www.asus-linux.org) if you have the time and the capacities.

As S3 is just commented out (like on the GA401Q) it is easy to enable it with the script in the following repo:

https://gitlab.com/marcaux/g14-2021-s3-dsdt

## newer amdgpu firmware
`linux-firmware-20220808-136.4.fc36.noarch`

As of today (2022-08-08) Fedora hasn't received the latest amdgpu firmware, yet (https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/commit/).

It includes the newest yellow carp patches:
https://lore.kernel.org/linux-firmware/MN0PR12MB61010F6FCCB0262CAD3BF7CAE29E9@MN0PR12MB6101.namprd12.prod.outlook.com/T/#u

You can install this package to get the newest version until a new linux-firmware package lands in Fedora. It should then automatically update again.

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
