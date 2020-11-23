1.  Create an extension to use with Jigasi
in asterisk 18.0 we will be using pjsip.conf
you will ahve to add it as a user than can be registered via zoiper
NOTE 8001 fails for me . 

2. Jitsi Server – Install Jigasi

• From terminal:

sudo apt-get install jigasi -y
• When prompted, enter your SIP username. This will be the 
Extension@asteriskIPAddress

• Password will be the ‘secret’ copied from when you created the extension in step 1.

3. Add some SIP related configuration settings to Jigasi

• From terminal:

sudo nano /etc/jitsi/jigasi/sip-communicator.properties
• Add the following lines under the SERVER_ADDRESS line:
Be sure to change domain and ports to match your configuration

net.java.sip.communicator.impl.protocol.sip.acc1403273890647.SERVER_PORT=5060
net.java.sip.communicator.impl.protocol.sip.acc1403273890647.PROXY_ADDRESS=asteriskIPAddress
net.java.sip.communicator.impl.protocol.sip.acc1403273890647.PROXY_PORT=5060
net.java.sip.communicator.impl.protocol.sip.acc1403273890647.PROXY_ADDRESS_VALIDATED=true
net.java.sip.communicator.impl.protocol.sip.acc1403273890647.PROXY_AUTO_CONFIG=false
net.java.sip.communicator.impl.protocol.sip.acc1403273890647.PREFERRED_TRANSPORT=udp
• Add or un-comment the following line:

net.java.sip.communicator.service.gui.ALWAYS_TRUST_MODE_ENABLED=true
4. Add credentials for Jigasi

If you’re using authentication, Jigasi will need it’s own credentials.

• I’m using prosody. prosodyctl register [username] [Jitsi Domain] [Password]

sudo prosodyctl register jigasiadmin meet.domain.com mysuperstrongpassword
• Add the new credentials to /etc/jitsi/jigasi/sip-communicator.properties
• From terminal:

• Add the following lines, replacing with the credentials you just created:
Note: password is clear text

org.jitsi.jigasi.xmpp.acc.USER_ID=jigasiadmin@meet.domain.com
org.jitsi.jigasi.xmpp.acc.PASS=mysuperstrongpassword
org.jitsi.jigasi.xmpp.acc.ANONYMOUS_AUTH=false

ALSO HERE ENSURE THE FOLLOWING - JITSI DOC IS PRETTY BAD SO HERE WHAT WE HAVE TO DO
UPDATE JIGASI CONFIG FILE WITH /etc/jitsi/jigasi/config as follows
 - JIGASI_SECRET= (value of /etc/prosody/conf.d/XXXX.cfg.lua:
Component “callcontrol.YOURHOSTNAME” component_secret = “topsecret”)

• Restart Jigasi with the following terminal command

sudo systemctl restart jigasi

5. Pause here and do some testing

At this point, Jigasi’s SIP extension should be registered, let’s validate that by heading over to FreePBX…

• FreePBX > Admin > Asterisk CLI
• Run CLI command: SIP show Peers
• The extension should show “OK” if registered properly

Dialing the extension from another SIP endpoint (desk phone or softphone) should route you to the default Jitsi Room “siptest”

• Pull up a jitsi meeting via web browser , use the name: siptest
• from another registered endpoint, dial your jitsi extension (my example is 888)
• If this doesn’t work , check logs
Jigasi log - (sudo nano /var/log/jitsi/jigasi.log)
FreePBX – Reports > Asterisk logfiles

Only Proceed to step 6 if you can successfully dial into the siptest room
