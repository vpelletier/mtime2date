mtime2date - Set system date from latest of files' modification time.

Intent
------

On platforms without battery-backed RTC (ex: raspberry pi without extra
boards), system thinks it is at Jan, 1 1970 until date is updated from some
external source (user, NTP, GPS, ...). These sources are only available after
some time (log-in and type command, reach peers, get a fix, ...), which can
cause issues, such as for example:

- bind9 not being able to use RSSIG records to validate other record (DNSSec)

- ntpd not being able to authenticate time server

- time-dependent rng being seeded with predictable data on each reboot

mtime2date only depends on having a root file system with selected files
having a recent-enough modification time (from last time system was up). It
does not need to write anything on system shutdown. Of course, it does not
provide a precise time, but given periodic logging (if you are on raspberry pi
you may have rngd to feed hardware RNG output to kernel entropy pool, and rngd
logs once per hour) it is a very convenient way to get a reasonable time set
once at boot, before most processes start.

Limitations
-----------

Obviously, mtime2date will only work if:

- log files (or any regularly-modified file) is available

- file data is stored on a medium outliving downtime

mtime2date will provide best results if used files are written as late as
possible prior to shutdown, and if system is regularly powered on and given
enough time to catch up with its usual time sources before going down again.

mtime2date checks current system time before setting it, to avoid moving
backward. As this check is not atomic (read then set), clock can go slightly
backward - the time for "test"to exit and for the shell to execute "date".

Usage
-----

Stand alone (not recommended)::

# mtime2date /some/file [/some/other/file [...]]

With systemd::

# systemctl enable mtime2date
