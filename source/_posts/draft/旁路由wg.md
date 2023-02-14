# 使用 OpenWrt 作为旁路由 搭建 WireGuard

自定义防火墙

```shell
# WireGuard
iptables -A FORWARD -i wg0 -j ACCEPT
iptables -t nat -A POSTROUTING -o br-lan -j MASQUERADE
```
