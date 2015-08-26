# paradigm

Access services on client machines in remote networks as though they are on your server's localhost. The paradigm script is expected to run on a headless, innaccesible unit, on an unknown network, and is designed to operate without supervision. At present, it is run once every minute by a user with server credentials. <strong>The server side component is not here yet. You will have to manually create the flat file.</strong>

Bear with me, I'm 38 and this is my first open-source software project.

<h2>Definitions</h2>
<ul>
  <li>ServiceName - Arbitrary name for a local service running on the client, e.g. Shell (for sshd). This name is used to identify the server side flat file database.</li>
  <li>ServicePort - Local port that ServiceName is listening on, e.g. 22</li>
  <li>Playnode - Name arbitrarily assigned to group services together. Usually the hostname of the client machine.</li>
  <li>Portalias - Server-assigned port mapped to the reverse tunnel. This value is retrieved from the server-side flat file before each tunnel is established.</li>
</ul>

Setup ssh-keys for authentication: (http://www.rebol.com/docs/ssh-auto-login.html)
- cd ~/.ssh
- on the playnode, run ssh-keygen -t rsa -b 4096 -C "jailedUser@control.server.com" subsituting your own server and username
- save the files as control_rsa
- enter no passphrase
- ssh jailedUser@control.server.com 'mkdir .ssh'
- cat control_rsa.pub | ssh jailedUser@control.server.com 'cat >> .ssh/authorized_keys'
- echo "Host control.server.com" >> config
- echo "Hostname control.server.com" >> config
- echo "IdentityFile ~/.ssh/myserver.rsa" >> config

Uses a server-side flat file to store portaliases to simplify connecting and provide access to multiple services on multiple nodes. Each node is able to forward as many ports as required.

Gets a little rambly after this point...

By consolidating the local services of many remote clients on the server, commands may be issued and data retrieved quickly, without having to restablish connection and authenticate each time. This facilitates highly responsive server-side control of local services from a WAN connection, without having to configure local networks. For example, a mobile web app to control music can run on a public web server to control a headless music player on the local network. This is a more simple (for the user), robust and reliable method.

Server-side control of a local service allows a reduction in stress on the client unit, augmentation of data with meta data, and caching for efficient scaling to many users. For example, the music player client will do less work, because the server only makes 10 requests of it per minute, while relaying that data 10 times as often to user's accessing the web site, and augmenting it with album artwork retrieved from Amazon.

But that is a use case. This script checks for the existence of , and otherwise initiates a ssh tunnel to reverse-forward a local port to a remote server. It has verbose logs. It is a work-in-progress.

In practice this is useful to manage a fleet of headless embedded media players such as digital signage in retail locations with poor quality computer networks. Remote accessibility to all forwarded service ports persists with minimal network requirements: access to one server over one port.

You can unplug and move a playnode from home to work and it will still be manageable through the website.

This is a proprietary script that is being converted to a general purpose utility.
