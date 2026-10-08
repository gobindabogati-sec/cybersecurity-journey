# Networking basics

## What a network is

A network is two or more devices connected so they can share data. Your phone on home Wi-Fi, a company's office, the whole internet: same idea at different sizes.

- LAN (local area network): one building or home. Fast, owned by you.
- WAN (wide area network): connects LANs over long distances. The internet is the biggest one.
- WLAN: a LAN over Wi-Fi.

## Devices you'll see everywhere

| Device | What it does |

| Router | Moves traffic between different networks (e.g. your home network and your ISP). Works with IP addresses. |
| Switch | Connects devices inside the same network. Works with MAC addresses. |
| Access point | Lets wireless devices join the network. |
| Firewall | Allows or blocks traffic based on rules. |
| Modem | Converts the signal from your ISP (cable, fibre, DSL) into something your router can use. |

A home "router" is usually all of these in one box.

## IP addresses

Every device on a network gets an IP address so traffic knows where to go.

IPv4 looks like `192.168.1.10`. Four numbers from 0 to 255, so 32 bits total. There are only about 4.3 billion of them, which isn't enough, hence IPv6.

IPv6 looks like `2001:0db8:85a3::8a2e:0370:7334`. 128 bits, so we won't run out.

### Private vs public

Private ranges are used inside networks and aren't routed on the internet:

- `10.0.0.0 – 10.255.255.255`
- `172.16.0.0 – 172.31.255.255`
- `192.168.0.0 – 192.168.255.255`

Your public IP is the one the internet sees, assigned by your ISP. NAT (network address translation) on the router lets all the private devices at home share that one public IP.

`127.0.0.1` is localhost, meaning "this machine".

### Subnet mask and CIDR

The subnet mask splits an IP into the network part and the host part.

`192.168.1.10` with mask `255.255.255.0` means the first three numbers are the network, the last one is the host. In CIDR notation that's written `192.168.1.10/24` (24 bits for the network).

A /24 gives 256 addresses, 254 usable (one is the network address, one is broadcast).

## MAC addresses

A MAC address is the hardware address burned into a network card, e.g. `00:1A:2B:3C:4D:5E`. IP is like your postal address and can change. MAC is closer to a serial number. Switches use MAC addresses to deliver traffic inside a LAN.

ARP (address resolution protocol) is how a device finds the MAC address that belongs to an IP on the local network. ARP spoofing is a classic attack because ARP trusts whatever reply it gets.

## DHCP and DNS

DHCP hands out IP addresses automatically when a device joins the network. It also gives the device its subnet mask, default gateway (the router) and DNS server.

DNS turns names into IP addresses. When I type `github.com`, my computer asks a DNS server which IP that is, then connects to the IP. Without DNS we'd be memorising numbers.

Common DNS record types:

- `A`: name to IPv4
- `AAAA`: name to IPv6
- `CNAME`: alias for another name
- `MX`: mail server for the domain
- `TXT`: text, often used for verification and email security (SPF, DKIM)

## TCP vs UDP

Both carry data between applications, but they behave differently.

| | TCP | UDP |
|---|---|---|
| Connection | Sets one up first | Just sends |
| Reliable | Yes, resends lost packets | No |
| Order | Guaranteed | Not guaranteed |
| Speed | Slower | Faster |
| Used for | Web, email, file transfer, SSH | Video calls, gaming, DNS lookups, streaming |

### TCP three-way handshake

1. Client sends SYN ("I want to connect")
2. Server replies SYN-ACK ("ok, I'm ready")
3. Client sends ACK ("got it")

Now the connection is open. A SYN flood attack sends loads of SYNs and never finishes step 3, so the server fills up with half-open connections.

## Ports

An IP gets traffic to the right machine. A port gets it to the right program on that machine. There are 65,535 of them. Ports 0 to 1023 are the "well-known" ones.

| Port | Protocol | What it's for |
|---|---|---|
| 20, 21 | FTP | File transfer (unencrypted) |
| 22 | SSH | Secure remote login |
| 23 | Telnet | Remote login, unencrypted, shouldn't be used |
| 25 | SMTP | Sending email |
| 53 | DNS | Name lookups |
| 67, 68 | DHCP | Handing out IPs |
| 80 | HTTP | Web, unencrypted |
| 110 | POP3 | Receiving email |
| 143 | IMAP | Receiving email |
| 443 | HTTPS | Web, encrypted |
| 445 | SMB | Windows file sharing |
| 3389 | RDP | Windows remote desktop |

These come up constantly in port scans. An open 23 or 3389 on a public server is usually a red flag.

## The OSI model

A 7-layer way of describing how data moves across a network. Real networks follow the simpler TCP/IP model, but OSI is the vocabulary everyone uses ("that's a layer 7 attack").

| # | Layer | What happens | Examples |
|---|---|---|---|
| 7 | Application | What the user's app talks | HTTP, DNS, SMTP |
| 6 | Presentation | Formatting, encryption | TLS, JPEG |
| 5 | Session | Keeping conversations open | Sessions, sockets |
| 4 | Transport | End-to-end delivery, ports | TCP, UDP |
| 3 | Network | Routing between networks | IP, ICMP, routers |
| 2 | Data link | Delivery within a LAN | Ethernet, MAC, switches |
| 1 | Physical | Actual signals | Cables, Wi-Fi radio |

How I remember it, bottom to top: Please Do Not Throw Sausage Pizza Away (Physical, Data link, Network, Transport, Session, Presentation, Application).

### TCP/IP model

Four layers that map onto OSI:

- Application (OSI 5–7)
- Transport (OSI 4)
- Internet (OSI 3)
- Network access (OSI 1–2)

## Commands I practised

bash
ipconfig                # Windows: show my IP, mask, gateway
ip a                    # Linux equivalent
ping 8.8.8.8            # is a host reachable?
tracert google.com      # Windows: show each hop to a host
traceroute google.com   # Linux
nslookup github.com     # DNS lookup
netstat -an             # open connections and listening ports
arp -a                  # local ARP table (IP to MAC)


## Why this matters for security

- You can't read a Wireshark capture or an Nmap scan without knowing ports, protocols and the handshake.
- Firewall rules are written in terms of IPs, ports and protocols.
- Lots of attacks target a specific layer: ARP spoofing (layer 2), IP spoofing (3), SYN floods (4), phishing sites and SQL injection (7).
- Knowing what "normal" traffic looks like is how you spot the weird stuff.

## Next up

- Subnetting practice (working out ranges by hand)
- Wireshark: capture my own traffic and find the handshake
- Nmap basics on my own lab machines
