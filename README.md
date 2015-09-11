# paradigm v1.0.4

Establish and maintain access from a server to local services on remote client machines. Paradigm is a portable shell script that creates remote port forwarding tunnels over ssh, using ports allocated by a helper script on the server. Requires only a POSIX-compatible shell and openssh. This framework provides a reliable way for a fleet of headless firewalled devices to remain accesible despite changing networks or geographic location.

The primary use case for this framework is to forward the listening port of the music player daemon (http://musicpd.org) to a server that may then provide playback control. This eliminates the otherwise essential configuration of the local network to provide client-server connectivity. 

The result is an embedded device that can be controlled from a phone, even if the phone wanders from Wifi to mobile data. It may be sent by post and installed with zero configuration by a layman.

Paradigm also facilitates the multiplication of control to many handsets with server-side caching, and augmenting of data from 3rd party sources (album art, artist bios etc.) while limiting and regulating load on the embedded device itself. Ideal for use in the commercial environments where high temperature operating conditions are common, and cpu usage must be kept minimal to prevent overheating.

<h3>Usage:</h3>
&nbsp;&nbsp;paradigm [CONFIGDIR]<br>
&nbsp;&nbsp;CONFIGDIR defaults to $HOME/.paradigm<br>
&nbsp;&nbsp;Reads one service per file in the format below.<br>
&nbsp;&nbsp;Run every minute to check all services and/or re-establish tunnels.

<h3>Example Config:</h3>
&nbsp;&nbsp;CONTROLUSER="convict"<br>
&nbsp;&nbsp;SERVERPORT=22<br>
&nbsp;&nbsp;LOGFILE="./log.paradigm"<br>
&nbsp;&nbsp;PLAYNODE="Dummy"<br>
&nbsp;&nbsp;SERVICENAME="MPD"<br>
&nbsp;&nbsp;SERVICEPORT=6600<br>
&nbsp;&nbsp;CONTROLSERVER="control"

<h3>Installation:</h3>
&nbsp;&nbsp;chmod a+x ./paradigm; cp paradigm /usr/bin<br>
Set to run once per minute in user crontab:<br>
&nbsp;&nbsp;*/1 * * * *  /usr/bin/paradigm  >> /dev/null 2>&1<br>
The "checker" script should go in the server user's home directory.

<h3>Definitions</h3>
<ul>
  <li>ServiceName - Arbitrary name for a local service running on the client, e.g. Shell (for sshd).</li>
  <li>ServicePort - Local port that ServiceName is listening on.</li>
  <li>Playnode - Arbitrary name referring to the client machine.</li>
  <li>Portalias - Server-assigned port mapped to the reverse tunnel. This value is retrieved from a server-side flat file before each tunnel is established.</li>
</ul>

<h3>The Flat File</h3>
Paradigm uses a server-side flat file to store portaliases to simplify connecting and provide access to multiple services on multiple nodes. Each node is able to forward as many ports as required.

File location: jailed user's home directory.<br/>
File name syntax: portalias-SERVICENAME (i.e. ~/portalias-Shell)

Syntax Example
<pre>
  Ricky^1025
  Julian^1026
  Bubbles^1027
</pre>

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

<h3>About</h3>
Access services on remote, portable, or mobile client machines as though they are on your server's localhost. Ports for local services like sshd are forwarded through encrypted tunnels to a server. Tunnels are checked and re-established when broken. Everything is logged. The paradigm script is expected to run on a headless, innaccesible unit, on an unknown network, and is designed to operate without supervision. 

In practice this is useful to manage a fleet of headless embedded media players such as digital signage in retail locations with poor quality computer networks. Remote accessibility to all forwarded service ports persists with minimal network requirements: access to one server over one port.
