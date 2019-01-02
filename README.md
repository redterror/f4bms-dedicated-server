# f4bms-dedicated-server
Notes and config files for running a dedicated Falcon4 BMS server.
This repo aims to capture the setup notes, config files, and supporting references for running a dedicated Falcon4 BMS server either for public or private use.

This document is my attempt to make this process easier and more repeatable in the future, for myself and others.

# Thanks / Credits

Thanks to the BMS devs and community members who helped along the way.
There is a nontrivial amount of arcana required to rig up this whirlygig.
This document was largely the output from:

* https://www.benchmarksims.org/forum/showthread.php?24564-REL-dedicated-server-for-Windows-with-no-GPU/
* https://www.benchmarksims.org/forum/showthread.php?14263-Dedicated-Server-Setup&p=199625&viewfull=1#post199625
* https://www.benchmarksims.org/forum/showthread.php?14032-Server-reverts-to-2d-map-on-it-s-own&p=195895&viewfull=1#post195895

Extra thanks to CobaltUK on discord for sharing some insights about the VeteranGaming BMS dedicated server.

# Server Setup

Broadly, running a dedicated server requires:

* A dedicated machine or VM running windows (WINE also possible on Linux).  The machine can be local to you or hosted in a datacenter.
* Sufficient bandwidth to host the target player count (players will likely use 1024Kbps or 2048Kbps bandwidth)
* A licensed install of Falcon4.
* Patience

Tested against BMS 4.33.3.

## AWS Setup
* Setup a VPC with basic pieces:
  * Create an internet gateway, attach it to your VPC.
  * Add a route to 0.0.0.0/0 on your default routing table to use the IGW.
  * Create a subnet for your server.
  * Create a security group or edit the default SG with the following ports open:
    * ICMP
    * TCP/UDP 2934-2937 (BMS game ports)
    * UDP 9987-9989 (IVC)
    * Allow RDP from your IP
* create a new ec2 windows instance.
  * A t2.micro is fine for most of the setup, but a c4.large will accelerate things.
  * The default 30G disk size is fine for a base install, but if you want to add more theaters I recommend going with 50 or 75GB.
* get admin password via the AWS console
* Connect via RDP
  * Ensure your RDP connection has the sound setting set to 'play on remote machine'
  * Ensure 32-bit color depth
  * Set your resolution as desired (I use 1440x900, this gives enough room for the BMS window at 1024x768 + the taskmgr alongside)
* BMS requires a sound card, so install Virtual Audio Cable: http://software.muzychenko.net/eng/vac.htm
  * In the services control panel, set audio service to start at boot.  
  * Also start it manually.
* reboot & RDP back in.  Ensure you see the sound bullhorn.

## BMS Setup
* install chrome, falcon4 (via steam or gog, etc), 7zip, and deluge (or another torrent client)
* fetch the BMS installer + incrementals
* enable ping: http://www.sysprobs.com/how-to-enable-ping-on-windows-server-2012-r2-firewall
* alternatively, change inbound policy to accept-all (do this for domain/private/public zones) (AWS security groups are in front of us, so why double up)

* download / install any desired theaters as normal
* fetch the server dlls: https://www.benchmarksims.org/forum/showthread.php?24564-REL-dedicated-server-for-Windows-with-no-GPU
  * Copy all files to bin/x64
* copy the [falcon_bms.cfg](falcon%20bms.cfg) file to User\Config
* copy the [atc.ini](atc.ini) file to your target theater's Campaign folder
  * KTO: `Data\Campaign\Save`
  * Other: `Data\<Campaign Name>\Campaign`
* Verify that all graphics settings are dialed down in F4Patch
* Launch BMS and ensure all graphics settings are set to the minimum.

## Running BMS + Entering 3D on the server
* Launch the IVC server
  * Make a shortcut to the IVC server on your desktop or use the regular launcher
* run the bat script to launch the game (again a shortcut on the desktop is fine)
* Under 'Comms', adjust the 'Host a game' phone setting with 85-90% of your uplink bandwidth
* Choose a new campaign or use a save, frag a training flight and select a seat.
* Do a ramp start when entering 3D, so the plane entered is powered down.
* Hit '~' once "in pit" to switch to satellite view (FPS boost should be visible)
* Hit alt+insert (RDP) or alt-tab (local) and minimize the window
* Minimize RDP.  Your server is ready to host a campaign.

### AWS Performance Notes:
* t2.micro is fine for setup
* c4.large gets 80+ fps in 2D, ~15fps in 2D with comms connected, and 50-60 fps in 3D satellite

# TODO
* investigate auto-running w/ above-normal CPU priority
