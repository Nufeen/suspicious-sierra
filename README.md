# SUSPICIOUS SIERRA CONNECTIONS

This is a tiny investigation of suspisious Mac OS Sierra connections reported by Little Snitch (commonly abbreviated as `LS` in current context), each section consists of quotes from the discussions in corresponding links. My own comments are marked with _italic_.

This document covers only recent network activity, for the detailed Mac OS security guide refer https://github.com/drduh/macOS-Security-and-Privacy-Guide

This gist with shell script for just disabling everything is related, though I'd recommend to be very careful with it: https://gist.github.com/pwnsdx/d87b034c4c0210b988040ad2f85a68d3 

### Few things to know:

To get the list of agents you can run: `ls /System/Library/LaunchAgents/`

Utility to operate them is called `launchctl`. To get info about certain service: `launchctl list com.apple.whatever`

Latest Mac OS versions include the System Integrity Protection (SIP) feature, that is controlled by `csrutil` util. 

By default it restricts unloading system agents, so most `launchctl unload` commands in old (pre-Maverics) privacy guides will just fail with the following error: `Operation not permitted while System Integrity Protection is engaged`

Stopping certain deamon in current session is not restricted though:

`sudo launchctl stop com.apple.whatever`

You can disable SIP on your own risk, details here: https://developer.apple.com/library/content/documentation/Security/Conceptual/System_Integrity_Protection_Guide/ConfiguringSystemIntegrityProtection/ConfiguringSystemIntegrityProtection.html


## `akd/gsa`

`akd` is part of the AuthKit (authentication/authorisation) framework. Also used in the process of authenticating of iCloud and other accounts using Apple ID.

It is used to authenticate the App Store. App Store login fails if you block it.

https://apple.stackexchange.com/questions/240108/what-is-the-akd-process-and-do-i-need-to-allow-it-to-connect-to-the-internet/247110

_Looks like it's the one you'll really need to keep in order to get updates automatically._


## `apsd`

That is the daemon which arbitrates push notifications and other cloud services.

https://www.reddit.com/r/mac/comments/2uris4/disable_apsd_to_speed_up_your_mac/

_Note: `aspd` rule is protected by Little Snitch (they consider it important enough for correct system functionality)._


## `captiveagent`

A captive portal is a network that forces an HTTP client to see a special web page (usually for authentication purposes) before using the Internet normally. A captive portal turns a Web browser into an authentication device. These are commonly used on wifi networks where authentication to the private network is done via a login browser page, rather than via the use of a WEP or WPA2 key, for example in some coffee shops and airports.

On Apple devices, if a captive portal is identified, a special application in `/System/Library/CoreServices` called `Captive Network Assistant.app` is opened. This is a very limited browser, separate to Safari, with no address bar or navigation buttons.

https://grpugh.wordpress.com/2014/10/29/an-undocumented-change-to-captive-network-assistant-settings-in-os-x-10-10-yosemite/

https://apple.stackexchange.com/questions/45418/how-to-automatically-login-to-captive-portals-on-os-x

_Pretty nice example of how things are messed up nowadays. I disabled it on a wi-fi connected computer and it seemes to be fine, but looks that's the thing to be accurate with, since wrong settings may cause problems with connecting to your own wi-fi networks._


## `ckkeyrolld`

_Looks like for now nobody is sure what it is:_

`ckkeyrolld` is the system daemon that rolls and verifies encryption metadata.

No clue about what these metadata really are and why they need to be shared with Apple.

`ckkeyrolld.plist` is related to iCloud encryption I believe.

https://www.reddit.com/r/MacOS/comments/7au0tv/what_is_ckkeyrolld/

https://twitter.com/icleus/status/943087366436282370

_Soundes like a creepy crap to be disabled. At least if you are not using iCloud (I do not)._


## `com.apple.Safari.SafeBrowsing.Service`

This database, provided by Google, is used by mobile Safari to check for known malicious web sites.

Google is constantly updating this database, so your iDevice refreshes on every sync.

https://apple.stackexchange.com/questions/101556/what-is-safari-safe-browsing-data-and-why-does-it-need-to-copy-every-time-i-sync

_Suspicious is that OSX sometimes calls the service while Safari is off. But overall this is a useful one._


## `gamed`

Obviously this framework is something to do with Apple's Game Centre, which I've only previously come across on iOS. What is it now doing on OSX, given that there seems to be no GUI 'front-end' for it? And how the **** do I disable it?

https://discussions.apple.com/message/23567732

https://apple.stackexchange.com/questions/223331/how-to-disable-game-center-process-gamed-on-os-x-10-11-el-capitan

_Definitely to be blocked or turned off._

### possible way to disable: 

In system settings, you have the panel with the notification center. I removed the game center from there. Hope it helps!

## `geod.xpc`

It looks like this service is entirely related to Location Services. Blocking it with Little Snitch (as I have done) and/or disabling it with the launchctl command, will render Location Services inoperable. About the only down side I see to that (for my own usage scenario) is loss of the Find My Mac functionality. Otherwise, I've got no particular reason for apps and system services to be utilising my location. People who travel (across timezones) may find that automatic updating of their timezone and clock will fail.

https://discussions.apple.com/thread/7353115

`Locationd` is a daemon that provides location services for OS X's "Core Location". This uses skyhook technology to figure out your mac's location (using WiFi). It's the same system used by the iPod Touch, and is also used by the iPhone (The iPhone also uses cell tower triangulation and GPS).

https://discussions.apple.com/thread/2141988

Even if you were to process all the geo location aspects on developer.apple.com - it's widely used - far greater than just the Core Location API - https://developer.apple.com/documentation/corelocation.

* Weather
* Calendar
* Address Book
* Maps
* Siri
* iCloud (finding the best data center to route uploads)
* Store (which geographic region should your content be)
* Time Zone and Night Shift
* Safari location

https://apple.stackexchange.com/questions/201839/services-using-com-apple-geod

### possible way to disable: 

_Location Services can be turned off in System Settings (and is actually recommended for the desktops, since you'll hardly ever need them). In my case that didn's stop geod.xpc activity though._


## `helpd`

`helpd` is the daemon that's running on your mac that gives you access to the help files when you open a program.

https://discussions.apple.com/thread/3930621

_I turn this thing off since it tries to reach remote servers when I do not ask for any kind of help._


## `imagent`

`imagent` is part of Apple's Messages app (formerly iChat). (_LS info_)

`IMAgent` is a process that listens for FaceTime invitations. It will be active even if FaceTime isn't running. You don't have to block it, but if you don't use FaceTime you can open the application, open preferences, and turn off FaceTime. That should quit the IMAgent process.

https://apple.stackexchange.com/questions/86814/firewall-settings-with-imagent

`imagent` memory leak in Mountain Lion: https://discussions.apple.com/thread/4209012?tstart=0

_Tries to reach `init-p01md.apple.com` with no reason, when iChat/FaceTime/etc are off._


## `ksfetch`

_Not an Apple service, but you'll probably encounter it. Useful one. Frequency of update checks is adjustable, see the link below._ 

The `ksfetch` process on OS X is part of Google Chrome's update mechanism. The ks prefix is an abbreviation of Keystone. The process appears to be responsible for fetching updates to Google's products.

https://apple.stackexchange.com/questions/207998/what-is-ksfetch-process-on-mac


## `mappushd`

Location Services allows applications and websites to gather and use information based on the current location of your computer.

Your approximate location is determined using information from local Wi-Fi networks, and is collected by Location Services in a manner that doesn’t personally identify you.

https://support.apple.com/en-us/HT204690

https://support.apple.com/en-gb/HT207092

_The part of Location services. Related to weather, timezones and so on. Can be disabled on desktops. The only real use of them is Find My Mac functionality on macbooks._

## `nbagent`

Noticeboard agent is a macOS system process. As part of Apple’s software update mechanism it is connecting to Apple’s servers. (_LS version_)

`nbagent` is used for website notifications that show up in notification center

https://discussions.apple.com/thread/6445084

_Still not sure what it is. What is noticeboard? Why does it need a special agent to connect apple servers? Just disabled it._

## `parsecd`

`parsecd` itself is an unknown network service (possibly related to security) which is run every 10 minutes approximately.

https://eclecticlight.co/2017/05/31/an-incomplete-list-of-sierras-dispatched-services/

Is this the service that sends all of my pasteboard content to Apple?

https://www.reddit.com/r/mac/comments/54870l/what_is_comappleparsecd/

`parsecd` is a macOS system process that is used for suggestions in Spotlight, Messages, Lookup and Safari (_Little Snitch info_)

But what the heck does `parsecd` actually do? 

It is location-based suggestions for Siri

I now see a Siri entry that’s checked but greyed out. I suspected that was because she was disabled so I reenabled her. But her location service is still checked and grayed out - no way to disable it!

Guess I’ll just have to block connections with LS to silence the cunning little witch.

https://apple.stackexchange.com/questions/266989/what-is-parsecd

## `touristd`

It can display different Apple tours depending on OS and device. You can try to run `/System/Library/PrivateFrameworks/Tourist.framework/Versions/A/Resources/touristd --help` to get some hint.

https://apple.stackexchange.com/questions/294291/what-is-touristd

Details: 

https://carlashley.com/2016/10/19/com-apple-touristd/
