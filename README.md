# paradigm

Access services on client machines in remote networks as though they are on your server's localhost. The paradigm script is expected to run on a headless, innaccesible unit, on an unknown network, and is designed to operate without supervision. At present, it is run once every minute by a user with server credentials. 

<strong>This script is not ready for public use yet.</strong> Bear with me, this is my first open-source software project.

<h2>Definitions</h2>
<ul>
  <li>ServiceName - Arbitrary name for a local service running on the client, e.g. Shell (for sshd).</li>
  <li>ServicePort - Local port that ServiceName is listening on.</li>
  <li>Playnode - Name arbitrarily assigned to group services together. Usually the hostname of the client machine.</li>
  <li>Portalias - Server-assigned port mapped to the reverse tunnel. This value is retrieved from a server-side flat file before each tunnel is established.</li>
</ul>


<h2>Setup ssh-keys for authentication:</h2>
- on the playnode, cd ~/.ssh
- run ssh-keygen -t rsa -b 4096 -C "user@control.server.com"
- save the files as control_rsa
- enter no passphrase
- ssh user@control.server.com 'mkdir .ssh'
- cat control_rsa.pub | ssh user@control.server.com 'cat >> .ssh/authorized_keys'
- echo "Host control.server.com" >> config
- echo "Hostname control.server.com" >> config
- echo "IdentityFile ~/.ssh/control_rsa" >> config

(http://www.rebol.com/docs/ssh-auto-login.html)


<h2>The Flat File</h2>

Paradigm uses a server-side flat file to store portaliases to simplify connecting and provide access to multiple services on multiple nodes. Each node is able to forward as many ports as required.

File location: jailed user's home directory.<br/>
File name syntax: portalias-SERVICENAME (i.e. ~/portalias-Shell)

Syntax Example
<pre>
	Hegel^1044
	Ricky^1025
	Julian^1026
	Barb^1027
</pre>


<h2>About this script</h2>

The primary reason for this script to exist is so that headless, inaccessible, portable embedded devices may remain accessible from a central server, despite changing geographic or network conditions. We are abstracting the network configuration away. Although this is useful for retaining shell access for maintenance, in the playnode system the target service is actually mpd - the music player daemon. This script facilitates reliable control of local mpd via remote server. Additionally, the SOCKS proxy function is used for use by bittorrent sync. All at once, shell, mpd and bittorrent traffic can flow encrypted through a single tcp port, from a totally firewalled unit.

Server-side control of a local service allows a reduction in stress on the client unit, augmentation of data with meta data, and caching for efficient scaling to many users. For example, the music player client will do less work, because the server only makes 10 requests of it per minute, while relaying that data 10 times as often to users accessing the web site, and augmenting it with album artwork retrieved from Amazon.

By consolidating the local services of many remote clients on the server, commands may be issued and data retrieved quickly, without having to restablish connection and authenticate each time. This facilitates highly responsive server-side control of local services from a WAN connection, without having to configure local networks. For example, a mobile web app to control music can run on a public web server to control a headless music player on the local network. This is a more simple (for the user), robust and reliable method.

This script checks for the existence of, and otherwise initiates a ssh tunnel to reverse-forward a local port to a remote server. It has verbose logs. It is a work-in-progress.

In practice this is useful to manage a fleet of headless embedded media players such as digital signage in retail locations with poor quality computer networks. Remote accessibility to all forwarded service ports persists with minimal network requirements: access to one server over one port.

This is a proprietary script that is being converted to a general purpose utility.
