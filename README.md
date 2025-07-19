# Replace OpenWrt DHCP and DNS Servers with Kea DHCP4 and BIND9

This guide explains how to replace the default OpenWrt DHCP and DNS servers with **Kea DHCP4** and **BIND9**.

---

## Hardware Environment

- Device Model: Redmi AX6
- CPU: Qualcomm IPQ8071A, 4-core Cortex-A53 @ 1.4GHz
- RAM: 512MB DDR3
- Firmware: Custom compiled OpenWrt 24.10 with NSS support

---

## 1. Install BIND and Kea DHCP4

```bash
opkg update
opkg install bind-server bind-check bind-dnssec bind-tools kea-dhcp4
```

---

## 2. Remove OpenWrt's default dnsmasq and odhcpd-ipv6only

```bash
opkg remove dnsmasq odhcpd-ipv6only
uci -q delete dhcp.@dnsmasq[0]
uci commit dhcp
```

---

## 3. Install and Configure Kea DHCP4

Copy init script and configuration file:

```bash
cp ./kea/etc/init.d/kea-dhcp4 /etc/init.d/
cp ./kea/etc/kea/kea-dhcp4.conf /etc/kea/
cp ./kea/usr/sbin/kea-wrapper /usr/sbin/
```

Edit the DHCP server configuration:

```bash
vim /etc/kea/kea-dhcp4.conf
```

Start and enable Kea DHCP4 service:

```bash
/etc/init.d/kea-dhcp4 start
/etc/init.d/kea-dhcp4 enable
```

---

## 4. Configure BIND9 DNS Server

Edit the main configuration:

```bash
cp ./bind/etc/bind/named.conf /etc/bind/
vim /etc/bind/named.conf
```

(Optional) Edit zone files:

```bash
vim /etc/bind/db.liuyu.dns
vim /etc/bind/db.192.168.1
```

---

## 5. Configure OpenWrt to use local BIND DNS Server

Set WAN DNS to localhost:

```bash
uci set network.wan.dns='127.0.0.1'
uci commit network
```

---

## 6. Start and enable BIND service

```bash
/etc/init.d/named start
/etc/init.d/named enable
```

---

## 7. Other useful commands

Restart services if needed:

```bash
service kea-dhcp4 restart
service named restart
```

---

## 8. Performance Test Report

For detailed performance testing of Kea DHCP4 on IPQ807X platform, see the [performance report](./kea-dhcp4-ipq807x-performance-report.md).

---

This setup replaces the default dnsmasq with a more flexible and robust Kea DHCP4 and BIND9 combination.




