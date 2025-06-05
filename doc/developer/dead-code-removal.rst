Dead Code Removal Notes
======================

This document records the rationale for dropping several inactive code
segments in the repository. The affected areas were wrapped in ``#if 0``
blocks or referenced functions that had no callers. Removing them reduces
maintenance overhead and eliminates misleading stubs.

Removed Sections
----------------

* ``eigrpd/eigrp_northbound.c`` – An old MTU retrieval in
  ``redistribute_get_metrics`` was protected by ``#if 0`` and never
  compiled. The EIGRP northbound metrics parsing works without it.
* ``lib/frrcu.c`` – The RCU watchdog contained a placeholder block for
  printing backtraces. The block was disabled and replaced with a small
  TODO comment.
* ``lib/libfrr.c`` – Code referencing Linux abstract sockets was disabled
  because it bypassed permission checks. The project does not support
  abstract sockets, so the block was removed.
* ``lib/module.c`` and ``lib/module.h`` – Stubs for ``frrmod_unload`` were
  declared under ``#if 0``. No implementation existed, so the unused
  declarations were dropped.
* ``lib/northbound.c`` – Several debug helper functions for configuration
  diffing were guarded by ``#if 0``. They cluttered the file and were never
  built.
* ``lib/typesafe.c`` – Hash and heap consistency check helpers were
  disabled. The remaining macros call no-op definitions now, matching the
  previous behavior.
* ``lib/vty.c`` – An optional restore of the candidate configuration after
  an edit failure was left as a commented possibility. This branch was not
  used.
* ``vrrpd/vrrp.c`` – VRRP startup checks for administrative state were
  commented out. Actual state validation uses other conditions, so these
  lines were dropped.
* ``zebra/kernel_socket.c`` – A metric update during interface address
  processing was disabled in ``#if 0``. The kernel does not set the field
  consistently, so the code was unused.

Testing Strategy
----------------

To ensure no regressions arise from deleting the unused code:

1. **Build FRR**:
   Run ``./bootstrap.sh`` followed by ``./configure`` and ``make`` to
   compile all daemons and unit test binaries.
2. **Execute unit tests**:
   Invoke ``python3 tests/runtests.py -v`` to run the test suite. These
   tests exercise core libraries and daemons.
3. **Run integration tests (optional)**:
   The `topotests` harness under ``tests/topotests`` can validate protocol
   interactions in network topologies if the environment permits.
4. **Deploy in a lab**:
   Start the FRR daemons using typical configurations and observe normal
   protocol operation. Basic commands such as ``show ip route`` should work
   as before.

Since each removed block was never compiled or referenced, successful
execution of the existing test suites and ordinary daemon operation
confirms that no behavior changed.
