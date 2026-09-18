# Task 8 — Capture Network Traffic with Wireshark

## Objective

The objective of this task is to capture and analyze network traffic using Wireshark. The activity focuses on understanding common network protocols such as HTTP, DNS, TCP, and ARP, analyzing the TCP three-way handshake, and identifying security risks associated with unencrypted HTTP traffic.

## Tools and Environment

- **Operating System:** Kali Linux
- **Virtualization:** VirtualBox
- **Network Interface:** eth0
- **Tool:** Wireshark
- **Capture Duration:** More than 5 minutes
- **Capture File:** `wireshark_capture.pcapng`

All traffic was captured from the Kali Linux virtual machine in a controlled environment.

## Network Traffic Capture

Wireshark was started on the active `eth0` network interface.

During the capture, controlled network activity was generated using:

- `ping` — to generate ICMP traffic
- `curl` — to generate HTTP traffic
- `nslookup` — to generate DNS traffic

The capture was kept running for more than five minutes and was then saved as:

`wireshark_capture.pcapng`

## Traffic Analysis

### 1. HTTP Traffic

The `http` display filter was used to identify HTTP packets.

The capture contained an HTTP request and response, including:

- `GET / HTTP/1.1`
- `HTTP/1.1 200 OK`
- `Content-Type: text/html`
- `Server: cloudflare`
- Full request URI: `http://example.com/`

This demonstrates that HTTP traffic can expose request information and response data because HTTP does not provide the encryption provided by HTTPS.

**Evidence:** `screenshots/01_http_traffic.png`

---

### 2. DNS Traffic

The `dns` display filter was used to identify DNS packets.

DNS traffic was observed while resolving a domain name. DNS packets allow a system to resolve domain names into IP addresses so that network connections can be established.

**Evidence:** `screenshots/02_dns_traffic.png`

---

### 3. TCP Three-Way Handshake

The `tcp` display filter was used to analyze TCP communication.

A complete TCP three-way handshake was observed:

1. **SYN** — The client requests to establish a TCP connection.
2. **SYN-ACK** — The server acknowledges the request and responds.
3. **ACK** — The client acknowledges the server's response.

Example packets observed:

- `192.168.29.183 → 172.66.147.243 [SYN]`
- `172.66.147.243 → 192.168.29.183 [SYN, ACK]`
- `192.168.29.183 → 172.66.147.243 [ACK]`

This process establishes the TCP connection before data transmission.

**Evidence:** `screenshots/03_tcp_handshake.png`

---

### 4. ARP Traffic

The `arp` display filter was used to identify Address Resolution Protocol traffic.

ARP is used on IPv4 local networks to determine the MAC address associated with an IP address.

The capture showed ARP request and response packets, including queries such as:

- "Who has [IP address]?"
- A response identifying the corresponding MAC address.

**Evidence:** `screenshots/04_arp_traffic.png`

---

### 5. Unencrypted HTTP Data

An HTTP response packet was inspected in detail.

The packet contained HTTP information including:

- HTTP status: `200 OK`
- Content type: `text/html`
- Server information
- Request URI: `http://example.com/`
- File data associated with the HTTP response

Because HTTP traffic is not encrypted, information transmitted through HTTP can potentially be inspected by an attacker who is able to capture the traffic.

**Evidence:** `screenshots/05_unencrypted_http.png`

## HTTP vs HTTPS

| Feature | HTTP | HTTPS |
|---|---|---|
| Encryption | No | Yes |
| Data confidentiality | Not provided by HTTP itself | Provided through TLS |
| Traffic inspection | Content can be readable when captured | Application data is encrypted |
| Common use | Legacy/non-sensitive communication | Secure web communication |

HTTPS should be used instead of HTTP when transmitting sensitive information such as credentials or personal data.

## Security Observations

The traffic analysis demonstrated several important networking and security concepts:

- Network packets contain information about communication between systems.
- DNS traffic reveals domain-resolution activity.
- TCP uses a three-way handshake to establish connections.
- ARP is used for IP-to-MAC address resolution on local IPv4 networks.
- HTTP does not encrypt application data.
- HTTPS uses encryption to protect application-layer communication.

## Glossary

| Term | Meaning |
|---|---|
| **Wireshark** | A network protocol analyzer used to capture and inspect network packets. |
| **Packet** | A unit of data transmitted across a network. |
| **HTTP** | Hypertext Transfer Protocol used for web communication. |
| **HTTPS** | HTTP secured using TLS encryption. |
| **DNS** | Domain Name System used to resolve domain names to IP addresses. |
| **TCP** | Transmission Control Protocol that provides reliable connection-oriented communication. |
| **ARP** | Address Resolution Protocol used to map IPv4 addresses to MAC addresses on a local network. |
| **SYN** | TCP flag used to initiate a connection. |
| **ACK** | TCP flag used to acknowledge received communication. |
| **MAC Address** | A hardware/network interface address used for local network communication. |
| **TLS** | Transport Layer Security protocol used to provide encrypted communication. |

## Ethical Testing Scope

This activity was performed for educational purposes in a controlled environment using a Kali Linux virtual machine.

Only traffic generated and observed from the controlled testing environment was analyzed. No public, university, or unauthorized networks were intentionally monitored or captured.

Network traffic analysis should only be performed on systems and networks for which appropriate authorization has been obtained.

## Conclusion

This task provided practical experience with Wireshark and network traffic analysis. HTTP, DNS, TCP, and ARP traffic were captured and examined using Wireshark display filters. The TCP three-way handshake was analyzed, and the security implications of unencrypted HTTP communication were demonstrated.

The captured traffic and screenshots provide evidence of the analysis performed during the task.