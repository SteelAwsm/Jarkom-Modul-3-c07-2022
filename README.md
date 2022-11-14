# Jarkom-Modul-3-c07-2022

Anggota Kelompok C07 :
* 5025201274 - Alif Adrian Anzary
* 5025201122 - Marsyavero Charisyah Putra
* 5025201209 - Hemakesha Ramadhani Heriqbaldi

# Topologi

# Konfigurasi Network Node
## Ostania
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
        address 10.13.1.1
        netmask 255.255.255.0

auto eth2
iface eth2 inet static
        address 10.13.2.1
        netmask 255.255.255.0

auto eth3
iface eth3 inet static
        address 10.13.3.1
        netmask 255.255.255.0
```

## SSS
```
auto eth0
iface eth0 inet dhcp
```

## Garden
```
auto eth0
iface eth0 inet dhcp
```

## WISE
```
auto eth0
iface eth0 inet static
        address 10.13.2.2
        netmask 255.255.255.0
        gateway 10.13.2.1
```
  
## Berlint
```
auto eth0
iface eth0 inet static
        address 10.13.2.3
        netmask 255.255.255.0
        gateway 10.13.2.1
```

## Westalis
```
auto eth0
iface eth0 inet static
        address 10.13.2.4
        netmask 255.255.255.0
        gateway 10.13.2.1
```

## Eden
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 4a:88:6d:54:a5:d1
```

## NewstonCastle
```
auto eth0
iface eth0 inet dhcp
```

## KemonoPark
```
auto eth0
iface eth0 inet dhcp
```

# Inisiasi (Ostania)
Pertama Ostania dan node-node perlu dikoneksikan ke dalam internet, pada node ostania, tuliskan:
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.13.0.0/16
cat /etc/resolv.conf
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

lalu di tiap node, tuliskan:
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

# Nomor 1 dan 2
### 1.1. DNS Server (WISE)
bind9 perlu diinstall dalam node WISE untuk menjadikannya sebagai DNS server.
```
apt-get update
apt-get install bind9 -y
```

### 1.2. DHCP Server (Westalis)
isc-dhcp-server perlu diinstall dalam node Westalis untuk menjadikannya sebagai DHCP server.
```
apt-get update
apt-get install isc-dhcp-server -y
```

### 1.3. Proxy Server (Berlint)

squid perlu diinsall dalam node Berlint untuk menjadikannya sebagai Proxy Server.
```
apt-get update
apt-get install libapache2-mod-php7.0 -y
apt-get install squid -y
```

### 2. DHCP Relay (Ostania)

isc-dhcp-relay perlu diinsall dalam node Ostania untuk menjadikannya sebagai DHCP Relay.
```
apt-get update
apt-get install isc-dhcp-relay -y
```

lalu, jika proses instalasi memunculkan argumen, maka ketik (secara urut tiap argumen):

```
10.13.2.4
```
```
eth1 eth3 eth2
```
```
apt-get install isc-dhcp-relay -y
```


# Nomor 3

Pada ostania (DHCP relay), buka file `/etc/default/isc-dhcp-relay` dan pastikan konfigurasi sama seperti berikut
```
# Defaults for isc-dhcp-relay initscript
# sourced by /etc/init.d/isc-dhcp-relay
# installed at /etc/default/isc-dhcp-relay by the maintainer scripts

#
# This is a POSIX shell fragment
#

# What servers should the DHCP relay forward requests to?
SERVERS="10.13.2.4"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth1 eth3 eth2"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
```

jika belum, ubah konfigurasi seperti diatas. </br>


Pada Westalis (DHCP server), buka file `/etc/default/isc-dhcp-server` dan ubah konfigurasi menjadi seperti berikut
```
# Defaults for isc-dhcp-server initscript
# sourced by /etc/init.d/isc-dhcp-server
# installed at /etc/default/isc-dhcp-server by the maintainer scripts

#
# This is a POSIX shell fragment
#

# Path to dhcpd's config file (default: /etc/dhcp/dhcpd.conf).
#DHCPD_CONF=/etc/dhcp/dhcpd.conf

# Path to dhcpd's PID file (default: /var/run/dhcpd.pid).
#DHCPD_PID=/var/run/dhcpd.pid

# Additional options to start dhcpd with.
#       Don't use options -cf or -pf here; use DHCPD_CONF/ DHCPD_PID instead
#OPTIONS=""

# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
#       Separate multiple interfaces with spaces, e.g. "eth0 eth1".
INTERFACES="eth0"
```

Pada Westalis (DHCP server) buka file `/etc/dhcp/dhcpd.conf` dan ubah konfigurasi server sehingga sama sebagai berikut:
```
subnet 10.13.1.0 netmask 255.255.255.0 {
    range 10.13.1.50 10.13.1.88;
    range 10.13.1.120
    10.13.1.155;
    option routers 10.13.1.1;
    option broadcast-address 10.13.1.255;
    option domain-name-servers 10.13.2.2;
    default-lease-time 360;
    max-lease-time 7200;
}

subnet 10.13.2.0 netmask 255.255.255.0 {
    range 10.13.2.5 10.13.2.254;
    option routers 10.13.2.1;
    option broadcast-address 10.13.2.255;
    option domain-name-servers 10.13.2.2;
    default-lease-time 360;
    max-lease-time 7200;
}
```


# Nomor 4

Pada Westalis (DHCP server) buka file `/etc/dhcp/dhcpd.conf` dan ubah konfigurasi server sehingga sama sebagai berikut:
```
subnet 10.13.3.0 netmask 255.255.255.0 {
    range 10.13.3.10 10.13.3.30;
    range 10.13.3.60 10.13.3.85;
    option routers 10.13.3.1;
    option broadcast-address 10.13.3.255;
    option domain-name-servers 10.13.2.2;
    default-lease-time 720;
    max-lease-time 7200;
}
```

# Nomor 5


untuk menguhubungkan client ke internet, pada WISE (DNS Server), buka file `/etc/bind/named.conf.options` dan ubah konfigurasi sehingga sama seperti berikut:

```
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0's placeholder.

        forwarders {
        192.168.122.1;
        };

        allow-query{any;};

        //=====================================================================$
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //=====================================================================$
        // dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```

# Nomor 6


Pada Westalis (DHCP Server), buka file `/etc/dhcp/dhcpd.conf`,  dalam subnet interface switch 1 (10.13.1.0) serta 3 (10.13.3.0) rubah konfigurasi menjadi sama sebagai berikut (sesuai dengan ketentuan soal: switch1 dengan 5 menit, swicth3 dengan 10 menit, dan waktu peminjaman maksimal selama 115 menit):
```
subnet 10.13.1.0 netmask 255.255.255.0 {
	...
    default-lease-time 300;
    max-lease-time 6900;
}

subnet 10.13.3.0 netmask 255.255.255.0 {
	...
    default-lease-time 600;
    max-lease-time 6900;
}
```

# Nomor 7

Dalam Node Eden, tuliskan command `ip a` lalu lihat `link/ether` pada eth0

```
link/ether 4a:88:6d:54:a5:d1
```

Link/ether tersebut digunakan untuk membuat fixed address. 
Dalam Westalis (DHCP Server) buka file `/etc/dhcp/dhcpd.conf` lalu tambahkan konfigurasi Pada `hardware ethernet` dengan isi nilai link/ether.

```
host Eden {
    hardware ethernet 4a:88:6d:54:a5:d1;
    fixed-address 10.13.3.13;
}
```

Kemudian dalam Eden, ubah isi file `/etc/network/interfaces` diubah menjadi seperti berikut:

```
auto eth0
iface eth0 inet dhcp
hwaddress ether 4a:88:6d:54:a5:d1
```
Kemudian dalam Westalis (DHCP Server) service dhcp server serta node Eden direstart.
