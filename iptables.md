Just a barebones iptables from mcu1. In case yours ever get stuffed, you can reference this one.

```
-P INPUT DROP
-P FORWARD DROP
-P OUTPUT ACCEPT
-N CHINAPOSITIONER
-N MEDIASERVER2
-N RSYNC_IN
-N RSYNC_OUT
-N SECETH
-N SECETH_LOCKED
-N SPOTIFY
-N TCP
-N UDP
-N WEBKITDATABASE
-N WEBKITNETWORK
-N WEBKITPAGE
-A INPUT -i tun8 -p tcp -m tcp --dport 7654 -j ACCEPT
-A INPUT -i tun8 -p tcp -m tcp --dport 4035 -j ACCEPT
-A INPUT -i tun8 -p tcp -m tcp --dport 22 -j ACCEPT
-A INPUT -s 192.168.90.199/32 -i toucan -p tcp -j ACCEPT
-A INPUT -i parrot -p tcp -m tcp --dport 22 -j ACCEPT
-A INPUT -p tcp -j RSYNC_IN
-A INPUT -s 127.0.0.0/8 -d 127.0.0.0/8 -i lo -j ACCEPT
-A INPUT -s 192.168.90.100/32 -d 192.168.90.100/32 -i lo -j ACCEPT
-A INPUT -i lo -j DROP
-A INPUT -p tcp -m tcp --dport 8002 -j DROP
-A INPUT -i toucan -j SECETH
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A INPUT -m conntrack --ctstate INVALID -j DROP
-A INPUT -p icmp -m icmp --icmp-type 8 -m conntrack --ctstate NEW -j ACCEPT
-A INPUT -i tun0 -j ACCEPT
-A INPUT -p udp -m conntrack --ctstate NEW -j UDP
-A INPUT -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -m conntrack --ctstate NEW -j TCP
-A INPUT -p udp -j REJECT --reject-with icmp-port-unreachable
-A INPUT -p tcp -j REJECT --reject-with tcp-reset
-A INPUT -j REJECT --reject-with icmp-proto-unreachable
-A FORWARD -s 192.168.90.101/32 -d 192.168.20.1/32 -i toucan -o parrot -j DROP
-A FORWARD -s 192.168.90.101/32 -i toucan -o parrot -j ACCEPT
-A FORWARD -d 192.168.90.101/32 -i parrot -o toucan -m state --state RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -s 192.168.90.101/32 -i toucan -o ppp0 -j ACCEPT
-A FORWARD -d 192.168.90.101/32 -i ppp0 -o toucan -m state --state RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -s 192.168.90.101/32 -i toucan -o wwan0 -j ACCEPT
-A FORWARD -d 192.168.90.101/32 -i wwan0 -o toucan -m state --state RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -s 192.168.90.103/32 -d 192.168.20.1/32 -i toucan -o parrot -j DROP
-A FORWARD -s 192.168.90.103/32 -i toucan -o parrot -j ACCEPT
-A FORWARD -d 192.168.90.103/32 -i parrot -o toucan -m state --state RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -s 192.168.90.103/32 -i toucan -o ppp0 -j ACCEPT
-A FORWARD -d 192.168.90.103/32 -i ppp0 -o toucan -m state --state RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -s 192.168.90.103/32 -i toucan -o wwan0 -j ACCEPT
-A FORWARD -d 192.168.90.103/32 -i wwan0 -o toucan -m state --state RELATED,ESTABLISHED -j ACCEPT
-A OUTPUT -p tcp -j RSYNC_OUT
-A OUTPUT -m owner --uid-owner 2447 -j WEBKITDATABASE
-A OUTPUT -m owner --uid-owner 2449 -j WEBKITPAGE
-A OUTPUT -m owner --uid-owner 2448 -j WEBKITNETWORK
-A OUTPUT -d 10.32.0.0/16 -o ppp0 -j DROP
-A OUTPUT -d 10.33.0.0/16 -o ppp0 -j DROP
-A OUTPUT -d 10.32.0.0/16 -o wwan0 -j DROP
-A OUTPUT -d 10.33.0.0/16 -o wwan0 -j DROP
-A OUTPUT -d 10.32.0.0/16 -o parrot -j DROP
-A OUTPUT -d 10.33.0.0/16 -o parrot -j DROP
-A OUTPUT -m owner --uid-owner 2444 -j SPOTIFY
-A OUTPUT -m owner --uid-owner 2333 -j CHINAPOSITIONER
-A OUTPUT -m owner --uid-owner 2446 -j MEDIASERVER2
-A CHINAPOSITIONER -d 127.0.0.1/32 -p udp -m udp --dport 8940 -j ACCEPT
-A CHINAPOSITIONER -d 127.0.0.1/32 -p udp -m udp --dport 8941 -j ACCEPT
-A CHINAPOSITIONER -j REJECT --reject-with icmp-port-unreachable
-A MEDIASERVER2 -d 127.0.0.1/32 -p udp -m udp --dport 53 -j ACCEPT
-A MEDIASERVER2 -d 224.0.0.26/32 -p udp -m udp --dport 4083 -j ACCEPT
-A MEDIASERVER2 -d 224.0.0.26/32 -p udp -m udp --dport 4148 -j ACCEPT
-A MEDIASERVER2 -d 224.0.0.26/32 -p udp -m udp --sport 4082 --dport 4999 -j ACCEPT
-A MEDIASERVER2 -p udp -m udp --sport 4082 -j REJECT --reject-with icmp-port-unreachable
-A MEDIASERVER2 -d 224.0.0.0/4 -j REJECT --reject-with icmp-port-unreachable
-A MEDIASERVER2 -p tcp -m tcp --sport 4082 --tcp-flags FIN,SYN,RST,ACK SYN -j REJECT --reject-with icmp-port-unreachable
-A MEDIASERVER2 -d 192.168.90.100/32 -p tcp -m tcp --sport 4082 -j ACCEPT
-A MEDIASERVER2 -d 127.0.0.1/32 -p tcp -m tcp --sport 4082 -j ACCEPT
-A MEDIASERVER2 -d 192.168.0.0/16 -j REJECT --reject-with icmp-port-unreachable
-A MEDIASERVER2 -d 172.16.0.0/12 -j REJECT --reject-with icmp-port-unreachable
-A MEDIASERVER2 -d 10.0.0.0/8 -j REJECT --reject-with icmp-port-unreachable
-A MEDIASERVER2 -d 127.0.0.0/8 -j REJECT --reject-with icmp-port-unreachable
-A RSYNC_IN -p tcp -m tcp --sport 873 -j RETURN
-A RSYNC_OUT -p tcp -m tcp --dport 873 -j RETURN
-A SECETH -j SECETH_LOCKED
-A SECETH_LOCKED -p udp -m udp --dport 18466 -j ACCEPT
-A SECETH_LOCKED -p tcp -m tcp --dport 4035 -j DROP
-A SECETH_LOCKED -p tcp -m tcp --dport 7654 -j DROP
-A SECETH_LOCKED -s 192.168.90.100/32 -j ACCEPT
-A SECETH_LOCKED -s 192.168.90.101/32 -j ACCEPT
-A SECETH_LOCKED -s 192.168.90.102/32 -j ACCEPT
-A SECETH_LOCKED -j DROP
-A SPOTIFY -d 127.0.0.1/32 -p udp -m udp --dport 53 -j ACCEPT
-A SPOTIFY -d 224.0.0.26/32 -p udp -m udp --dport 4211 -j ACCEPT
-A SPOTIFY -d 224.0.0.26/32 -p udp -m udp --sport 4210 --dport 4999 -j ACCEPT
-A SPOTIFY -p udp -m udp --sport 4210 -j REJECT --reject-with icmp-port-unreachable
-A SPOTIFY -d 224.0.0.0/4 -j REJECT --reject-with icmp-port-unreachable
-A SPOTIFY -d 192.168.90.100/32 -p tcp -m tcp --dport 4220 -j ACCEPT
-A SPOTIFY -p tcp -m tcp --sport 4210 --tcp-flags FIN,SYN,RST,ACK SYN -j REJECT --reject-with icmp-port-unreachable
-A SPOTIFY -d 192.168.90.100/32 -p tcp -m tcp --sport 4210 -j ACCEPT
-A SPOTIFY -d 192.168.0.0/16 -j REJECT --reject-with icmp-port-unreachable
-A SPOTIFY -d 172.16.0.0/12 -j REJECT --reject-with icmp-port-unreachable
-A SPOTIFY -d 10.0.0.0/8 -j REJECT --reject-with icmp-port-unreachable
-A SPOTIFY -d 127.0.0.0/8 -j REJECT --reject-with icmp-port-unreachable
-A WEBKITDATABASE -j DROP
-A WEBKITNETWORK -d 127.0.0.1/32 -p udp -m udp --dport 53 -j ACCEPT
-A WEBKITNETWORK -d 224.0.0.0/4 -j REJECT --reject-with icmp-port-unreachable
-A WEBKITNETWORK -d 192.168.0.0/16 -j REJECT --reject-with icmp-port-unreachable
-A WEBKITNETWORK -d 172.16.0.0/12 -j REJECT --reject-with icmp-port-unreachable
-A WEBKITNETWORK -d 10.0.0.0/8 -j REJECT --reject-with icmp-port-unreachable
-A WEBKITNETWORK -d 127.0.0.0/8 -j REJECT --reject-with icmp-port-unreachable
-A WEBKITPAGE -d 127.0.0.1/32 -p udp -m udp --dport 53 -j ACCEPT
-A WEBKITPAGE -d 224.0.0.0/4 -j REJECT --reject-with icmp-port-unreachable
-A WEBKITPAGE -d 192.168.0.0/16 -j REJECT --reject-with icmp-port-unreachable
-A WEBKITPAGE -d 172.16.0.0/12 -j REJECT --reject-with icmp-port-unreachable
-A WEBKITPAGE -d 10.0.0.0/8 -j REJECT --reject-with icmp-port-unreachable
-A WEBKITPAGE -d 127.0.0.0/8 -j REJECT --reject-with icmp-port-unreachable
```
