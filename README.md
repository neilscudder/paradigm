# paradigm

Access services on remote, portable, or mobile client machines as though they are on your server's localhost. Tunnels encrypted over ssh forward local ports securly to the server. The paradigm script is expected to run on a headless, innaccesible unit, on an unknown network, and is designed to operate without supervision. At present, it is run once every minute by system cron. 

Installation:
  chmod a+x ./paradigm; cp paradigm /usr/bin [or somewhere in your bin path]
Set to run once per minute in user crontab:
  */1 * * * *  /usr/bin/paradigm -u convict Playnode Shell 22 control  >> /dev/null 2>&1

<strong>Usage:</strong> paradigm [OPTIONS] PLAYNODE SERVICENAME SERVICEPORT SERVER

-h Print this usage text.<br>
-u User account on server. (Default=paradigm.)<br>
-p Server listening port. (Default=22.)<br>
-t Test shortcut using embedded values<br>
  
Examples:<br>
  paradigm Playnode Shell 22 server.ca<br>
  paradigm -u user -p 443 -l ./log.paradigm Playnode MPD 6600 server.ca

<h3>Definitions</h3>
<ul>
  <li>ServiceName - Arbitrary name for a local service running on the client, e.g. Shell (for sshd).</li>
  <li>ServicePort - Local port that ServiceName is listening on.</li>
  <li>Playnode - Arbitrary name referring to the client machine.</li>
  <li>Portalias - Server-assigned port mapped to the reverse tunnel. This value is retrieved from a server-side flat file before each tunnel is established.</li>
</ul>

<h3>Change log 0.1 to 0.6.3</h3>
<ul>
  <li>Parameters moved to CLI args</li>
  <li>Finer grain error reporting</li>
  <li>Much better logging</li>
  <li>More readable code</li>
  <li>Compatible with /bin/dash</li>
  <li>Proxy function removed, focus on core competency</li>
  <li>Added test mode</li>
</ul>

<h3>Setup ssh-keys for authentication:</h3>
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

<h3>The Flat File</h3>

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


<h3>About this script</h3>

The primary reason for this script to exist is so that headless, inaccessible, portable embedded devices may remain accessible from a central server, despite changing geographic or network conditions. We are abstracting the network configuration away. Although this is useful for retaining shell access for maintenance, in the playnode system the target service is usually mpd - the music player daemon (http://www.musicpd.org/). This script facilitates reliable control of local mpd via remote server.

Server-side control of a local service allows a reduction in stress on the client unit, augmentation of data with meta data, and caching for efficient scaling to many users. For example, the music player client will do less work, because the server only makes 10 requests of it per minute, while relaying that data 10 times as often to users accessing the web site, and augmenting it with album artwork retrieved from Amazon.

By consolidating the local services of many remote clients on the server, commands may be issued and data retrieved quickly, without having to restablish connection and authenticate each time. This facilitates highly responsive server-side control of local services from a WAN connection, without having to configure local networks. For example, a mobile web app to control music can run on a public web server to control a headless music player on the local network. This is a more simple (for the user), robust and reliable method.

This script checks for the existence of, and otherwise initiates a ssh tunnel to reverse-forward a local port to a remote server. It has verbose logs. It is a work-in-progress.

In practice this is useful to manage a fleet of headless embedded media players such as digital signage in retail locations with poor quality computer networks. Remote accessibility to all forwarded service ports persists with minimal network requirements: access to one server over one port.

This is a proprietary script that is being converted to a general purpose utility.
