
## pfSense auf deinem Dedicated Server installieren, Host IPv4 Adresse von Proxmox.

Netzwerk config

```bash
auto vmbr0
iface vmbr0 inet static
        address XXXX/XX
        gateway XXXXXX
        bridge-ports XXX
        bridge-stp off
        bridge-fd 0
        bridge_maxwait 0
        post-up echo 1 > /proc/sys/net/ipv4/ip_forward
        post-up iptables -t nat -A PREROUTING -i vmbr0 -p tcp -m multiport ! --dport 22,8006 -j DNAT --to 10.0.10.2
        post-up iptables -t nat -A PREROUTING -i vmbr0 -p udp -j DNAT --to 10.0.10.2

auto vmbr1
iface vmbr1 inet manual
        address 10.0.10.1/30
        bridge-ports none
        bridge-stp off
        bridge-fd 0
        post-up iptables -t nat -A POSTROUTING -s '10.0.10.0/30' -o vmbr0 -j MASQUERADE
        post-down iptables -t nat -D POSTROUTING -s '10.0.10.0/30' -o vmbr0 -j MASQUERADE
#pfSense WAN

auto vmbr2
iface vmbr2 inet manual
        bridge-ports none
        bridge-stp off
        bridge-fd 0
#pfSense LAN1
```
    
