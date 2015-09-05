iocage (experimental)
=====================

**A FreeBSD Jail Manager**

iocage is a zero dependency, drop in jail/container manager amalgamating some
of the best features and technologies the FreeBSD operating system has to offer.
It is geared for ease of use with a simple and easy to understand command
syntax.

This is an experimental fork of iocage, for the purpose of rapidly exploring new
features and ideas. It is a sandbox for coming up with creative solutions to
problems and for testing changes. This is not a drop in replacement for upstream
iocage, it is not intended to be used in any production environment, there is no
goal of overtaking the upstream project. The objective is to write clean code
and implement features that are necessary for use cases the original authors
have expressed no desire to support.

This fork focuses on code quality and correctness. A major cleanup of the
upstream code base was performed to make iocage more pleasant to work on. By
enforcing more consistent style and naming conventions, making functions more
concise and easier to reason about, and introducing a separation between user
interfaces and the implementation of functionality, this fork makes new features
easier to introduce.

Users should be aware that this repository may at times be broken. This is a
development tree that potentially introduces breaking changes, and the code in
this tree should not be considered release quality or suitable for production
use at this time. Testing and bug reports are very welcome. The documentation is
unfortunately difficult to maintain. Currently the `iocage help` documentation
is the most accurate and up-to-date reference for using the latest changes.

The jail formats of this fork and upstream iocage are basically compatible, but
the command line interfaces are diverging. The goals of enhancing our jail
format and command line interface mean that upstream compatibility takes a lower
priority.

- **IRC**: For general discussion and brainstorming on experimental
           feature ideas, feel free to join [Freenode #freqlabs](https://www.irccloud.com/invite?hostname=irc.freenode.net&channel=%23freqlabs).

**CURRENT GOALS**
- Enhance iocage with more zfs-like functionality, syntax
- Improve portability of jails across hosts
- Find and handle catastrophic edge cases
- Prefer excess of understandable functions over few, unreasonably complex ones
- Avoid wasteful overhead, use of global state
- Track useful features from upstream

**FUTURE GOALS**
- Extend the basejail concept to allow for specifying a custom base
- Hierarchical jails, layering/inheritance
- Consolidate redundant documentation files
- Test suite for proving correctness

**NON-GOALS**
- Git integration for iocage

**FEATURES**
- Templates, clones, basejails, fully independent jails
- Ease of use
- Zero configuration files
- Rapid thin provisioning within seconds
- Automatic package installation
- Virtual networking stacks (vnet)
- Shared IP based jails (non vnet)
- Resource limits (CPU, MEMORY, etc.)
- Filesystem quotas and reservations
- Dedicated ZFS datasets inside jails
- Transparent ZFS snapshot management
- Binary updates
- Differential jail packaging
- Export and import
- And many more!

**ADDITIONAL ENHANCEMENTS**
- Concurrent startup and shutdown of jails by rcboot and rcshutdown, based on
  the priority of the jail
- Improved error messages
- Getopts option parsing for more flexible and familiar operation
- Versioned jail format for keeping track of changes between iocage versions
- Prefix matching for tags in addition to UUIDs
- A user property is used for pool activation rather than hijacking the comment
- An alternate pool may be specified for jail_zfs_dataset with the jail_zfs_pool
  property
- The fstab for jails offers `%jailroot%` as a placeholder for the root path of
  a jail
- Option to not replace rc.conf every start (resolver=none, resolver=copy)
- Helpful comments in the code
- Various bug fixes
- And more! :)

**QUICK HOWTO**

Fetch a release:

`iocage fetch`

Create a jail:

`iocage create tag=myjail ip4_addr="em0|192.168.1.10/24"`

Start the jail:

`iocage start myjail`

**REQUIREMENTS**
- FreeBSD 9.3-RELEASE amd64 or newer
- ZFS file system
- Optional - Kernel compiled with:

        # This is optional and only needed if you need VNET and resource
        # limits

        options         VIMAGE # VNET/Vimage support
        options         RACCT  # Resource containers
        options         RCTL   # same as above

**OTHER CONSIDERATIONS**
- Documentation needs work, currently `iocage help`is the most maintained
  reference
- For resource limiting please read rctl(8)
- For the explanations on jail properties read jail(8)
- Create bridge0 and bridge1 interfaces

**HINTS**
- Use iocage set/get to modify properties
- To understand what most properties do read `iocage help`
- If using VNET consider adding the following to `/etc/sysctl.conf` on the host:

        net.inet.ip.forwarding=1       # Enable IP forwarding between interfaces
        net.link.bridge.pfil_onlyip=0  # Only pass IP packets when pfil is
                                       # enabled
        net.link.bridge.pfil_bridge=0  # Packet filter on the bridge interface
        net.link.bridge.pfil_member=0  # Packet filter on the member interface
