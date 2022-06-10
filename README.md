+==================================================+
|                     WARNING                      |
|  Yaku-NS is in the beta stage of development     |
| possibly unstable, insecure, sperimental code.   |
|               USE IT AT YOUR RISK.               |
+==================================================+

-----------
ADVERTISING
-----------

  Yaku-ns is GPLed, but you can get a BSD-like license for your
  own company usage (for example to develop a closed-source fork
  and put yaku-ns in some router, appliance, ...) for 1000 EUR.
  The licence can't be sold to others, and applies to the current
  CVS source code, that is, you can't use code that I put
  in the CVS _after_ you already got the license.
  There aren't time limits. After you got the license you can
  modify the code and sell it in binary form, inside a ROM,
  where you want, forever.

  For more information please write to antirez@invece.org

--------
OVERVIEW
--------

  Yaku-NS is a DNS server that implements a subset of the
  DNS protocol.

  For a full featured DNS server check the BIND
  For a very secure DNS server (not complete, with a silly license) check DJBDNS
  For an alternative (almost complete) DNS server implementation check DENTS
  For a new upcoming GPL DNS implementation check MARADNS

  The goal is to provide an easy to configure GPLed alternative for:

	* A DNS server for myself. I'm unable to configure bind
	* A primary/secondary DNS server
	* Forward only DNS server
	* Dial-up systems
	* Embedded systems

----------------------------------
HOW TO SETUP YAKU IN YOUR UNIX BOX
----------------------------------

  Yaku-NS runs without root privileges in a chroot jail.
  We provided this features for your security, please enable
  this features!

COMMAND LINE OPTIONS
~~~~~~~~~~~~~~~~~~~~

  usage: yaku-ns [-p <port>] [-f <port>] [-C <max>] [-F <max>]
                 [-T <forward_timeout>] [-c <config_file>]
                 [-l <logfile>] [-r <chroot jail>] [-u owner]
                 [-b <addr>] [-xdDhV]

-p <port>	Use the UDP port <port> for the DNS service.

-P <port>	Use the TCP port <port> for the DNS under TCP service.
		The only two requests accepted under TCP are IN/AXFR and IN/SOA.

-f <port>	Query the external DNS servers to the port <port>.

-C <size>	Use a cache of size <size>, 0 is no-cache.

-F <max>	Use a forwarded request queue of size <size>, 0 turn off
		the forwarding.

-T <timeout>	Forwarded requets expire in <timeout> seconds.

-c <file>	Use the config file <file>: WARNING you must specify
		the absolute path here, since the server chdir() to "/".
		Also note that this path is relative to the chroot jail.
		Use "-" as filename to provide the configuration from the
		standard input.

-l <file>	Use the <file> for yaku-ns logs. Remember to use an
		absolute path, relative to the chroot (if used).

-r <directory>	Chroot to <directory>.

-u <username>	Run as <username> (default is nobody, not so secure).

-b <address>	Bind only the interface with IP address <address>.

-x		Enable the TCP services.

-d		Demonize.

-h		Show a little help.

-V		Verbosity level: -V: low level, -VV medium level, -VVV
		high level of verbosity. -VVV is raccomanded for new users.

  See how to create a configuration file using the self-commented
  config file at Documentation/yaku-ns.conf.example

  Follow this steps to install Yaku-NS in a unix-like system:

COMPILING YAKU-NS
~~~~~~~~~~~~~~~~~

  $ vi tunable.h (optional)
  $ make

INSTALLING ENS
~~~~~~~~~~~~~~

  Create a system user "yaku" in the "yaku" group.

  # mkdir /usr/local/yaku-ns
  # chown yaku:yaku /usr/local/yaku-ns
  # chmod 700 /usr/local/yaku-ns
  # cp yaku-ns /usr/local/yaku-ns
  # cp Documentation/yaku-ns.conf.example /usr/local/yaku-ns/yaku-ns.conf
  # chown root:root /usr/local/yaku-ns/*
  # chmod 755 /usr/local/yaku-ns/yaku-ns
  # chmod 644 /usr/local/yaku-ns/yaku-ns.conf
  # touch /usr/local/yaku-ns/yaku-ns.log
  # chown yaku:yaku /usr/local/yaku-ns/yaku-ns.log
  # chmod 644 /usr/local/yaku-ns/yaku-ns.log
  # cd /usr/local/yaku-ns
  # ls -l

  The output of the ls -l command should be like the following:

  total 168
  -rwxr-xr-x    1 root     root       153306 Dec 26 14:48 yaku-ns
  -rw-r--r--    1 root     root        10107 Dec 26 14:49 yaku-ns.conf
  -rw-r--r--    1 yaku     yaku            0 Dec 26 14:53 yaku-ns.log

CONFIGURING YAKU-NS
~~~~~~~~~~~~~~~~~~~

  Edit the example self-commented configuration file
  /usr/local/yaku-ns/yaku-ns.conf and create your configuration.

RUNNING YAKU-NS
~~~~~~~~~~~~~~~

  To run Yaku-NS inside a chroot jail (raccomanded!)
  with TCP services enabled just use:

  # /usr/local/yaku-ns/yaku-ns -xd -u yaku -r /usr/local/yaku-ns/ -c /yaku-ns.conf

  Note that the configuration file is specified using the absolute
  path, relative to the chroot.

SIGNALS
~~~~~~~

  Yaku-NS performs different action if some signal is received:

  SIGHUP:	Reload the configuration (use it when your records was updated)
  SIGUSR1:	Force yaku-ns to log some debugging information
  SIGUSR2:	Switch the forwarding ON/OFF

-------
GETZONE
-------

  Getzone is a trivial utility that performs an AXFR request
  over TCP to some primary DNS server and produces as output
  the zone in a format compatible with the Yaku-NS configuration file.

  You can use it to create a rudimental secondary DNS (slave)
  but a better solution will be on the road ASAP (It wasn't ASAP
  since I use only external secondary nameservers, so yaku-ns
  is my primary DNS server).

  Usage: getzone -z <zone> -s <server IP address> [ -p <port> ]

    -z		The zone to download
    -s		The IP address of the master server
    -p		Specify a different destination port, the default is port 53

  Example:

  $ getzone -z test.org -s 1.2.3.4 > salve-db.test.org

  Than you need to send a SIGHUP to Yaku-NS to force a local RRs reload.
  Obviously you need to add an include keyword in the yaku-ns.conf
  to include the zone. See the example configuration file for more
  information.

Have fun,
antirez
