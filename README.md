# Tame Your Monitors!

`tym` is a tool with a set of configuration to automatically apply your monitor layout. The layout is applied when you:

 * plug the monitor in
 * unplug it
 * resume from sleep or hibernation

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
SUBSYSTEM=="drm", ACTION=="change", RUN+="/usr/bin/su -c 'tym -v apply' ${INSERT_USERNAME_HERE}"
```

You should the rule to `/etc/udev/rules.d` and name it e.g. `99-tym.rules`.

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

This should be in `/etc/pm/sleep.d/` with a name, let's say `40tym`.


## Resources

[auto monitor setup](https://blog.sleeplessbeastie.eu/2013/01/07/how-to-automatically-set-up-external-monitor/)  
[writing udev rules](http://www.reactivated.net/writing_udev_rules.html)

## Similar projects

[disper](http://willem.engen.nl/projects/disper/)  
[autorandr](https://github.com/wertarbyte/autorandr)
