# Full Network Security Assessment

## 1. Executive Summary

This assessment was conducted against an authorized Kali Linux virtual machine in a controlled VirtualBox environment. The objective was to evaluate network exposure, observe network traffic, and identify web-security configuration issues using Nmap, Wireshark, and Nikto.

Nmap identified the assessment host as active, but no open TCP ports were found across all 65,535 TCP ports scanned. Wireshark was used to capture and analyze HTTP, DNS, ARP, and TCP traffic. The capture demonstrated that standard HTTP traffic can be inspected at the packet level because it is not protected by TLS encryption.

For the web-security portion, a temporary HTTP test server was created locally on 127.0.0.1:8000 and assessed with Nikto. Nikto reported missing X-Frame-Options and X-Content-Type-Options headers, as well as server-version and path-related observations. The reported wp-config.php result was not treated as a confirmed credential exposure because the test service was a minimal Python HTTP server and was not a WordPress installation.

## 2. Scope and Authorization

| Item | Details |
|---|---|
| Assessment target | Kali Linux virtual machine |
| Target IP | 192.168.29.184 |
| Network | 192.168.29.0/24 |
| Environment | Controlled VirtualBox/Kali Linux environment |
| Assessment period | 24–25 September 2026 |
| Nmap | Service/version detection, OS detection attempt, and full TCP-port scan |
| Wireshark | Network traffic capture and protocol analysis |
| Nikto | Local HTTP test server at 127.0.0.1:8000 |
| External systems | Excluded |
| Purpose | Educational security assessment |

All testing described in this report was performed against systems and services under the assessmenter's control.

## 3. Assessment Methodology

The assessment was performed in three phases.

### Phase 1 — Network Reconnaissance

Nmap was used to identify whether the host was reachable, detect services and versions, attempt operating-system identification, and scan all 65,535 TCP ports.

### Phase 2 — Network Traffic Analysis

Wireshark was used to capture network traffic for more than five minutes. The capture was analyzed for HTTP, DNS, ARP, TCP connection establishment, and unencrypted HTTP communication.

### Phase 3 — Web Security Assessment

A temporary Python HTTP server was created locally on 127.0.0.1:8000. Nikto was used to inspect the service for common web-server configuration and security issues.

## 4. Network Configuration

The Kali VM used the eth0 interface with the address 192.168.29.184/24.

Evidence: `screenshots/01_network_configuration.png`

## 5. Nmap Reconnaissance

### 5.1 Service and OS Detection

The following scan was performed:

```bash
sudo nmap -sV -O 192.168.29.184
```

The host was identified as up. Nmap reported that all 1,000 default TCP ports were closed and could not provide a specific OS fingerprint.

### 5.2 Full TCP Port Scan

A complete TCP scan was then performed:

```bash
sudo nmap -p- -sV 192.168.29.184
```

Results:
- Host status: Up
- TCP ports scanned: 65,535
- Open TCP ports: 0
- Closed TCP ports: 65,535
- Services detected: None

This indicates that no TCP services were exposed on the assessed Kali VM at the time of testing.

Evidence:
- `screenshots/02_nmap_full_tcp_scan.png`
- `nmap_default_scan.txt`
- `nmap_full_tcp_scan.txt`

## 6. Wireshark Traffic Analysis

A fresh capture was performed using the eth0 interface and retained as `wireshark_task10_capture.pcapng`.

### 6.1 HTTP

HTTP filtering revealed request and response traffic, including GET / HTTP/1.1 and HTTP/1.1 200 OK packets.

Evidence: `screenshots/03_http_analysis.png`

### 6.2 DNS

DNS filtering showed DNS queries and responses, including queries for example.com and both A and AAAA record activity.

Evidence: `screenshots/04_dns_analysis.png`

### 6.3 ARP

ARP filtering showed local address-resolution requests and responses, including requests for the local gateway and corresponding replies.

Evidence: `screenshots/05_arp_analysis.png`

### 6.4 TCP

TCP filtering showed connection establishment and termination. The captured traffic included the TCP three-way handshake: SYN, SYN-ACK, and ACK.

Evidence: `screenshots/06_tcp_analysis.png`

### 6.5 Unencrypted HTTP

An HTTP response was inspected in detail. The capture showed HTTP 200 OK, request URI `/`, full request URI `http://example.com/`, Content-Type: text/html, HTTP response headers, and file data.

Because HTTP is not protected by TLS, application-layer information can be inspected directly in captured packets.

No passwords, session tokens, or other sensitive credentials were observed in the captured traffic.

Evidence: `screenshots/07_unencrypted_http.png`

## 7. Nikto Web Assessment

A temporary local Python HTTP server was created for authorized testing:

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

The service was verified locally and then scanned using:

```bash
nikto -h http://127.0.0.1:8000
```

### 7.1 Missing X-Frame-Options

Nikto reported that the X-Frame-Options header was not present.

**Risk:** Lack of an anti-framing policy can increase exposure to clickjacking scenarios in applicable web applications.

**Recommendation:** Configure an appropriate framing policy using X-Frame-Options and/or a Content Security Policy frame-ancestors directive.

**Effort:** Easy

### 7.2 Missing X-Content-Type-Options

Nikto reported that X-Content-Type-Options was not set.

**Recommendation:** Configure:

```text
X-Content-Type-Options: nosniff
```

**Effort:** Easy

### 7.3 Server Version Observation

Nikto identified the service as SimpleHTTP/0.6 Python/3.13.2 and reported the SimpleHTTP server signature as outdated.

**Assessment note:** This was treated as a version/configuration observation rather than a confirmed exploitable vulnerability. Software should be maintained according to supported versions and deployment requirements.

**Effort:** Medium

### 7.4 wp-config.php Scanner Result

Nikto reported a wp-config.php-related result.

This was **not treated as a confirmed credential exposure** because the assessed service was a minimal Python HTTP server and was not a WordPress installation. Further validation would be required before classifying this as a real exposed file.

Evidence: `screenshots/08_nikto_scan.png`

## 8. Findings Register

| ID | Finding | Severity | Affected Asset | Recommended Fix |
|---|---|---|---|---|
| F-01 | Unencrypted HTTP communication | Medium | HTTP traffic observed during capture | Use HTTPS/TLS for web communication |
| F-02 | Missing X-Frame-Options | Low | Local HTTP test server | Configure X-Frame-Options and/or CSP frame-ancestors |
| F-03 | Missing X-Content-Type-Options | Low | Local HTTP test server | Add X-Content-Type-Options: nosniff |
| F-04 | Server-version/configuration observation | Low | Local Python HTTP test server | Maintain supported software versions and review server choice |
| O-01 | No exposed TCP services | Informational | 192.168.29.184 | Continue minimizing unnecessary network services |

## 9. Risk Assessment

### Medium

**F-01 — Unencrypted HTTP**

HTTP does not provide TLS confidentiality or integrity. Where sensitive information is transmitted, use of HTTPS is recommended.

### Low

**F-02 — Missing X-Frame-Options**

The absence of an anti-framing policy can increase clickjacking exposure where the application has functionality that could be abused through framing.

**F-03 — Missing X-Content-Type-Options**

Adding `nosniff` is a straightforward web-security hardening measure.

**F-04 — Server-version/configuration observation**

The Nikto result warrants maintenance review, but the scan alone does not establish a specific exploitable vulnerability.

### Informational

**O-01 — No exposed TCP services**

The full TCP scan found no open TCP services on the assessed host, which limits the TCP services available for remote interaction.

## 10. Remediation Roadmap

| Priority | Recommendation | Effort |
|---|---|---|
| High | Replace HTTP with HTTPS/TLS for applicable web services | Medium |
| Medium | Configure anti-framing protection | Easy |
| Medium | Add X-Content-Type-Options: nosniff | Easy |
| Low | Review and minimize unnecessary network services | Easy |
| Low | Maintain supported server/software versions | Medium |

## 11. Conclusion

The assessment combined network reconnaissance, packet analysis, and web-server security testing within a controlled Kali Linux environment.

The Nmap assessment found no open TCP ports on the target VM. Wireshark demonstrated practical analysis of HTTP, DNS, ARP, and TCP protocols and showed how unencrypted HTTP communication can be inspected at the packet level. Nikto identified web-security hardening opportunities on the temporary local HTTP server.

The assessment demonstrates the value of combining multiple security tools with evidence-based analysis. Recommended remediation actions focus on encrypted web communication, security-header hardening, software maintenance, and continued reduction of unnecessary network exposure.

## 12. Evidence

### Screenshots

1. `screenshots/01_network_configuration.png`
2. `screenshots/02_nmap_full_tcp_scan.png`
3. `screenshots/03_http_analysis.png`
4. `screenshots/04_dns_analysis.png`
5. `screenshots/05_arp_analysis.png`
6. `screenshots/06_tcp_analysis.png`
7. `screenshots/07_unencrypted_http.png`
8. `screenshots/08_nikto_scan.png`

### Supporting Files

- `nmap_default_scan.txt`
- `nmap_full_tcp_scan.txt`
- `wireshark_task10_capture.pcapng`
