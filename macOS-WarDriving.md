# Wardriving setup

Wardriving is easy. Basic wardriving does not require anything - a simple Android phone with an application called *WiGLE* available on the Google Play Store is absolutely enough.

**In fact, simply walking around while looking at the available Wi-Fi networks on any mobile phone - without any application - could also work, but won't be very practical - especially when driving.**

When I am driving, I use a high performance setup that allows me to log all available networks at typical city limits speeds (30 km/h to 50 km/h). You should know that a single beacon can sometimes travel very far - far beyond the range at which a connection can be established. This is why using a real wardriving application helps a lot - as it logs every single beacon it receives. Such an application will typically find many more networks compared to simply browsing the phone menus. You will also get additional information about the type of the device used which can help you identify high-end professional installations with good signal coverage.

# Tools

## Hardware

My hardware setup consists of:
* A high gain dual 5db Wi-Fi adapter - the BrosTrend AC1200 Linux WiFi Adapter. This is an excellent Wi-Fi adapter that is often used by computer security professionals and it is a very good deal at about 40€ in 2025. It is not the only one - The Kali Linux project usually maintains a list of Wi-Fi adapters suitable for wardriving. Be sure to use the newer `rtw88_8822bu` Realtek driver and not the older one - `rtl8192cu` that is the default on many Linux distributions.

  ```shell
  echo blacklist rtl8192cu >> modprobe.d/blacklist.conf
  ```

  Having a good Wi-Fi adapter will greatly extend your range. Currently the best consumer Wi-Fi antennas are 10db but you will have to buy these separately and attach them - BrosTrend AC1200 allows replacing of the built-in antennas which are mounted on industry-standard SMA connectors. Investing in a weather-resistant rooftop antenna is also an excellent choice - as the chassis of the vehicle will attenuate the signal. With the 5db antennas I am sometimes able to detect Wi-Fi networks from more than 300m away.

* My Macbook Air with a VMWare Fusion box running Linux which owns the USB device - the same setup used for the untraceable Internet

* An Android mobile phone that I use for GPS data

## Software

* The latest `aircrack-ng` distribution from their Github repository with a patch of mine used to make it compatible with the GPS from the Android phone - https://github.com/aircrack-ng/aircrack-ng/pull/2682. Don't use the latest release, it has a horrible bug handling GPS data.
* An open-source Android application called [`gpsdRelay`](https://github.com/project-kaat/gpsdRelay) available on the F-Droid store.
* My own custom software that processes both the *WiGLE* CSV data and the `aircrack-ng` CSV data and builds good looking maps using publicly available mapping services. I will certainly upload it to Github when I no longer need to use wardriving but at the moment I prefer not to do so, since it can potentially be used to identify my requests to the map server. This part is luxury anyway - you can still get all the information without it.

# Setup

When I am wardriving, I use the built-in Wi-Fi of the Macbook Air as an AP in *Connection Sharing* mode - if you followed the Untraceable Internet tutorial this will work through the GUI. macOS includes a somewhat hidden implementation of OpenBSD's `pf` that you can tune through `/etc/pf.conf`. Just be mindful that at each update of macOS, this file will be overwritten. Allow the traffic between the Wi-Fi AP and the VMWare Fusion box:

```ini
# Apple stuff
nat-anchor "com.apple/*"

# add this line to access the internet from the mobile phone
nat on utun4 from 192.168.0.0/16 to ! 192.168.0.0/16 -> (utun4)

# Apple stuff
rdr-anchor "com.apple/*"
dummynet-anchor "com.apple/*"
anchor "com.apple/*"
load anchor "com.apple" from "/etc/pf.anchors/com.apple"

# add this line to allow communication between gpsRelayd and gpsd
pass from 192.168.0.0/16 to 192.168.0.0/16
```

Also enable network forwarding if it is not already the case:

```shell
sysctl net.inet.ip.forwarding=1
```

Install `gpsd` on the Linux box. On `systemd` systems it will be started automatically by `systemd` when traffic is detected on its port. Configure it to listen to network traffic by editing `/etc/default/gpsd`:

```ini
# The IP address of the Linux box on the VMWare interface
# (refer to the untraceable Internet setup for this one)
DEVICES="udp://192.168.108.128:2947"
GPSD_OPTIONS=""
```

Now connect the Android phone to the Macbook by WiFi and launch `gpsRelayd`. Add a single target device with the IP of the Linux box: `192.168.108.128:2947` in UDP mode and enable *NMEA generation*. From this moment on, launching `gpsmon` on the Linux box should show your realtime location coming from the Android GPS. This setup will save you one precious USB-C port on the Macbook - because the first one is used for powering it through the 12V circuit of the vehicle, while the other one is used by the external WiFi adapter.

You are now ready to launch `airodump`:

```shell
./aircrack-ng/airodump-ng --encrypt opn -a -g --berlin 30 -w ${OUTPUT_FILE} ${WLAN}
```

Adjust carefully the `berlin` option - the time in seconds that the WiFi beacons remain on the screen. It should be long enough to allow you to remain concentrated on driving, looking at the computer screen when stopped at red lights. It should be short enough to be able to quickly take the decision to park on a given spot while knowing that the current WiFi is still in range.

# Legality of wardriving

Wardriving, when used to find official public Wi-Fi APs, is certainly absolutely legal - it is no different than simply enabling Wi-Fi on your mobile phone to connect to nearby networks.

Mapping Wi-Fi APs that are not open to use by the general public is certainly more debatable - on both legal and ethical grounds. Google is doing this in many countries and there have been some objections to this practice, but at the moment the legal precedence stays and the practice is not considered banned and as far as I know, it has never been challenged in court. Google is using this data for bootstrapping their Assisted GPS method of obtaining a very fast first fix when launching Google Maps - a feature that I have covered extensively because of its privacy implications - including being used to track me personally.

Cracking the encryption of secured Wi-Fi APs is certainly universally illegal. It constitutes an illegal access of a computer network by circumventing a technical protection.

Connecting to any open Wi-Fi AP, that advertises itself as such, is also certainly legal in most jurisdictions. An open Wi-Fi AP broadcasts a signal that marks it as accepting any connection. It can be compared to a revolving door with an *OPEN* sign on it.

Connecting to a hidden Wi-Fi AP that is not encrypted is probably not legal - although this should be decided by a court. Connecting to this Wi-Fi requires snooping another client that establishes a connection. This can be compared to entering through a door protected by a digit code by snooping someone else entering this code. It also constitutes an illegal access of a computer network by circumventing a technical protection.

Snooping the traffic of an open unencrypted Wi-Fi network is certainly not ethical, but the legality is questionable. As far as I know, this has never been challenged in court, but if one day it is, there should probably be different rules for broadcast traffic and for station-to-station traffic.

Snooping the traffic of an encrypted network is certainly very illegal and should be considered eavesdropping by circumventing a technical protection.

Usually highly sensible installations will carry a sign somewhere - for example to be displayed on the user screen by a captive portal - that will say something along the lines of *Access to this network is prohibited unless explicitly authorized*. Connecting to this Wi-Fi, even if it is open and unencrypted, is certainly illegal. It could be a simple illegal access of a computer network - if it is not encrypted - or an illegal access to a computer network by circumventing a technical protection - if it is encrypted. The difference between the two is very similar to the difference between trespassing and breaking-and-entering.
