Display the NAT rules
```shell
sudo iptables -t nat -v -L -n --line-number
sudo iptables -t nat -v -L PREROUTING -n --line-number
```



To forward all UDP traffic from 10.248.0.140:53 to 192.168.49.2:30007
```shell
iptables -t nat -A PREROUTING -p udp --dport 53 -j DNAT --to-destination 192.168.49.2:30007
iptables -t nat -A POSTROUTING -p udp -d 192.168.49.2 --dport 30007 -j SNAT --to-source 10.248.0.140:53
```
Check the PREROUTING
```shell
sudo iptables -t nat -v -L PREROUTING -n --line-number
```
```shell
Chain PREROUTING (policy ACCEPT 63 packets, 8011 bytes)
num   pkts bytes target     prot opt in     out     source               destination
1      126 16460 PREROUTING_direct  all  --  *      *       0.0.0.0/0            0.0.0.0/0
2      126 16460 PREROUTING_ZONES  all  --  *      *       0.0.0.0/0            0.0.0.0/0
3       30  2348 DOCKER     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL
4        0     0 DNAT       udp  --  *      *       10.248.0.140         0.0.0.0/0            udp dpt:53 to:192.168.49.2:30007
```
Check the POSTROUTING
```shell
sudo iptables -t nat -v -L POSTROUTING -n --line-number
```


To delete a rule:
```shell
sudo iptables -t nat -D PREROUTING {line_number}
```

To forward all UDP traffic from ens10f0 to br-9568ebc0b975

```shell
sudo iptables -A FORWARD -p udp -i ens10f0 -o br-9568ebc0b975 -j ACCEPT
sudo iptables -A FORWARD -p udp -i ens10f0 -o br-9568ebc0b975 -m state --state ESTABLISHED,RELATED -j ACCEPT
sudo iptables -t nat -A POSTROUTING -o br-9568ebc0b975 -j MASQUERADE
```
