NetTraffic
==========

Version: 0.1.0

A lightweight Bash script that displays real‑time network traffic volume (bytes and packets) per remote IP address. It captures TCP and UDP packets on the default network interface, aggregates statistics, and refreshes the screen after every packet – giving you an instant overview of who your machine is talking to and how much data is flowing.

Requirements
------------

- Linux (any major distro)
- tcpdump and gawk command-line utilities (see Installation below)

Dependencies
------------

The script relies on two common command‑line tools tcpdump and gawk, both available in the official repositories of all major Linux distributions.

Arch Linux / Manjaro
--------------------

sudo pacman -S tcpdump gawk

Debian / Ubuntu
---------------

sudo apt update && sudo apt install tcpdump gawk

RHEL / CentOS / Fedora
----------------------

sudo yum install tcpdump gawk

> On older RHEL/CentOS systems using yum:
> sudo yum install pulseaudio-utils

SUSE / openSUSE
---------------

sudo zypper install tcpdump gawk

> **Note**: The script must be run with root privileges because `tcpdump` requires elevated permissions to capture packets.

Usage
-----

1. Save the script
   Copy the script into a file named `nettraffic` (or any name you prefer).

2. Make it executable 
   ```bash
   chmod +x nettraffic

3. Run as root
   sudo ./nettraffic

How It Works
------------

1. Auto‑detection 
   The script uses `ip route` to find the default network interface and `ip addr` to obtain its IPv4 address. If detection fails, it exits with a helpful error message.

2. Packet capture  
   `tcpdump` is launched on the detected interface with the options `-n` (no name resolution), `-l` (line‑buffered), `-q` (quick output), and a filter for TCP and UDP traffic. Error messages are suppressed to keep the display clean.

3. Parsing and aggregation
   The output of `tcpdump` is piped into an `awk` script that:
   - Splits the source and destination fields to extract IPv4 addresses and ports.
   - Determines the direction of the packet (IN or OUT) by comparing with the local IP.
   - Extracts the packet length (if present; some packets like pure ACKs have no length field and are counted with 0 bytes).
   - Updates per‑remote‑IP counters for outbound/inbound bytes and packets.

4. Real‑time display
   After every packet, the screen is cleared (using ANSI escape codes) and a table is redrawn showing all remote IPs encountered so far, along with cumulative out/in bytes and packets.

5. Clean exit  
   Pressing `Ctrl+C` triggers a `trap` that restores the terminal and exits gracefully.

You will see a continuously updating table similar to this:

Traffic per Remote IP (updated every packet)
------------------------------------------------------------
Remote IP            Out Bytes       In Bytes        Out Pkts   In Pkts
------------------------------------------------------------
192.168.1.100        14520           2890            120        45
8.8.8.8              1024            512             8          4
...

Press Ctrl+C to stop monitoring.

License
-------

Distributed under the GNU General Public License v3.0.
See LICENSE.txt for details.
