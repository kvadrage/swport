# swport
swport - switch port configuration tool.

This tool was made to make it easier to manage networking ports on Linux-based
HW switches running [switchdev](https://www.kernel.org/doc/Documentation/networking/switchdev.txt) (like [Mellanox Spectrum](https://github.com/mellanox/mlxsw/wiki#mlxsw)).

## Usage
It works as a wrapper to ip and devlink tools from [iproute2](http://man7.org/linux/man-pages/man8/ip.8.html) package to support
the following operations:
* index switch ports by multiple attributes:
    * port netdev name (eth1, sp1, swp1, ...)
    * port front panel number (1, 2, 3, ...)
    * port switchdev portname (p1, p2, p3s0, ...)
    * port mac address (11:22:33:44:55:66)

* display aggregate port information
    * current port netdev name
    * port front panel number
    * port switchdev portname
    * port switchdev SWID
    * port PCI ID
    * port MAC address
    * port split count

Check help for additional details:
```
$ swport help
```

## Examples
**Show: show port information**
```
$ swport show 1
swp1: {'port_name': u'p1', 'port_swid': u'506b4b8c8400', 'port_pci': u'pci/0000:01:00.0/45', 'port_mac': u'50:6b:4b:8c:84:2d', 'port_no': u'1'}
```

**Rename: rename switch ports netdev name**
* automatically with "sw" prefix and "s" delimiter for split ports (disable and then enable ports with rename)
```
$ swport rename -su
swp1s0, swp1s1, ..., swp2, swp3, ...
```
* automatically by prefix, added to port switchdev portname
```
$ swport rename -p "sd" -d "n"
sdp1n0, sdp1n1, ..., sdp2, sdp3, ...
```
* manually by mapping between port front panel number and desired netdev name
```
$ swport rename -t port-number "1=swp1,2=swp2,3=swp3"
swp1, swp2, swp3
```
* manually by mapping between port MAC address and desired netdev name
```
$ swport rename -t port-number "11:22:33:44:55:66=swp1"
swp1
```

**Split: split ports into cable break-outs (2 or 4 split ports)**
```
$ swport split -r -u "1=4,3=4,5=2,32=2"
swp1s0-3, swp3s0-3, swp5s0-1, swp32s0-1
```
`-r` - autorename flag
`-u `- unsplit unused ports
`-p` and `-d` flags for prefix and delimiter can also be specified

**Unsplit: unsplit port**
```
$ swport unsplit -r "1,3,5,32"
```
`-r` - autorename flag
`-p` and `-d` flags for prefix and delimiter can also be specified

**Reset: reset port(s) configuration**
```
$ swport reset -s "1,3,5,32"
```
`-r` - shutdown after reset
