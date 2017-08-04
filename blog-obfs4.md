 [%sep] [Testing obfs4proxy on FreeBSD](#welcome-ob
fs4-fbsd)

###20170805###

<a id="welcome-obfs4-fbsd">Testing obfs4proxy on FreeBSD</a> by gman999

The Tor Project's [Pluggable Transports](https://www.torproject.org/docs/pluggable-transports.html.en) are a mitigation measure against deep-packet inspection (a.k.a. DPI). Commonly deployed on Tor bridges, [obfs4proxy](https://github.com/Yawning/obfs4/blob/master/doc/obfs4-spec.txt) is the [most common PT in use](https://torbsd.github.io/oostats/bridges-trans-count.txt).

Unfortunately, there is currently no official FreeBSD or OpenBSD support for obfs4, which means that the operating system diversity of obfs4proxy is [dismal](https://torbsd.github.io/oostats/bridges-trans-os.txt).

__TDP__ is working to change that.

While our Tor Browser for OpenBSD doesn't yet support PTs, we've finally embarked on the first steps. Vinicius built a security/obfs4proxy with its two dependencies, security/go-ed25519 and security/go-siphash, and it's on our [GitHub repo](https://github.com/torbsd/freebsd-ports/tree/egypcio/security).

At this point, it needs review and testing.

What can you do?

If you're running a FreeBSD Tor bridge, grab the directories and build it.

Adding obfs4proxy support to a Tor bridge is easy, with the addition of a single line:

```
ServerTransportPlugin obfs4 exec /usr/local/bin/obfs4proxy -enableLogging -logLe
vel info managed
```

Note the logging options with _-enableLogging_ and _-logLevel info_. Neither is required, but being able to watch the service on a bridge is useful, particularly in this phase of development.

With info level logging enable, the log, which by defaults resides in the Tor data directory pt_state/obfs4proxy.log should show something like this:

```
2017/08/05 18:03:29 [NOTICE]: obfs4proxy-0.0.7 - launched
2017/08/05 18:03:29 [INFO]: obfs4proxy - initializing server transport listeners
2017/08/05 18:03:29 [INFO]: obfs4 - registered listener: [scrubbed]:35549
2017/08/05 18:03:29 [INFO]: obfs4proxy - accepting connections
```

Feedback, comments and patches are appreciated, preferably as a [GitHub issue](https://github.com/torbsd/freebsd-ports/issues).

A final general note on obfsproxy. For obvious obfuscation purposes, the TCP port obfs4 listens on is randomized, although the same port will be used with restarts. That causes an issue for anyone running a bridge on a residential connection, where some form of port forwarding by port and protocol is necessary.

There is a simple work-around to that problem in the torrc file. Just add the following line with the preferred TCP port allowing a long-term setting for the necessary port forward:

```
ServerTransportListenAddr obfs4 0.0.0.0:$preferred_port
````

As expected, Yawning Angel's [obfs4 documentation](https://gitweb.torproject.org/pluggable-transports/obfs4.git/tree/README.md) is all you really need.