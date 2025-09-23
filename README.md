# Urban Tech Guerilla

## Dedication

I dedicate the *Urban Tech Guerilla* to Ernesto "Che" Guevara.

Despite having wildly different political ideas, Che has always been an inspiration to me.

A determined fighter who never gave up on liberty, Che has become a symbol of political activism.

Just as his guide inspired people around the world, and as a symbol of political pluralism, I will be writing this new guide, updated for the modern world, for everyone who fights for whatever cause he finds righteous. Because everyone who is willing to sacrifice personal comfort for a cause is worthy of my help.

## Ideology

This guide is meant for political activists and not criminals. In fact, the use of violence is probably the one most important element that has radically changed since Che's original book. Violence is no longer justified in the developed world - and for this we should thank people such as Che. While governments will always find means to oppress the people, in the Internet age, they will rarely do so through violence.

You will find nothing in this guide that is against the law. In cases where the laws vary by jurisdictions, this will be noted.

Another very important statement to make is that liberty always comes with a price. Policing a free society is much more difficult than policing an authoritarian one. If anyone ever uses it to further criminal goals, then this will be an undesirable side effect of liberty.

## Everyday life

Living the Jason Bourne lifestyle is usually much exciting than in the movies. You will be spending large amounts of time on the lookout for many basic necessities that average people take for granted. A place to spend the night, food, water, electricity - nothing will come to you without some effort.

I will try to give you as many tips as possible, but you should know that there are many tricks that I won't be able to share before the conclusion of my own affair since these risk compromising my own safety.

#### Living in a van

These days a delivery van is the ideal choice for anyone leading a clandestine life. A second-hand delivery van is not that expensive and can easily be accommodated with a bed, a small gas appliance for cooking, solar panels, diesel heating for the winter and a small fridge. A shower and a chemical toilet are somewhat more complicated, but are very nice additions too. These will allow you to live almost completely off the grid while substantially lowering your food costs - compared to someone who has to buy ready-to-eat meals.

Once you have been living in a van yourself, you will learn to immediately spot one when it is parked on your street. However most people will never tell the difference. And a carefully built so-called *stealth* van will certainly pass under the radar even for the police.

#### Food

In the developed world, it is generally considered that the cheapest healthy good meal costs about $1 (or 1€) and the minimum amount of money that you need for your daily survival would be about $5 (or 5€). Below this amount, you will have to resort to scavenging. However you should not forget that the only way to go that low is to have an access to a fridge and a cooking stove.

#### Keeping your physical location secret and using the Internet

There are two main legal ways to access the Internet anonymously:
 - dedicated data-only burner eSIM cards that can be bought with crypto currencies
 - wardriving and using public Wi-Fi hotspots

SIM cards offer the best mobility, but you should know that many EU countries severely restrict burner SIM cards - including France where this guide is currently being written. In most EU countries the law says that the mobile operator cannot sell you an anonymous SIM card, but there is nothing preventing you from buying one. This means that those are still available, but the cost is usually higher since you will have to be in roaming mode.

Wardriving for public Wi-Fi hotspots should work very well in every large metropolitan area. These days there are so many of them that usually you will be looking for a good parking place first, and then for a Wi-Fi hotspot.

Wardriving tends to be the safer choice. In most countries there are only a handful of mobile operators and these will be a very good central location for identifying you. Wardriving on the other side gives you access to very large number of completely independent networks that cannot be monitored from a central location.

In the addendum, you will find a tutorial for installing a dedicated Linux Tor router with a firewall using a virtual machine (the current version supports only macOS since this is what I am using, but this is possible on all OS). This router works for both eSIM cards and public Wi-Fi APs.

##### Wardriving for Internet access

I will be writing a separate tutorial for the wardriving setup itself.

You should avoid residential Wi-Fi APs. Even when these are open, they tend to be lower-end lower-power WiFi APs. Professional APs are made to cover large areas and are installed by professionals who know how to place the antennas to achieve the best coverage possible. These are found in most big retail stores, hotels, restaurants, bars, parks, universities, schools, hospitals, conference halls and everywhere where many people tend to come. These public APs will sometimes have restrictions, but Tor can be made to work on any network that allows HTTP(S) access (albeit somewhat slower than on a completely unrestricted network). It is purposely made to be difficult to detect.

Another excellent choice are the corporate APs for visitors. Most large office buildings will have a free AP for business visitors. These tend to offer very good Internet access with few restrictions since the business visitors are supposed to be able to connect to their own corporate VPN networks - which is essentially what you will be doing too.

One thing that you should be wary when using public Wi-Fi APs is to try to vary the locations.  Otherwise you risk to always use the same provider which, if identified, might start tracking you. Refer to the section on *Randomizing the MAC address* in the router guide.

## Addendum

### Practical Guides

An early draft for a guide on how to make untraceable Internet connections using public Wi-Fi APs. I will certainly try to make it accessible to the average layman.

[macOS-untraceable-internet.md](macOS-untraceable-internet.md)
