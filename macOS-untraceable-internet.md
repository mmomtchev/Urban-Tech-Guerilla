# Creating a completely untraceable Internet connection on macOS

**A guide for network and OS-savvy people on the run from a first-world state-sponsored extortion scheme.**

This tutorial will show you how to create a completely untraceable Internet connection using macOS and publicly-available Wi-Fi networks. As a law-abiding citizen fighting criminal corruption, I won't cover `aircrack-ng`. There are plenty of publicly-available Wi-Fi networks in most major metropolitan areas and you can stay on the good side of the law without compromising your own safety.

First of all, should you be tempted to simply connect to public Wi-Fi APs and then to use Tor to access the Internet, you should know that any modern computer will immediately betray you. It will make tons of connections to services such as iCloud (on macOS), Google (if you use Chrome), Spotify (if you use it), VSCode configuration syncing, Whatsapp, autoupdaters, whatever... All those services cannot be easily made to use Tor and will give up your IP address the very moment you connect.

The goal of this setup is to preserve perfectly the secrecy of the entry Wi-Fi AP in such extreme cases as mine where my telecom operator and Big Tech - including Google, Microsoft and Twitter - are actively collaborating with the extortion scheme.

The setup uses a Linux guest running Tor exposed to the host macOS via a SOCKS5 proxy through which OpenVPN connects. This allows for a perfectly normal - albeit somewhat slower - Internet experience where everything works. Remote servers see a connection coming from the public OpenVPN server. People tracing the OpenVPN server see only a connection coming from a Tor exit node. People managing the Wi-Fi AP see simply a plain Linux computer connecting to the Internet and using Tor - if you go for the fast mode - or simply browsing the Internet - if you go for the slower bridged mode.

Its main advantage is that you do not need to pay attention - the setup makes it impossible for any traffic from the macOS host to leave the computer unless it is routed through Tor. Traffic from the Linux guest is routed normally through the Wi-Fi AP. Do not use the Linux guest for anything besides connecting to the Wi-Fi AP.

Another great thing about this setup is that you are not limited to the Tor-friendly websites. Most of you, who have never used Tor for your daily browsing, do not know that many official websites block Tor users - or impose additional security restrictions. This setup does not have this problem.

## Prerequisites

- a macOS computer
- an external USB Wi-Fi adapter - high powered antennas work best but are not mandatory
- a publicly hosted server that will serve as the public origin of your connection if you need one (some services such as `ssh` require it) - you can use your own or you can use any public OpenVPN-compatible service - you do not need to trust your VPN provider - they do not see the origin of your connection
- VMWare Fusion with a Linux guest

## The Linux router

Install the Linux router guest using host-only networking and pass the external USB Wi-Fi adapter directly to the guest. I won't cover the installation of the Linux guest, you certainly can do this yourself. Make sure you see both the host-only network adapter which will appear as an Ethernet device and the external USB Wi-Fi adapter. Do not use any personally identifiable information in this guest. Connect to the AP normally. You can install a GUI - or you can do as me and use XQuarts on the macOS host and launch a browser remotely on a text-only headless Linux guest. You will need the browser because most public APs have captive portals that require JavaScript. Make sure the host-only connection works. Check the IP address on the Linux guest and use `ssh` from the macOS host.

## Tor

You can install `tor` directly from the Linux repositories - since you are not running a relay, you don't need the latest version.

Edit `/etc/tor/torrc` and enable (these lines will be there but commented out):

```ini
SocksPort 9050
SocksPort 192.168.108.128:9150
SocksPolicy accept 192.168.0.0/16
SocksPolicy accept 127.0.0.0/8
SocksPolicy reject *
```

The IP address should be the IP address of the Linux guest on the host-only virtual network.

`tor` should start - you can dump the WLAN interface and check `/var/log/*` to make sure everything works.

At this point you can try from the Linux guest:

```shell
curl --socks5 127.0.0.1:9050 https://duckduckgo.com
```

Once this works, go back to the macOS host and try the same:

```shell
curl --socks5 192.168.108.128:9150 https://duckduckgo.com
```

Note that you still haven't set up DNS, so `curl` will use the system DNS on the macOS but the connection will be routed through Tor.

## OpenVPN

I am using certificates, but you can use shared secrets, it is less hassle unless you already have your own CA.

Note that Tor cannot route `udp` packets, so you will have to use the less performant `tcp` mode.

If you are using a public OpenVPN service, refer to its documentation on how to set up a connection.

If you want to run your own OpenVPN service, install OpenVPN on the remote server from the Linux repositories.

Create `/etc/openvpn/tor.conf`:

```ini
port 48723 # you can use any port you wish
proto tcp
dev tun

ca      /etc/ssl/YourCA.crt
cert    /etc/ssl/YourCert.crt
key     /etc/ssl/private/YourKey.pem 
# you can generate this file with openssl
dh      /etc/ssl/dhparams.pem

topology subnet

# use any free network
server 192.168.64.0 255.255.255.0
ifconfig-pool-persist server/ipp.txt

push "route 192.168.64.0 255.255.255.0"
push "redirect-gateway def1 local"
# If you want to push the DNS servers of your remote provider
# (or you can use your own)
push "dhcp-option DNS x.x.x.x"
push "dhcp-option DNS x.x.x.x"

keepalive 10 120

# you can generate this file with openssl
tls-auth /etc/openvpn/server/ta.key 0
auth-nocache

cipher AES-256-CBC
data-ciphers AES-256-CBC

persist-key
persist-tun

status /var/log/openvpn/openvpn-status.log

verb 3  # verbose mode

client-to-client
explicit-exit-notify 1
```

Then run `systemctl restart openvpn@tor`.

Now, install Tunnelblick on the macOS host and set up a tunnel:

```ini
client
dev tun
proto tcp

remote <your VPN server> 48723
socks-proxy 192.168.108.128 9150
resolv-retry infinite
nobind

persist-key
persist-tun

ca      YourCA.crt
cert    YourCert.crt
key     YourKey.pem

remote-cert-tls server
# you can generate this file with openssl
tls-auth ta.key 1
auth-nocache

cipher AES-256-CBC
data-ciphers AES-256-CBC

mute-replay-warnings

verb 3
```

At this point you should be able to connect with Tunnelblick via Tor. You should even be able to access the Internet if you manually set up the routing, but the problem is that the macOS GUI will be confused and it will think that the computer is offline.

## Creating a new macOS *Network Service*

VMware Fusion creates a bridge for the host-only network, but it is not identified in the macOS GUI as a valid network adapter.

We will have to create a new network adapter to make the macOS GUI work with it.

Identify the VMWare Fusion device by using `ifconfig` (look at the IP address) - it will usually be `bridge100` (or maybe `bridge101` if you have another virtual machine).

Start by creating a macOS *hardware port* tied to `bridge100`:

```shell
sudo plutil -insert VirtualNetworkInterfaces.Bridge.bridge100 -dictionary -append /Library/Preferences/SystemConfiguration/preferences.plist
sudo plutil -insert VirtualNetworkInterfaces.Bridge.bridge100.Interfaces -array /Library/Preferences/SystemConfiguration/preferences.plist
sudo plutil -insert VirtualNetworkInterfaces.Bridge.bridge100.UserDefinedName -string "VMWareFusion100" /Library/Preferences/SystemConfiguration/preferences.plist
```

Now you should have a new *hardware port* in `networksetup`:

```shell
sudo networksetup -listallhardwareports
```

Then, create a new macOS *network service* tied to this *hardware port* with the existing IP addresses. The first IP address is the IP address of the macOS host on this virtual network and the second one is the IP address of the Linux guest which will be the router.

```shell
sudo networksetup -createnetworkservice Tor VMWareFusion100
sudo networksetup -setmanual Tor 192.168.108.1 255.255.255.0 192.168.108.128
```

Now if you go to `System Settings...` / `Network` you will notice a new network adapter called `Tor` along any eventual Wi-Fi and Ethernet adapters. This will be your untraceable connection. It will be immediately green and active. You can enable and disable it from the GUI. Do not worry, you won't leak anything at this point - the Linux router is not set up for masquerading.

If you want to access your OpenVPN server by DNS name, you will also have to install a recursive DNS server on the Linux guest and use:

```shell
sudo networksetup -setdnsservers Tor 192.168.108.128
```

You don't need this step if the IP address of your VPN server is static and you do not care if those tracing your OpenVPN server know what sites you connect to.

If your OpenVPN server has a dynamic IP - or you want to hide this information - what other sites you visit, besides those that are already tracking you, you should use a 3rd party public DNS service.

After your network adapter is created, you should delete the *hardware port* - this device is managed by VMWare Fusion and you do not want the macOS system to create it at the next reboot. You do not need it - the *network service* references `bridge100` directly.

```shell
sudo plutil -remove VirtualNetworkInterfaces.Bridge.bridge100 /Library/Preferences/SystemConfiguration/preferences.plist
```

## Browsing

This setup is mainly geared towards hiding the entry AP - and not the destination of your traffic.

It can however do both at the same time.

You should set up two browsers - one that connects directly to the Internet - this one will always appear as a connection coming from the remote VPN server and it will be capable of accessing all websites. With this browser however, people tracing the VPN server will be able to know what sites you visited and search engines will be able to provide your search history - even if you don't use an account.

Another browser should be set up to use the SOCKS5 proxy. Connections from this browser will appear as coming from a random Tor exit node and these will be untraceable. However it will be limited to the Tor-friendly websites.

## Playing mobile games on your phone while preserving the secrecy of the AP

At this point it will be possible to use the internal macOS Wi-Fi adapter in connection sharing mode to connect your mobile phone - provided that you remove the SIM card.

## Uninstalling

The only thing that needs uninstalling if you want to remove the setup is to simply remove the *network service* - which can be cleanly removed from the `System Settings ...`/`Network` menu. If you also delete `/Library/Preferences/SystemConfiguration/preferences.plist.old` - the backup copy - no other traces will remain on your computer once you have removed the VMWare Fusion virtual machine.
