# Tame Your Monitors!

`tym` is a tool with a set of configuration to automatically apply your monitor layout. The layout is applied when you:

 * plug the monitor in
 * unplug it
 * resume from sleep or hibernation

So no more black screens, fiddling with `xrandr` or manually *clicking out* the layout every time you take your laptop with you.

## Dependencies

 * [python](https://www.python.org/)
 * [pm-utils](http://pm-utils.freedesktop.org/wiki/) — if you want to apply layout when resuming
 * [udev](http://www.freedesktop.org/software/systemd/man/udev.html) — if you want to apply layout when (un)plugging
 * [arandr](http://christian.amsuess.com/tools/arandr/) — `unxrandr` command

## Usage

First you need to save your current monitor layout:
```
$ tym save
```

This will put the configuration to `~/.tym`.

All you need to do now is to create a udev rule and pm-utils script so the layout is applied automatically.

### udev rule

If you've run the `save` command as root, this is the udev rule you want:
```
SUBSYSTEM=="drm", ACTION=="change", RUN+="tym apply"
```

If you run it as a different user, you need to switch user before running `tym`:
```
SUBSYSTEM=="drm", ACTION=="change", RUN+="/usr/bin/su -c 'tym apply' ${INSERT_USERNAME_HERE}"
```

You should put the rule to `/etc/udev/rules.d` and name it e.g. `99-tym.rules`.

### pm-utils script

```shell
#!/bin/sh
#
# 40tym: apply saved profile on thaw/resume

case "$1" in
    thaw|resume)
        tym apply
        ;;
esac
```

Same case as with the udev rule: pick correct user:

 * root: `/usr/bin/su -c 'tym apply' ${INSERT_USERNAME_HERE}`
 * user: `tym apply`

This should be in `/etc/pm/sleep.d/` with a name, let's say `40tym`.


## Resources

[How to automatically set up external monitor](https://blog.sleeplessbeastie.eu/2013/01/07/how-to-automatically-set-up-external-monitor/)  
[Writing udev rules](http://www.reactivated.net/writing_udev_rules.html)

## Similar projects

[disper](http://willem.engen.nl/projects/disper/)  
[autorandr](https://github.com/wertarbyte/autorandr)
