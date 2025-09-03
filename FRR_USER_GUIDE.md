# FRRouting User Guide

## Overview
FRRouting (FRR) is an open-source IP routing suite that exchanges routing information, makes policy decisions, and programs the OS kernel with forwarding entries. It supports full L3 configuration from static routes to IPv6 router advertisements, making it suitable for environments ranging from small labs to large Internet exchanges【F:doc/user/about.rst†L7-L17】

## Architecture
FRR uses a modular architecture composed of multiple daemons. Each major routing protocol runs in its own process and communicates with a central `zebra` daemon responsible for coordinating routes and interacting with the dataplane. This design provides resiliency and extensibility since faults in one protocol do not impact others【F:doc/user/about.rst†L53-L64】

All daemons can be managed through the integrated `vtysh` shell, which proxies configuration to each process over UNIX domain sockets and allows a single unified configuration file【F:doc/user/about.rst†L88-L94】

## Supported Protocols
The following routing protocols are provided by FRR out of the box【F:README.md†L12-L29】

- BGP
- OSPFv2 / OSPFv3
- RIPv1 / RIPv2 / RIPng
- IS-IS
- PIM-SM/MSDP
- LDP
- BFD
- Babel
- Policy-Based Routing (PBR)
- OpenFabric
- VRRP
- EIGRP (alpha)
- NHRP (alpha)

## Installation
FRR uses the traditional GNU Autotools build system.
1. Install build dependencies such as autoconf, automake, libtool, and development libraries for your platform.
2. Run `./bootstrap.sh` to generate the build system.
3. Execute `./configure --prefix=/usr --sysconfdir=/etc/frr` to tailor the build.
4. Compile and install with `make && make install`.

Source releases are available on the FRR GitHub releases page, and Debian packages are provided via the project APT repository【F:README.md†L31-L38】

## Basic Setup
After installation, daemons must be explicitly enabled in `/etc/frr/daemons`. Initially all services are disabled (`=no`). Change the desired lines to `=yes` to start protocols such as `zebra`, `bgpd`, or `ospfd` on service restart【F:doc/user/setup.rst†L27-L54】

Enabling `vtysh` applies configuration automatically when daemons start and is recommended for most deployments【F:doc/user/setup.rst†L55-L76】【F:doc/user/setup.rst†L99-L105】

Each daemon accepts options to control operation (e.g., `--daemon` to fork to background, `-A` to specify the VTY address). These options can be set in the same file under `<daemon>_options` entries【F:doc/user/setup.rst†L129-L136】

## Configuration via vtysh
Use `vtysh` for unified configuration:
```shell
sudo vtysh
```
Within the shell, enter configuration mode and define protocol-specific settings. For example, to configure BGP:
```shell
configure terminal
router bgp 65000
 neighbor 192.0.2.1 remote-as 65001
 address-family ipv4 unicast
  network 203.0.113.0/24
 exit-address-family
write memory
```
The `write memory` command saves the running configuration to disk so it is reloaded on service restart.

## Monitoring and Logging
FRR daemons log crash backtraces to `/var/tmp/frr/<daemon>/<pid>/crashlog` and maintain per-thread message buffers for debugging【F:doc/user/setup.rst†L12-L23】

For daemon supervision, `watchfrr` monitors configured processes and can restart them if they exit unexpectedly. Ports for each daemon's VTY interface can be defined in `/etc/services` to allow remote management (e.g., `bgpd` on TCP 2605)【F:doc/user/setup.rst†L141-L159】

## Example Workflow
1. Edit `/etc/frr/daemons` to enable `zebra` and the desired protocol daemons.
2. Start services using `systemctl restart frr`.
3. Connect with `vtysh` to apply configuration.
4. Verify routing tables using protocol-specific `show` commands or the `show ip route` view in `vtysh`.

## Additional Resources
- The official user documentation, including protocol-specific guides and advanced topics, is available at [https://docs.frrouting.org](https://docs.frrouting.org).
- Community support is provided via mailing lists and Slack channels referenced in the project README.

