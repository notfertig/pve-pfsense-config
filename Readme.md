
## pfSense auf deinem Dedicated Server mit 1er IP Adresse installieren (Proxmox).

pfSense Download Link: https://www.pfsense.org/download/
_____________________________________________
qemu-guest-agent (host):
Install Command:
```bash
pkg install qemu-guest-agent
```

Shellcmd Command (pfSense):
```bash
service qemu-guest-agent start
```
 /etc/rc.conf Zusatz Konfiguration (host):
 ```bash
qemu_guest_agent_enable="YES"
qemu_guest_agent_flags="-d -v -l /var/log/qemu-ga.log" 
```
_____________________________________________

Netzwerk Config:

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
    
