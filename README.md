# SUSPICIOUS SIERRA CONNECTIONS

This is a tiny investigation of suspisious Mac OS Sierra connections reported by Little Snitch, each section consists of quotes from the discussions in corresponding links. My own comments are marked with _italic_.

This document covers only recent network activity, for the detailed Mac OS security guide refer https://github.com/drduh/macOS-Security-and-Privacy-Guide

This gist with shell script for just disabling everything is related, though I'd recommend to be very careful with it: https://gist.github.com/pwnsdx/d87b034c4c0210b988040ad2f85a68d3 


## `akd/gsa`

`akd` is part of the AuthKit (authentication/authorisation) framework. Also used in the process of authenticating of iCloud and other accounts using Apple ID.

It is used to authenticate the App Store. App Store login fails if you block it.

https://apple.stackexchange.com/questions/240108/what-is-the-akd-process-and-do-i-need-to-allow-it-to-connect-to-the-internet/247110

_Lookes like it's the one you'll really need to keep in order to get updates automatically._


## `apsd`

That is the daemon which arbitrates push notifications and other cloud services.

https://www.reddit.com/r/mac/comments/2uris4/disable_apsd_to_speed_up_your_mac/

_Note: `aspd` rule is protected by Little Snitch (they consider it important enough for correct system functionality)._


## `captiveagent`

A captive portal is a network that forces an HTTP client to see a special web page (usually for authentication purposes) before using the Internet normally. A captive portal turns a Web browser into an authentication device. These are commonly used on wifi networks where authentication to the private network is done via a login browser page, rather than via the use of a WEP or WPA2 key, for example in some coffee shops and airports.

On Apple devices, if a captive portal is identified, a special application in `/System/Library/CoreServices` called `Captive Network Assistant.app` is opened. This is a very limited browser, separate to Safari, with no address bar or navigation buttons.

https://grpugh.wordpress.com/2014/10/29/an-undocumented-change-to-captive-network-assistant-settings-in-os-x-10-10-yosemite/

https://apple.stackexchange.com/questions/45418/how-to-automatically-login-to-captive-portals-on-os-x

_Pretty nice example of how the things are messed up nowadays. I disabled it on a wi-fi connected computer and it seemes to be fine, but looks that's the thing to be accurate with, since wrong settings may cause problems with connecting to your own wi-fi networks._


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


## `geod.xpc`

It looks like this service is entirely related to Location Services. Blocking it with Little Snitch (as I have done) and/or disabling it with the launchctl command, will render Location Services inoperable. About the only down side I see to that (for my own usage scenario) is loss of the Find My Mac functionality. Otherwise, I've got no particular reason for apps and system services to be utilising my location. People who travel (across timezones) may find that automatic updating of their timezone and clock will fail.

https://discussions.apple.com/thread/7353115

`Locationd` is a daemon that provides location services for OS X's "Core Location". This uses skyhook technology to figure out your mac's location (using WiFi). It's the same system used by the iPod Touch, and is also used by the iPhone (The iPhone also uses cell tower triangulation and GPS).

https://discussions.apple.com/thread/2141988

_Location Services can be turned off in System Settings (and is actually recommended for the desktops, since you'll hardly ever need them). In my case that didn's stop geod.xpc activity though._


## `helpd`

`helpd` is the daemon that's running on your mac that gives you access to the help files when you open a program.

https://discussions.apple.com/thread/3930621

_I turn this thing off since it tries to reach remote servers when I do not ask for any kind of help._


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
