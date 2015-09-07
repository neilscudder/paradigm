# paradigm v1.0.3

Access services on remote, portable, or mobile client machines as though they are on your server's localhost. Ports for local services like sshd or mpd are forwarded through encrypted tunnels to a server. Tunnels are checked and re-established when broken. Everything is logged. The paradigm script is expected to run on a headless, innaccesible unit, on an unknown network, and is designed to operate without supervision. Run paradigm every minute with system cron.

Usage:<br>
&nbsp;&nbsp;paradigm [CONFIGDIR]<br>
&nbsp;&nbsp;CONFIGDIR defaults to $HOME/.paradigm<br>
&nbsp;&nbsp;Reads one service per file in the format below.<br>
&nbsp;&nbsp;Run every minute to check all services and/or re-establish tunnels.

Example config file ($HOME/.paradigm/Dummy-MPD.conf): <br>
&nbsp;&nbsp;CONTROLUSER="convict"<br>
&nbsp;&nbsp;SERVERPORT=42<br>
&nbsp;&nbsp;LOGFILE="./log.paradigm"<br>
&nbsp;&nbsp;PLAYNODE="Dummy"<br>
&nbsp;&nbsp;SERVICENAME="MPD"<br>
&nbsp;&nbsp;SERVICEPORT=6600<br>
&nbsp;&nbsp;CONTROLSERVER="control"

Installation:<br>
&nbsp;&nbsp;chmod a+x ./paradigm; cp paradigm /usr/bin<br>
Set to run once per minute in user crontab:<br>
&nbsp;&nbsp;*/1 * * * *  /usr/bin/paradigm  >> /dev/null 2>&1<br>

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
Paradigm checks for the existence of, and otherwise initiates a ssh tunnel to reverse-forward local ports to a remote server.

In practice this is useful to manage a fleet of headless embedded media players such as digital signage in retail locations with poor quality computer networks. Remote accessibility to all forwarded service ports persists with minimal network requirements: access to one server over one port.
