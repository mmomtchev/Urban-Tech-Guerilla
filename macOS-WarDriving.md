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
