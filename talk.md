# abstract

If you read HN, we should be using microservices, docker containers, and linux.
And yet I'm giving a talk at BSDCan. It's either bold or very foolish to go
against the grain, and yet it's been generally speaking, bliss.

Let's get cracking!

# why we migrated

Our core platform is a mixture of Perl, Puppet, Erlang, RabbitMQ, Apache
CouchDB, and Kyoto Tycoon. About 18 months ago, it was spread out across more
than 20 virtual servers of various size, all running Debian Unstable.

This was most of the time OK apart from the continual stream of OpenSSL
vulnerabilitues, that caused cascading updates across our entire stack -
reboots, rebuilds, more reboots.  debian unstable was ... unstable.

Debian unstable meant that it was almost impossible to match dev and production,
and with a continual stream of systemd related changes it became a continual
battle against our own infrastructure.

We were burned badly by changes in the base stack that flowed on through to our
production apps, such as perl 5.22 -> 5.24RC that broke Net::HTTP, which we
needed to track down live in production, or changes to the default OpenSSH
which highlighted a number of old format ssh keys by breaking our database
replication.

I really, really, missed zfs snapshots and boot environments. A lot.

With so many tightly coupled VMs any single failure could propagate through
the system.

Our service provider would have weekly transient network hiccups that were long
enough to cause our ssh tunnels to fail over, and later restart, but with all
the VMs reconnecting furiously afterwards, we would end up suffering ephemeral
tcp port exhaustion, blocking both admin ssh and our application-specific ssh
tunnels, and giving us a more severe outage than the original issue.

A couple of experiments with docker fell by the wayside; the linux container
story is still being written and the pace of change itself is a cause for
instability.

# law and order

So what did we want?

- a deterministic platform
- tight control over patch cycles
- robust container support
- previous experience with FreeBSD
- a puppet run
- business problems
- stuck in Docker hell -

# problems

- haproxy vs rabbitmq
- spiped threshold
- jail networking is not obvious
    - clone lo1
    - ngrep/tcpdump see traffic on lo0
-
# thoughts

- systemd-style declaration of services is nice. really. especially process
    cleanup
- if jail.conf and pf.conf had a baby and wrapped it in zfs properties...
- libxo everywhere, all the time
- streaming JSON API of "major" system events
- be able to use zfs send for freebsd-updates

