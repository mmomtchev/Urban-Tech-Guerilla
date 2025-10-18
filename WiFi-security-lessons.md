# Lessons to learn when it comes to Wi-Fi security

Paradoxically, some of the best secured Wi-Fi APs are the residential Internet boxes.

How comes?

The answer is very simply - these usually come as a package designed by a small team with very significant experience working with the general public. The box team knows very well that the average user won't go through a complicated setup and they will be supplying hundreds of thousands, if not millions, of these boxes. After decades of crusading by the IT security industry, these teams have finally learned that the *secure by default* is the only right configuration. Besides, those boxes are a very dynamic market with tough competition and they are usually replaced often. I have rarely seen residential boxes that are more than 10 years old.

On the other hand, small professional installations are very often ad-hoc installations, performed by a small company that does many other things besides Wi-Fi. They are almost always customized to meet the specific requirements of the client. If the client is an IT company, these tend to have some good understanding of IT security. If it is not, no one will know. Also, very often, the installation will never get replaced or upgraded, as long as it works for its original purpose.

The worst offenders are usually small branch offices of administratively challenged organizations - especially government and NGOs.

On the residential side, there are three big holes left to plug in:
 * WPS authentication

  WPS authentication is still enabled by default on many boxes. It is very weak and this is not common knowledge - I learned this when I started to wardrive. Almost everyone knows about WEP being weak and that WPA passwords need to be long to be secure, but WPS is much more rarely discussed and it is a huge problem. A modern 3D graphics card can crack it in a second or so. A general purpose CPU will need a few minutes. It is very often enabled by default on consumer devices.

 * Wi-Fi range extenders
  
  4 out of 5 residential Wi-Fi range extenders are open and unencrypted. This is where the process of the box fails - the user finds that it is much easier to disable the box password then to configure the range extender. As the range extender has a very good amplified antenna, this signal becomes usable at a great distance.

* Various home appliances that can be configured with a mobile phone

  Very often the user won't do it properly and the device will be left in a permanently open state.

On the commercial side, the situation is much more complicated. Every single network is different. These days it is not that much about the classical old-style computer networks - it is the Internet of Things and the way it interacts with the old-style computer networks. It is gaining traction very fast, devices are very cheap and both the users and the makers are very inexperienced when it comes to security.
