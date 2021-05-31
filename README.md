# Convert nvram.ini

## About

This script should be used with Firmadyne-emulated devices.

One way of overriding default NVRAM values in a Firmadyne-emulated devices is to place a file in the `/firmadyne/libnvram.override/` directory of the device's filesystem. Each key should be a separate file with the filename as the key and the contents of the file being the value (no tailing newline "0xa").

An easy way of maintaining custom NVRAM values and quickly making changes is to use a single `nvram.ini` file. For example:

```
ATEMODE=1
debug_cprintf_file=1
apps_u2ec_ex=1
boardflags=0x100
ct_max=300000
ehci_ports=
enable_samba_tuxera=
force_change=1
lan_ifname=br0
lan_ifnames=vlan1 ra0 rai0
lan_wps_oob=enabled
```

## Usage

Place this script in (or link it to) Firmadyne's root directory:
```
andrew ~ $ git clone ssh://git@bitbucket.parsonssecure.us:7999/~f008261c/convert-nvram.git
Cloning into 'convert-nvram'...

andrew ~ $ cd firmadyne/

andrew ~/firmadyne $ ln -s ~/convert-nvram/convert_nvram.sh
```

Note: The script will automatically mount & unmount the device's filesystem.

Run the script while providing the path to the nvram.ini file & the Firmadyne image ID:
```
andrew ~/firmadyne $ ./convert_nvram.sh ~/nvram.ini 1
----Running----
----Adding Device File----
----Making image directory----
----Mounting----
----Running----
----Unmounting----
----Disconnecting Device File----
loop deleted : /dev/loop1
```

## Non-printable Characters

Sometimes, a device will look for a 4-byte integer value with a call to `nvram_get_int()`. You might see logs in your device's console under Firmadyne that look like this:
```
nvram_get_int: Unable to read key: /firmadyne/libnvram/sw_mode!
```

To handle this, the `nvram.ini` file can use any of the escape characters you would normally use with the `echo -e` command. For example:
```
ATEMODE=1
debug_cprintf_file=1
sw_mode=\x1\0\0\0
```

Now, your device's console will show:
```
nvram_get_int: sw_mode
nvram_get_int: = 1
```