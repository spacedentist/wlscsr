# wlscsr
"Wayland Screen Configuration Save and Restore"

## Features
 - can save current screen configuration and easily restore it later
 - does not require to run as daemon in the background
 - can be configured to ignore a head (the built-in laptop screen) when an ACPI
 lid is closed
 - supports using wlr-randr or hyprctl for querying/setting screen config

## Objective
There are already a number of programs available that can automatically restore
screen configuration when screens or connected or disconnected. Unlike those,
wlscsr does not wait for screens to be connected or disconnected. Instead, the
user must manually invoke it to restore a saved screen configuration. This can
be done easily by adding a key binding to your window manager. Why? Because the
user should be able to do manual changes to the screen config when they so
choose, using either command line tools (like wlr-randr, swaymsg or hyprctl),
or graphical tools (like wdisplays). The user stays in control. With a single
keystroke they can restore the saved configuration - but only if and when they
wish.

The current screen configuration can be saved with `wlscsr save`. The saved
configuration for the current set of connected outputs (by checking make,
model, serial number) can be restored with `wlscsr restore`. The latter can be
bound to a key.

For example, this is how to bind it SUPER+O in Hyprland:
```
bindl = SUPER, O, exec, wlscsr restore
```
`bindl` is used, so that this shortcut works even on the lock screen.

## Configuration
wlscsr can be configured using a TOML file called `wlscsr.toml` in an XDG
config file location (e.g. `~/.config/wlscsr.toml`).

### Lid
To configure which head should be ignored when the laptop lid is closed, add a
`[[lid]]` section to the config file with `file` and `head` entries. `file`
must point to the ACPI state file (e.g. `/proc/acpi/button/lid/LID/state` - it
may be slightly different on your system, e.g. it might say `LID0` instead of
`LID`). `head` is the name of the hyprctl output that should be ignored when
the lid is closed. Example:
```
[[lid]]
file = "/proc/acpi/button/lid/LID/state"
head = "eDP-1"
```
You can define multiple such `[[lid]]` sections. While I can't imagine there
are devices with multiple ACPI lids, it might still be useful to have multiple
sections if you use your config on multiple machines. For each `[[lid]]`
section, if the defined ACPI state file says the lid is closed (the contents of
the file end with the letters `closed` after stripping white space), then the
given head is added to the set of ignored heads.
