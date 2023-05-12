# Fastweb FastGate *'cmproxy'* buffer overflow (CVE-2022-30114)

## Introduction
This script is a Proof of Concept (PoC) of CVE-2022-30114 and causes a reboot of **[Fastweb FastGate](https://www.fastweb.it/myfastweb/assistenza/guide/FASTGate/)** home routers, both GPON and VDSL2 version.

CVE-2022-30114 is a ***heap-based* buffer overflow** in the *'cmproxy'* executable, a program which handles HTTP requests through a Lighttpd FastCGI webserver listening on TCP port 8888.  A specially crafted HTTP request allows a remote attacked to crash the executable and reboot the device, causing a Denial of Service.

### Affected devices
* **Technicolor MediaAccess FGA2130FWB** (GPON) - Version 18.3.n.0482_FW_233_FGA2130 and below 
* **Technicolor MediaAccess DGA4131FWB** (VDSL2) - Version 18.3.n.0482_FW_264_DGA4131 and below


## Vulnerabilty 
The devices are vulnerable to a *heap-based* buffer overflow, caused by the lack of validation of the length of the '*Authorization*' HTTP header value on the web service exposed on **TCP port 8888**. The service is exposed on both the WAN and the LAN interfaces of the device[^1].

[^1]: WAN access was disabled as a compensative control after the first disclosure to Fastweb. As of writing, the service is still exposed on the internal LAN.

A remote, unauthenticated attacker, sending a string longer than 100 bytes in the '*Authorization*' HTTP header, causes an overflow in a pre-allocated buffer in the *'.bss'* memory section of an executable file called *'cmproxy'* which handles HTTP requests sent on the mentioned service above via FastCGI protocol.

This allows to overwrite the process's heap memory, causing the process to become corrupted and crash on the first memory allocation. It's worth noting that the C library version used (GNU C Library - glibc v2.24) contains protection measures to detect heap corruption but it is not excluded, however, that by deepening the analysis it would be possible to overwrite heap structures and achieve code execution.

See [Blog Post](https://str0ng4le.github.io) for details.
