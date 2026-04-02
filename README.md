# badvpn-tun2socks (with fwmark support)

> Forked from [ambrop72/badvpn](https://github.com/ambrop72/badvpn) — All credit to the original author **Ambroz Bizjak** (ambrop7@gmail.com)

A patched version of badvpn-tun2socks with **`--fwmark` support** for policy routing bypass.

## What's Changed

Added `--fwmark <mark>` option that sets `SO_MARK` on all outgoing SOCKS5 sockets. This is essential for VPN/tunnel setups where policy routing would otherwise cause a routing loop.

### Example
```bash
badvpn-tun2socks \
  --tundev tun1 \
  --netif-ipaddr 198.18.0.1 \
  --netif-netmask 255.255.255.0 \
  --socks-server-addr 1.2.3.4:1080 \
  --username myuser \
  --password mypass \
  --fwmark 100 \
  --socks5-udp
```

With `ip rule add fwmark 100 table main priority 10`, traffic from badvpn-tun2socks bypasses the tun routing table and goes directly to the SOCKS5 proxy.

## Features

- Lightweight C implementation (~1.3 MB binary, ~1-2 MB RAM)
- SOCKS5 with username/password authentication
- UDP support (via `--socks5-udp` or `--udpgw-remote-server-addr`)
- **NEW: `--fwmark` for policy routing bypass**
- IPv4 and IPv6 support

## Build

```bash
git clone https://github.com/Seng555/badvpn-tun2socks.git
cd badvpn-tun2socks
mkdir build && cd build
cmake .. -DBUILD_NOTHING_BY_DEFAULT=1 -DBUILD_TUN2SOCKS=1
make -j$(nproc)
# Binary at: build/tun2socks/badvpn-tun2socks
sudo cp tun2socks/badvpn-tun2socks /usr/local/bin/
```

## All Options

```
--tundev <name>                  TUN device name
--netif-ipaddr <addr>            Network interface IP (required)
--netif-netmask <mask>           Network interface netmask (required)
--netif-ip6addr <addr>           IPv6 address
--socks-server-addr <addr>       SOCKS5 server address (required)
--username <username>            SOCKS5 username
--password <password>            SOCKS5 password
--password-file <file>           SOCKS5 password from file
--append-source-to-username      Append client source IP to username
--fwmark <mark>                  Set SO_MARK on outgoing sockets
--socks5-udp                     Use native SOCKS5-UDP
--udpgw-remote-server-addr       UDP gateway server address
--udpgw-max-connections <num>    Max UDP connections (default: 256)
--udpgw-transparent-dns          Enable transparent DNS via udpgw
--loglevel <0-5>                 Log level
```

## Credits

- **Original Author**: [Ambroz Bizjak](https://github.com/ambrop72) — [ambrop72/badvpn](https://github.com/ambrop72/badvpn)
- **Fork Maintainer**: [Seng555](https://github.com/Seng555)
- **License**: BSD (same as original)

## Original Project

This is a fork of [BadVPN](https://github.com/ambrop72/badvpn) which includes:
- NCD programming language
- tun2socks network-layer proxifier
- Peer-to-peer VPN
- UDP gateway (udpgw)

This fork focuses specifically on the **tun2socks** component with the addition of fwmark support for production VPN/proxy deployments.
