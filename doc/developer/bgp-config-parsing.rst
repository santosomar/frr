BGP Configuration Parsing
=========================

FRRouting's BGP daemon (bgpd) uses the common VTY infrastructure to parse
configuration commands. The key files involved are:

* ``lib/command.c`` – provides the shared CLI backend used by all daemons.
* ``bgpd/bgpd.c`` – installs BGP-specific CLI commands at startup by calling
  ``bgp_vty_init()``.
* ``bgpd/bgp_vty.c`` – defines the main BGP configuration mode, including the
  ``router bgp`` command.
* ``bgpd/bgp_evpn_vty.c`` – adds EVPN-specific configuration commands such as
  ``advertise-default-gw`` for VNIs.
* ``bgpd/bgp_flowspec_vty.c`` – handles FlowSpec-related commands like
  ``local-install`` for local policy routing.

Together these files implement the parsing and execution of BGP configuration
through the VTY interface.
