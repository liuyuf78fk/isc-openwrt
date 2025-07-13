# Replace OpenWrt DHCP and DNS Servers with Kea DHCP4 and BIND9

This guide explains how to replace the default OpenWrt DHCP and DNS servers with **Kea DHCP4** and **BIND9**.

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
cp ./kea-dhcp4/etc/init/kea-dhcp4 /etc/init/
cp ./kea-dhcp4/etc/kea/kea-dhcp4.conf /etc/kea/
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

This setup replaces the default dnsmasq with a more flexible and robust Kea DHCP4 and BIND9 combination.
