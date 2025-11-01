# Captive Portals and Wi-Fi Security

After almost 2 months of full-time war-driving I have become an expert in captive portals.

Almost every public Wi-Fi has a captive portal.

## Operation

A captive portal is a very specific mechanism that abuses the network protocol to achieve something that was not meant to be possible.

How does a captive portal work?

A captive portal is operates at the network protocol level. When a new previously unknown device connects to the Wi-Fi AP, the captive portal initially blocks its outgoing traffic, operating as a firewall. Some will silently discard all outgoing packets. Others will intercept the connections and return a `TCP` `RST` packet. Still others will return an `ICMP` port unreachable message, resulting a `Connection refused` error on the client. This applies to all traffic, including DNS traffic.

When a connection with a web browser to a remote host on ports 80 or 443 is detected, the captive portal will intercept it and return a short response - operating in this case as a transparent proxy. This short response will usually be an `HTTP` 404 redirect to the welcome page of the captive portal. In order to make the browser connect, the captive portal will provide some form of fake DNS service - otherwise the browser won't even attempt the connection. This is one of the most fragile elements of captive portals and it will often lead to connection failures, depending on the client software.

Another weakness of the captive portals is the `HTTPS` protocol - which has security features that prevent man-in-the-middle interception of the traffic. In these cases, older or broken captive portals can fail to establish a connection. Newer ones usually respond with a fake certificate, provoking a warning about impersonation in the client browser and proposing the user to accept the potentially insecure webpage which will appear with a read broken padlock in the URL bar.

This has become a very serious problem since recent browsers switched to an `HTTPS` first policy. If the first site you try connecting to is an `HTTPS` site, the connection might not work.

These days some browsers will establish an invisible connection to some remote `HTTP` site on launch. Google Chrome for example will always connect to `http://connectivitycheck.gstatic.com`. This allows to immediately trigger the captive portal.

Once the user has authenticated, the captive portal will lift the firewall restrictions and further connections will proceed normally. Very often, this will also lead to different types of DHCP leases - since the captive portal also operates the DHCP server. It will also remember your MAC address - which will be your DHCP lease. If you briefly disconnect and reconnect, it will assign you the same IP address.

The captive portal may also store a cookie in the client web browser that will allow the user to skip the webpage the next time it connects. In this case, the captive portal will simply flash briefly and immediately connect.

## Uses

Most public Wi-Fi APs will simply display a welcome message containing the *Terms of Service* policy and usually some kind of advertisement related to the business operating the captive portal. Some - especially the corporate ones - will ask you to provide some identity details. Some will even asking for registration and give you a username and a password. I have tried on numerous occasions to identify myself as the Wizard of Oz or something and to say that I was here for the free Internet. I have always received a password.

In very rare cases, some corporate APs will play it as if they were very serious, asking for a *sponsor email* - the person in the company who invited you here.

Which brings us to our next subject.

## Captive Portal Security

**A captive portal does not provide security and should not be used for security.** The Wi-Fi encryption is the security mechanism. If you want security, you should use WPA2/WPA3 with a strong (long) password. This is a mechanism that has been designed from the ground up to provide security. If you want to protect your Wi-Fi with a password, use it.

The captive portal, on the other hand, is a horrible network stack hack. It is good for advertisement, as no one will spend 5 minutes to skip an advertisement that takes 3 seconds to click through, but it shall never be used to restrict access to the Wi-Fi.

First of all, I have seen far too many - to not say absolutely every single one - captive portals that use `HTTP` instead of `HTTPS`. Needless to say, over an open unencrypted Wi-Fi network, these kind of passwords are not very secure.

Second, given that the captive portal will remember your MAC address after your first connection, if you do not have a password, there is nothing stopping you from taking someone else's MAC address - as these are transmitted over the air. The captive portal knows the devices only by their MAC addresses which are not a security feature.
