# paradigm

A shell script to establish and maintain forwarding of local ports over reverse ssh tunnels to a server for remote access to local services. Reverse port forwarding over ssh is useful for maintaining access to headless units in unpredictable network conditions.

<h2>Definitions</h2>
<ul>
  <li>ServiceName - Arbitrary name for a local service running on the client, e.g. Shell (for sshd). This name is used to identify the server side flat file database.</li>
  <li>ServicePort - Local port that ServiceName is listening on, e.g. 22</li>
  <li>Playnode - Name arbitrarily assigned to group services together. Usually the hostname of the client machine.</li>
  <li>Portalias - Server-assigned port mapped to the reverse tunnel. This value is retrieved from the server-side flat file before each tunnel is established.</li>
</ul>

Uses a server-side flat file to store portaliases to simplify connecting and provide access to multiple services on multiple nodes. Each node is able to forward as many ports as required.

In practice this is useful to manage a fleet of headless embedded media players such as digital signage in retail locations with poor quality computer networks. Remote accessibility to all forwarded service ports persists with minimal network requirements: access to one server over one port.

You can unplug and move a playnode from home to work and it will still be manageable through the website.

This is a proprietary script that is being converted to a general purpose utility.
