# Today I Learned

## Docker Private Image Registry가 같은 클러스터에 있을 때, 접근 방법은?

* Problem: 평범한 방법으로는 Docker Private Image Registry 내 Image를 Pull 못한다.
* Problem: Node 상의 Docker는 Kubernetes 내 Internal DNS를 쓰지 못한다.

https://stackoverflow.com/questions/52675900/kubernetes-pull-images-from-internal-registry-with-on-premise-deployment

## 특정 Node 내 Docker Image Cache를 모두 지우는 방법은?

https://forums.docker.com/t/how-to-delete-cache/5753

## PromQL 기초 지식 - Proper Mental Model

Look for a video stream - PromCon EU 2019: PromQL for Mere Mortals

## 2020 백엔드 개발자 로드맵

초보자용 - https://velog.io/@exploit017/2020-백엔드-개발자-로드맵


## Oracle Cloud Virtual Machine Instance - Wireguard VPN 서버로 동작하기 위해 *추가로* 필요한 셋팅

Add the following to wg0.conf:

PostUp = /etc/wireguard/helper/add-nat-routing.sh PostDown = /etc/wireguard/helper/remove-nat-routing.sh

Create two corresponding scripts in /etc/wireguard/helper/ and add execution permissions.  

add-nat-routing.sh: https://pastebin.com/raw/DWRcUjX2 

```
#!/bin/bash
IPT="/sbin/iptables"
IPT6="/sbin/ip6tables"

IN_FACE="ens3"                   # NIC connected to the internet
WG_FACE="wg0"                    # WG NIC
SUB_NET="10.66.66.0/24"          # WG IPv4 sub/net aka CIDR
WG_PORT="59075"                  # WG udp port
SUB_NET_6="fd42:42:42::/64"      # WG IPv6 sub/net

## IPv4 ##
$IPT -t nat -I POSTROUTING 1 -s $SUB_NET -o $IN_FACE -j MASQUERADE
$IPT -I INPUT 1 -i $WG_FACE -j ACCEPT
$IPT -I FORWARD 1 -i $IN_FACE -o $WG_FACE -j ACCEPT
$IPT -I FORWARD 1 -i $WG_FACE -o $IN_FACE -j ACCEPT
$IPT -I INPUT 1 -i $IN_FACE -p udp --dport $WG_PORT -j ACCEPT

## IPv6 (Uncomment) ##
$IPT6 -t nat -I POSTROUTING 1 -s $SUB_NET_6 -o $IN_FACE -j MASQUERADE
$IPT6 -I INPUT 1 -i $WG_FACE -j ACCEPT
$IPT6 -I FORWARD 1 -i $IN_FACE -o $WG_FACE -j ACCEPT
$IPT6 -I FORWARD 1 -i $WG_FACE -o $IN_FACE -j ACCEPT
```

remove-nat-routing.sh: https://pastebin.com/raw/pkf5Vv8Z

```
#!/bin/bash
IPT="/sbin/iptables"
IPT6="/sbin/ip6tables"

IN_FACE="ens3"                   # NIC connected to the internet
WG_FACE="wg0"                    # WG NIC
SUB_NET="10.66.66.0/24"          # WG IPv4 sub/net aka CIDR
WG_PORT="59075"                  # WG udp port
SUB_NET_6="fd42:42:42::/64"      # WG IPv6 sub/net

# IPv4 rules #
$IPT -t nat -D POSTROUTING -s $SUB_NET -o $IN_FACE -j MASQUERADE
$IPT -D INPUT -i $WG_FACE -j ACCEPT
$IPT -D FORWARD -i $IN_FACE -o $WG_FACE -j ACCEPT
$IPT -D FORWARD -i $WG_FACE -o $IN_FACE -j ACCEPT
$IPT -D INPUT -i $IN_FACE -p udp --dport $WG_PORT -j ACCEPT

# IPv6 rules (uncomment) #
$IPT6 -t nat -D POSTROUTING -s $SUB_NET_6 -o $IN_FACE -j MASQUERADE
$IPT6 -D INPUT -i $WG_FACE -j ACCEPT
$IPT6 -D FORWARD -i $IN_FACE -o $WG_FACE -j ACCEPT
$IPT6 -D FORWARD -i $WG_FACE -o $IN_FACE -j ACCEPT
```
