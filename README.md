# JunOS Filter Elements
Junos Filter Elements by Protocol

These filter elements aim to provide RFC-referenced and compliant filtering. They would typically be used to build a Routing Engine (RE) Filter and be applied inbound on the lo0 interface. They use the principle of least privilege e.g. the filter elements are as specific as possible

Feedback and corrections welcome, please submit a pull request or open an issue to discuss

## Thigs to decide First
When constucting a loopback filter there are two questions that need to be answered first:
 * Will your router need to process IP Options?
 * Will your router need to process fragments?

These packets are punted to the RE and a possible attack vector. If you don't need these packets on your router, discard them first.

### IP Options

If your network runs RSVP on an older version of JunOS <15.2 (or uses multiple vendors) then perhaps it relies upon the IP Options Router Alert bit. Upon receipt of an packet with this bit set, the router will punt it to the RE, opening a possible attack vector. If you run RSVP the chances are it now uses RFC2961 refresh reduction which prohibits the use of the Router Alert bit in bundled messages.

If you are sure your network does not need IP Options processing, then deal with this first.
 * [Discard Term for IPv4 Options](options/inet/input.conf)

### Fragments

In a properly dimensioned network, fragmentation should not occur to the router control plane.

In IPv4, the first-fragment is easily identified:
 * More Fragments bit set to 1
 * Fragment Offset field set to 0

If you are not doing fragment processing, then there is no point in completing further layer 3/4 matches on this packet. It needs to go, and accepting it will only take up reassembly buffer space. It should be immediately dropped.

Subsequent IPv4 Fragments will have:
 * Fragment Offset field non-zero

Because the RE filters are simply bitwise matching on the packet header, there is a theoretical possibility subsequent fragment payloads could match a firewall filter term. So to be absolutely sure, any packet with a non-zero fragment offset should be immediately discarded before further terms are evaluated.

 * [Discard Term for IPv4 Fragments](fragments/inet/input.conf)

With IPv6, because all fragments are placed in fragment headers, they can be identified easily:
 * next-header value set to fragment (44).

When constructing an IPv6 RE filter, it is safer to accept on next-header. The next header cannot be bypassed and typically everything else gets dropped at the final term. So if you have a long list of next-header accept terms, and then a discard term at the end, IPv6 fragments will be safely dropped here.

## General Approach

When constructing a firewall filter it can quickly get complex. If you use abstraction to multiple levels this can make it more difficult to read. For this reason, the examples here don't refer to prefix lists or other filters. This makes it clearer what is allowed for each protocol. There are also problems with using prefix lists:
 * The temptation to re-use them can mean your filters aren't as specific as they could be
 * Using JunOS apply-path wildcards in prefix lists can open your filter much wider than you expect

In the case of an apply-path wildcard such as this:
```
    prefix-list router-ipv4 {
        apply-path "interfaces <*> unit <*> family inet address <*>";
    }
```
You might think that the result will be the /32 addresses of each interface. But this actually takes the subnet of the interface. This can be danegerous if you are connected to a large peering LAN for example. All the peers would potentially be permitted by your filters referring to this prefix-list!

Know exactly what you are permitting and where you are permitting it.

## Protocols
Individual filter elements for each protocol

* [BGP](bgp) [inet](bgp/inet) [inet6](bgp/inet6)
* [OSPF](ospf) [inet](ospf/inet) [inet6](ospf/inet6)
* [VRRP](vrrp) [inet](vrrp/inet) [inet6](vrrp/inet6)
* [ICMP](icmp) [inet](icmp/inet) [inet6](icmp/inet6)
* [RSVP](rsvp) [inet](rsvp/inet)
* [BFD](bfd) [inet](bfd/inet) [inet6](bfd/inet6)

## Management Protocols
Individual filter elements for each management protocol

* [SSH](ssh)
* [SNMP](snmp)
