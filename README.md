zizzania
========

zizzania sniffs wireless traffic listening for WPA handshakes and dumping only
those frames suitable to be decrypted (one beacon + EAPOL frames + data). In
order to speed up the process, zizzania sends IEEE 802.11 DeAuth frames to the
stations whose handshake is needed, properly handling retransmissions and
reassociations and trying to limit the number of DeAuth frames sent to each
station.

![Screenshot](http://i.imgur.com/NG7CyU0.png)

Usage
-----

    zizzania (-r <file> | -i <device> [-c <channel>]
              ([-n] | [-d <count>] [-a <count>] [-t <seconds>]))
             [-b <address>...] [-x <address>...] [-2 | -3]
             [-w <file> [-g]] [-v]

    -i <device>   Use <device> for both capture and injection
    -c <channel>  Set <device> to RFMON mode on <channel>
    -n            Passively wait for WPA handshakes
    -d <count>    Send groups of <count> deauthentication frames
    -a <count>    Perform <count> deauthentications before giving up
    -t <seconds>  Time to wait between two deauthentication attempts
    -r <file>     Read packets from <file> (- for stdin)
    -b <address>  Limit the operations to the given BSS
    -x <address>  Exclude the given station from the operations
    -2            Settle for the first two handshake messages
    -3            Settle for the first three handshake messages
    -w <file>     Write packets to <file> (- for stdout)
    -g            Also dump multicast and broadcast traffic
    -v            Print verbose messages to stderr (toggle with SIGUSR1)

Examples
--------

* Put the network interface in RFMON mode on channel 6 and save the traffic
gathered from the stations associated to a specific access point:

        zizzania -i wlan0 -c 6 -b AA:BB:CC:DD:EE:FF -w out.pcap

* Passively analyze the traffic generated by any station on the current channel
assuming that the network interface is already RFMON mode:

        zizzania -i wlan0 -n

* Strip unnecessary frames from a pcap file (excluding altogether the traffic
generated by one particular station) considering an handshake complete after
just the first two messages (which should be enough for unicast traffic
decryption):

        zizzania -r in.pcap -x 00:11:22:33:44:55 -w out.pcap

* Use [airdecap-ng][aircrack-ng] to decrypt a pcap file created by zizzania:

        airdecap-ng -b AA:BB:CC:DD:EE:FF -e SSID -p passphrase out.pcap

Dependencies
------------

* [CMake][cmake]
* [libpcap][libpcap]

### Debian-based

    sudo apt-get install libpcap-dev

### Mac OS X ([Homebrew](http://brew.sh/))

    brew install libpcap

Build
-----

    mkdir build
    cd build
    cmake -DCMAKE_BUILD_TYPE=Release ..
    make

The install process is not mandatory, zizzania can be run from the `src`
directory. Just in case:

    make install
    make uninstall

Mac OS X support
----------------

In order to sniff packets live and to perform the deauthentication phase
zizzania requires that the network interface/driver supports RFMON mode and
injection. This is known to be troublesome with Mac OS X and hence it is not
directly supported by zizzania.

[aircrack-ng]: http://www.aircrack-ng.org
[cmake]: https://cmake.org/
[libpcap]: http://www.tcpdump.org
