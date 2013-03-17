Hostname Setup At Boot Time
===========================

The `hostname-setup` package provides an easy way to setup the hostname of
a system at boot time. This can be quite usefull when creating a prototype
image that is to be used as the base for multiple systems, especially when
one wants to avoid the creation of a custom installer.


Contents
--------

Below please find a list of the main contents of the `hostname-setup`
package:

- **/etc/default/hostname-setup**  
The main configuration. A detailled explanation can be found in the
_Configuration_ section below.

- **/etc/init.d/hostname-prompt**  
A wrapper around `hostname-setup` to be registered with `initrd`. When
invoked, it will check whether the current persistent hostname already
fulfills the `HOSTNAME_SETUP_ALLOW` and `HOSTNAME_SETUP_DENY` patterns
and, if not, prompt the for a new hostname to setup.

- **/etc/init.d/hostname-persist**  
A wrapper around `hostname-setup` to be registered with `initrd`. When
invoked, it updates `/etc/hostname` and `/etc/hosts`, according to the
current hostname.

- **/sbin/hostname-setup**  
The main script. When invoked directly, it  will perform all operations
at once, just like `hostname-prompt` and `hostname-persist` when one's
invoking them in sequence.

Please note that the wrapper scripts are independent from each other. One
could, for example, use `hostname-prompt` only - in order to always request
a hostname to be entered when the system is started, not only during the
first boot.
Anyway, the primary reason for this separation is the fact that the system
determines it's own hostname when `/etc` is still mounted _read-only_.
Thus; persisting the configuration, if required, needs to be done later.

Requirements
------------

Currently, the `hostname-setup` script works with _Debian_ and derivates
like _Ubuntu_, requiring only a base system. The following releases have
been tested and verified so far:

- Debian GNU/Linux 7.x "wheezy"
- Debian GNU/Linux 6.x "squeeze"
- Debian GNU/Linux 5.x "lenny"
- Ubuntu 12.10 "Quantal Quetzal"
- Ubuntu 12.04 "Precise Pangolin"

The package should work with any `initrd`-based system that's using the
`/etc/hostname` and `/etc/hosts` files, without or with minor adjustments.

Other systems haven't been adopted yet. Contributions are welcome!


Installation
------------

Installation differs between various OS and init mechanisms. This package
does NOT include an official installation process, and manual modifications
of boot scripts should be done by experienced operators only!

Anyway, below please find an example for Debian Squeeze, which should work
for most derivates using `initrd`:

    # Fetching latest source from master
    user@host:~$ git clone git://github.com/mjennig/hostname-setup.git

    # Installing the init scripts
    user@host:~$ sudo cp -i hostname-setup/etc/init.d/hostname-* /etc/init.d/
    # Installing the configuration files
    user@host:~$ sudo cp -i hostname-setup/etc/default/hostname-* /etc/default/
    # Installing the main script
    user@host:~$ sudo cp -i hostname-setup/sbin/hostname-* /sbin/

    # Custom configuration
    user@host:~$ sudo vim /etc/default/hostname-setup

    # Enable prompting at boot time
    user@host:~$ sudo update-rc.d hostname-prompt start 01 S
    # Enable persisting at boot time
    user@host:~$ sudo update-rc.d hostname-persist start 01 S

Note: On any `initrd` based system, one should verify the compatibility
of each script's LSB header! See http://wiki.debian.org/LSBInitScripts for
more information about this topic.


Configuration
-------------

All configuration is done by modifying `/etc/default/hostname-setup`. The
contents of this file are pretty self-explanatory:

    # The default hostname whitelist pattern
    : ${HOSTNAME_SETUP_ALLOW:="[[:alnum:]\\-]\\+"}

    # The default hostname blacklist pattern
    : ${HOSTNAME_SETUP_DENY:="localhost"}

    # The default prompt to ask for a new hostname
    : ${HOSTNAME_SETUP_PROMPT:="Please enter a new name for this host: "}

    # The path to the hostname(1) executable
    : ${HOSTNAME_SETUP_HOSTNAME:="`which hostname 2>/dev/null`"}

Note that the (POSIX) regular expressions are interpreted with implicit
leading `^` and trailing `$`, so they must apply to the entire hostname.


Resources
---------

Below please find a list of resources associated with the `hostname-setup`
package:

- Source-Code:                   https://github.com/mjennig/hostname-setup
- Issue-Tracker:          https://github.com/mjennig/hostname-setup/issues


Copyright & License
-------------------

Copyright (c) 2013 Mathias J. Hennig

All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions
are met:

1.  Redistributions of source code must retain the above copyright
    notice, this list of conditions and the following disclaimer.
2.  Redistributions in binary form must reproduce the above copyright
    notice, this list of conditions and the following disclaimer in
    the documentation and/or other materials provided with the
    distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDER AND CONTRIBUTORS
"AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
(INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

