# MicroSocks - Multithreaded, Small, Efficient SOCKS5 Server
[简体中文](README.md) | English

## Table of Contents
- [Introduction](#introduction)
- [Features](#features)
- [History](#history)
- [Command Line Options](#command-line-options)
  - [One-time Authentication Example](#one-time-authentication-example)
- [Supported SOCKS5 Features](#supported-socks5-features)
- [Troubleshooting](#troubleshooting)

## Introduction
MicroSocks is a SOCKS5 service that you can run on your remote boxes to tunnel connections through them, if for some reason SSH doesn't cut it for you.

## Features
- Lightweight Design
  - Spawns a thread with a low stack size for each client
  - Main process consumes virtually no resources
  - Limited only by file descriptors and available RAM

- Robust Operation
  - Handles resource exhaustion gracefully
  - Simply denies new connections when resources are low, instead of crashing like other programs

- Easy to Use
  - No configuration file needed
  - Everything configurable via command line
  - Often requires no parameters for quick setup

## History
This is the successor of "rocksocks5", written with different goals in mind:
- Prefer usage of standard libc functions over homegrown ones
- No artificial limits
- Focus on minimal source code size and maximal readability, reusability, and extensibility

As a result, IPv4, DNS, and IPv6 are supported out of the box using the same code. In contrast, rocksocks5 uses multiple compile-time defines to reduce binary size, achieving as low as 10 KB when only IPv4 support is enabled.

Even so, when optimized for size, this program is under 50 KB when statically linked against musl libc, making it usable even on the cheapest routers.

## Command Line Options
```bash
microsocks -1 -q -i listenip -p port -u user -P passw -b bindaddr -w wl
```

All arguments are optional. Default listenip is 0.0.0.0 and port is 1080.

- -q: Disables logging
- -b: Specifies which IP outgoing connections are bound to
- -w: Specifies a comma-separated whitelist of IP addresses for authentication-free access
  Example: -w 127.0.0.1,192.168.1.1,::1
- -1: Activates one-time authentication mode, useful for programs that don't support username/password auth

### One-time Authentication Example
Authenticate once using a supported program (like curl):
```bash
curl --socks5 user:password@listenip:port anyurl
```

After successful authentication, the IP is whitelisted and can use the proxy without further authentication.

## Supported SOCKS5 Features
- Authentication: None, Password, One-time
- Network Protocols: IPv4, IPv6, DNS
- Transport Protocol: TCP (UDP not supported yet)

## Troubleshooting
If you experience segfaults, try raising the `THREAD_STACK_SIZE` in sockssrv.c for your platform in steps of 4KB.

If this fixes your issue, please file a pull request.

MicroSocks uses the smallest safe thread stack size to minimize overall memory usage, but this may need adjustment for different platforms.