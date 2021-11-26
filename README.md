# JunOS Filter Elements
Junos Filter Elements by Protocol

These filter elements aim to provide RFC-referenced and compliant filtering. They would typically be used to build a Routing Engine (RE) Filter and be applied inbound on the lo0 interface. They use the principle of least privilege e.g. the filter elements are as specific as possible

Feedback and corrections welcome, please submit a pull request or open an issue to discuss

## Junk
Things that should be blocked as early as possible, and will never be processed by the device.

 * [inet](junk/inet) [inet6](junk/inet6)

## Protocols
Individual filter elements for each protocol

* [BGP](bgp) [inet](bgp/inet) [inet6](bgp/inet6)
* [OSPF](ospf) [inet](ospf/inet) [inet6](ospf/inet6)
* [VRRP](vrrp) [inet](vrrp/inet) [inet6](vrrp/inet6)
* [ICMP](icmp) [inet](icmp/inet) [inet6](icmp/inet6)
* [BFD](bfd) [inet](bfd/inet) [inet6](bfd/inet6)

## Management Protocols
Individual filter elements for each management protocol

* [SSH](ssh)
* [SNMP](snmp)
