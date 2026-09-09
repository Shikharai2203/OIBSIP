# Task 1 — Basic Network Scanning with Nmap

## Objective

The objective of this project is to perform basic network reconnaissance using Nmap and identify open ports, running services, service versions, and the operating system of a local test machine.

The scanning was performed against `127.0.0.1` (localhost), which refers to the Kali Linux virtual machine itself.

## What is Nmap?

Nmap (Network Mapper) is an open-source network scanning and security auditing tool. It can be used to discover hosts, identify open ports, detect services and their versions, and perform operating-system detection.

Network scanning is useful for security professionals because it helps identify the attack surface of a system and can reveal services that may require security review.

## Lab Environment

- Operating System: Kali Linux
- Nmap Version: 7.95
- Target: `127.0.0.1` (localhost)
- Target Type: Local virtual machine
- Web service used for testing: Python SimpleHTTPServer
- Detected Python Version: 3.13.2

A temporary Python HTTP server was created on port 8000 for the purpose of demonstrating Nmap's ability to identify an open service.

## Scanning Methodology

### 1. Basic Scan

Command:

```bash
nmap 127.0.0.1
```
The basic scan identified port `8000/tcp` as open.

Result:

```text
8000/tcp open http-alt
```

### 2. Service and Version Detection

Command:

```bash
nmap -sV 127.0.0.1
```
The `-sV` option enables service and version detection. It attempts to determine which service is running on an open port and identify the software and version.

The scan identified the following service:

```text
8000/tcp open http SimpleHTTPServer 0.6 (Python 3.13.2)
```
This means that TCP port 8000 was open and an HTTP service was detected. Nmap identified the service as SimpleHTTPServer 0.6 running with Python 3.13.2.


### 3. Operating System Detection

Command:

```bash
sudo nmap -O 127.0.0.1
```
The `-O` option enables operating-system detection. Nmap attempts to identify the operating system by analyzing characteristics of the target's network responses.

The scan reported:

```text
Device type: general purpose
Running: Linux 2.6.X|5.X
OS details: Linux 2.6.32, Linux 5.0 - 6.2
Network Distance: 0 hops
```

Nmap identified the target as a general-purpose Linux system. The OS details are an estimate produced by Nmap's OS fingerprinting and should not be treated as an exact kernel-version identification.

## Findings

| Port | State | Service | Version |
|------|-------|---------|---------|
| 8000/tcp | Open | HTTP | SimpleHTTPServer 0.6(Python 3.  13.2) |

### Security Considerations

An open port is not automatically a vulnerability. It indicates that a service is listening and accepting connections.

In this lab, port 8000 was intentionally opened by starting a temporary Python HTTP server. The server was bound to `127.0.0.1`, limiting access to the local machine.

However, an HTTP service should be reviewed if exposed unnecessarily. Security considerations include:

- Whether the service is required.
- Whether the service is properly configured.
- Whether sensitive files or information are exposed.
- Whether the service is maintained and up to date.
- Whether the service should be accessible only locally or to a wider network.

## Ethical Considerations

Network scanning should only be performed against systems that you own or have explicit authorization to test.

This project was performed entirely on a controlled local Kali Linux virtual machine using `127.0.0.1`.

Scanning systems or networks without permission may be unauthorized and potentially illegal.

## Evidence

Screenshots documenting the scans are available in the `screenshots` directory:

- `01_basic_nmap_scan.png`
- `02_service_version_detection.png`
- `03_os_detection.png`

The complete Nmap output is available in `nmap_scan_results.txt`.

## Conclusion

Nmap was used to perform basic reconnaissance of a local test system. The scans identified an open HTTP service on TCP port 8000, detected the Python SimpleHTTPServer service and Python version, and performed operating-system fingerprinting.

The exercise demonstrated how network scanning can help security analysts identify exposed services and gather information that can be used for further security assessment.

The exercise demonstrated how network scanning can help security analysts identify exposed services and gather information that can be used for further security assessment.