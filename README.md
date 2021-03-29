# Germany-Online-Privacy

This is a repo for people who aren't well versed in web technologies and online privacy, to understand the current problematic political situation and get set up to remain private online.

## Disclaimer

Should you trust me? No. I'm a random dude on the internet. Do not run any code from a random dude on the internet, unless you know what you're doing. Before you do anything I say or take anything I write for granted, I advise you to do your own research.

Also, the usual: Anything to do is at your own risk. 

## Why?

But ephidrineon, why would we want anything like this? We have strong laws to protect us! This is not a big deal at all and you're whining.

I disagree with you. Currently, there are two major problems. First, we have [Vorratsdatenspeicherung](https://en.wikipedia.org/wiki/Data_retention#Germany). This means that a lot of data is collected about what you do and where, and held for six months. The release of that data is not permitted unless a court says so, but there's no easy and inexpensive or free way to get to know what's actually going on.

Second of all, and this is, in my opinion, the much bigger problem: As of March 2021, the Internet in Germany is officially being censored. Yes, you read that right. As of March 2021, [CUII](https://cuii.info) (Clearingstelle Urheberrecht im Internet) is allowed to filter and block websites that could contain copyrighted material. This is currently done using DNS blocking (and we will circumvent that). The issue I take with this is that the blocking has to be done somehow, and that is by inspecting every bit of DNS traffic that leaves your machine. This of course means that there is a potential to log every webpage you visit with a timestamp. Now this does not mean this is actively being done, however, I personally do not trust the whole thing.

#### Why this is a problem

While blocking copyrighted material online could be viewed as a positive move, and I feel that if you want something, you should pay for it, censorship is censorship. At the moment, what is being done is that a DNS request to a page on the blocklist (currently there is only one and I am not going to link it) is filtered on the ISP side and your traffic is redirected to https://notice.cuii.info. 

For now, only the DNS is being captured, inspected and redirected, however, it may be possible that in the future, IP addresses of those services could be blocked entirely aswell. This is just my speculation, though.

CUII claims that the pages which go on the blocklist are thoroughly vetted and only if "everyone agrees" that a page should be blocked, it goes on the list. But ephidrineon, where is the problem with that? Well, see, CUII is made up of private companies. CUII claims that there is a jury of three people, who are to remain anonymous for their protection(?) are the instance which makes the decisions here. Can you see where this is going?

#### You still haven't explained why this is a problem.

If you didn't get the hint in the previous paragraph, here's my take on this: It's about the principle. I should be able to do everything online that I, and only I as the sole authority, desire. 

## The basics

This "guide", if you can call it that, will be split up into a couple of parts. An introduction to basic DNS and VPNs, an informational piece on why both of those would be useful, and a guide to setting up the things I mention.

## Introduction

This is not the ultimate online privacy guide, and it doesn't aim to be. There are a lot more things to consider. What I will try to explain to you is how to get reasonable privacy with (near) untouched usability.

### DNS

I have talked about the internet censorship working with DNS blocking. This is a quick explanation of what DNS is and how it works. For more information, I highly recommend reading through https://howdns.works - it's a simple comic which explains DNS in an easily understandable way.

Think of DNS as the phonebook of the internet. Computers usually don't talk to each other using easily memorable names. If you want to visit a website, the server that holds said website will have an IP address. DNS is the tool used to translate from easily memorable hostnames, like example.com, to IP addresses which are not easy to remember, like 198.51.100.123.

So, if you request a hostname, DNS will handle the translation of that hostname for you. By default, DNS uses UDP over port 53 and the communication is unencrypted. So, an attacker listening on the wire (or your ISP), could easily check out which websites you visit and when.

The talk from your PC to the DNS server would look something like this (highly simplified):

> PC: DNS, u up?
>
> DNS: what do you want
>
> PC: Have an IP for example.com?
>
> DNS: Yeah, the A record on example.com is 198.51.100.123
>
> PC: Nice, thanks!

So now your PC can go to 198.51.100.123 and ask for the page example.com, and it will be served. Easy! 

Now, an ISP which is a member of CUII will interfere with that. The convo between DNS and your PC might instead look like this:

> PC: DNS, u up?
>
> DNS: what do you want
>
> PC: Have an IP for [blocked page]?
>
> ISP: Hol' up. I think you really wanted to go to notice.cuii.info...
>
> ISP: Yo DNS, I make the calls here. Give that guy the IP for notice.cuii.info. 
>
> DNS: The A record on notice.cuii.info is 203.0.113.20
>
> PC: Thanks!

And you, the user, would be none the wiser and just see [this beautifully crafted page](https://notice.cuii.info) which basically tells you to sod off.

### So what can we do about that?

There are two relatively new technologies that can help us circumvent this kind of censorship by simply encrypting the traffic from your PC to the DNS server. The DNS server of your choice, however, has to support this feature, otherwise it won't work.

The technologies I'm talking about are DoH and DoT, (DNS-over-HTTPS and DNS-over-TLS). Both aim to accomplish the same goal: encrypting the DNS requests so someone "listening in on the wire" like our ISPs like to do are not able to inspect the packages and thus won't know where you're trying to navigate.

If you want more information, I highly suggest reading about the technologies themselves:

[DNS over TLS Wikipedia](https://en.wikipedia.org/wiki/DNS_over_TLS)

[DNS over HTTPS Wikipedia](https://en.wikipedia.org/wiki/DNS_over_HTTPS)

#### Great, how do I set that up?

It depends on how you want to use it. The most elaborate way to making sure your DNS queries are encrypted would be to set up your own recursive DNS resolver, like Pi-Hole and Unbound. Contrary to popular belief, just setting a different DNS server to circumvent this **does not work!** The packet inspection is on the provider level, so unless you set up encrypted DNS, the claims that simply "setting your DNS to something else" is nothing but hot air.

I personally use Unbound on OpenWrt and have all DNS traffic forwarded via TLS to dns.google. However, you can do as you please. Here are some great guides to setting up a centralized DNS solution for your home:

How to set up Pi-Hole with Unbound: [anudeepND/pihole-unbound](anudeepND/pihole-unbound)  
How to configure Unbound to use DoT: [Configuring unbound as DNS resolver with DNS-over-TLS and DNSSEC](https://blog.cyclemap.link/2020-01-11-unbound/)

However, you do not need to dive that deep to get reasonably secure name resolution. There are way simpler options to set up DoH or DoT on your device itself.

#### Mobile Devices

**iOS:**

You should use DNSCloak. It's FOSS (Free, Open Source Software). Install the app, choose a server you want (make sure it has "DOH" and "No logs" in the tags!), and hit the play button on top. That's it. [s-s/dnscloak](https://github.com/s-s/dnscloak) 

**Android**

Android 9.0+ has "Private DNS" as a feature built in. The path to get to the setting might vary from device to device, but in general it would be Settings, Network and Internet, Advanced, Private DNS. Set that to "Hostname of private DNS provider" and key in the address. If your device doesn't have Android 9.0 yet, it's time to upgrade.

#### Computers

**Windows 10**

On Windows 10, you can set up DNS over HTTPS somewhat easily. In the newer insider builds, it will be in the network settings and you can set it to only use encrypted DNS. If you have the newest insider build, you know what you're looking for. If you don't, however, you will have to enable it in the registry. This can be automated in a few simple steps:

Step 1: Right-click your start button and click "Windows PowerShell (Admin)"

Step 2: Enter this command. What this does is add a registry value that lets Windows know that yes, we really want to use this feature.  
``reg add HKLM\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters /v EnableAutoDoh /t REG_DWORD /d 2``

Step 3: Reboot your PC.

Step 4: Go to the networking settings and set the DNS server of your choice.

**Linux and macOS**

I personally have no experience with the way it works on these systems. However, there is a setup guide over on DNS-over-HTTPS.com: https://dns-over-https.com/setup/

#### Software

**Google Chrome**

Into the address bar, enter ``chrome://flags/#dns-over-https`` and set the flag to "Enabled". Note that you will need to set your system DNS to a server which supports DoH.

**Firefox**

Open the settings and scroll all the way down until you see "Configure how Firefox connects to the internet" and click "Settings...". In the new window, at the bottom, check "Enable DNS over HTTPS". At "Use Provider", pick your poison. By default, Firefox uses Cloudflare, but you can set that to custom and set your own server.

### But which DNS server should I use?

It's really up to you to decide which DNS services you trust and what you want. The landscape of DNS has changed over the years. It went from "Just use Google" back when I started out worrying about networking to thoroughly vetting the DNS services you use. There are even ad blocking DNS servers out there, which enable you to block advertisements system wide. If you're like me and hate ads especially on your phone, it might be useful to choose an ad filtering DNS for your phone. You don't need root on Android or a Jailbreak on iOS for that. Technology!

The great people at privacytools.io have an exceptional list of privacy oriented DNS servers, so you can make an informed decision. Make sure the server supports DNS over HTTPS!

https://privacytools.io/providers/dns/

## VPN

You're paranoid, you don't trust the internet in this country anymore, and you want the maximal amount of privacy from the German government as possible. Great, here we are. It sounds like a VPN might be for you.

### What a VPN is, and what it isn't

I'll start with the elephant in the room: a VPN is not a magic tool to make you super anonymous on the internet. It really isn't. To get to the bottom of this, I *highly* recommend reading this fantastic excerpt on why VPN providers are a bad choice and when a VPN might be useful: [joepie91/vpn.md](joepie91/vpn.md)

### You still want a VPN? Fine.

I'll show you how to set one up. But first, let me get this straight. I am, for better or for worse, a random dude on the internet. You should not trust me.

**IMPORTANT!** This is the quick-quick-quick guide to set up a VPN server of your own, and it will be using OpenVPN. If you want a more detailed guide on how to do it with a thorough explanation, you might want to watch this video instead of reading my guide: https://www.youtube.com/watch?v=gxpX_mubz2A

#### Pick a location for your server. Seriously, this matters.

You want to set up your own VPN, it is going to require you having an own (virtual) server somewhere. This means that you will have to shell out some cash for it. Shocker, I know.

You should choose a location within Europe (if you're in Germany, which... I guess you are?) that's not Germany. If you really want even more privacy, I recommend a non-fourteen-eyes country. What that is and why it matters, read here: https://en.wikipedia.org/wiki/UKUSA_Agreement especially 3.2.

For now, and in my own subjective opinion, Switzerland looks like a pretty good location.

* It's a non-EU country
* It is mostly politically neutral
* In the past, Switzerland has proven very liberal towards piracy and internet freedom
* It's a non-fourteen-eyes country

Book a VPS in Switzerland (or any other country you like). The important characteristics here are that it's NOT an OpenVZ VM. I will not recommend any providers here. I had Microsoft Azure credits left, so I used that. Microsoft has an Azure Datacenter in the North of Switzerland and the virtualization is carried out using Hyper-V. Be advised though that it is by far not the cheapest option out there. If you choose Azure, make sure to set the NIC security group to "Advanced". Otherwise you're in for a world of pain.

For a VPN, you don't need a lot of performance, really. The smallest tiers at most providers shoud be fine, as long as it has a decent internet uplink. Another HARD REQUIREMENT is a public IPv4 address. Make sure you get one of those.

#### The quick-quick-quick guide to VPN glory (using OpenVPN)

Step 1: Provision a virtual server. Choose any Linux operating system you like and if possible, make sure you authenticate with an SSH key.

Step 2: Connect to the server via SSH.

Step 3: This is an example for Debian-based systems like Ubuntu and, well, Debian. Run these commands to update your system and install some basic tools we will need.
`sudo apt update && sudo apt upgrade`
`sudo apt install curl wget speedtest-cli vnstat`

Step 4: Once that is finished, execute ``speedtest-cli`` and look at the values. Since the server is located in a datacenter, the values should *exceed* the internet speed you're getting. This is what you want. If that's not the case, try it again after rebooting the VM. If you still get sub-par speeds, take advantage of that money back guarantee.

Step 5: Execute the great OpenVPN install script by angristan. Instructions over there: [angristan/openvpn-install](angristan/openvpn-install)

Step 6: Edit the OpenVPN server configuration file and change ``verb 3`` to ``verb 0``. This disables all logging on the server: ``sudo nano /etc/openvpn/server.conf``

Step 7: Restart the OpenVPN service: ``sudo systemctl restart openvpn@server``

Now you can grab your .ovpn file and import it to your OpenVPN client (like OpenVPN Connect V3 on Windows) and that's it!

If you don't know how you should get the .ovpn file, the simplest way is to ``cat`` it, copy the output and paste it into a Notepad window, and save that as a .ovpn file. ``cat ./nameofyourclient.ovpn``



