# Open Source OT / ICS Security
Credit: https://github.com/Fortiphyd/GRFICSv3

With a good dose of curiosity and the free resource, GRFICSv3, I decided to learn about OT / ICS systems and how an adversary might attack such infrastructure. GRFICSv3 is a FREE and open source OT security lab with realistic networking and a 3D process simulation for training and learning ICS security. GRFICSv3 consists of the following:

- **End-to-end OT / ICS security lab** — PLCs, HMIs, engineering workstations, routers, and attacker tools
- **3D process visualization** — watch tank levels and valves respond in real time
- **Virtual Walkthroughs** — explore the warehouse in first person, observing physical layouts and security lapses
- **Built-in attack & defense tools** — Kali Linux, MITRE Caldera, a custom firewall and Suricata IDS interface, and an optional Wazuh SIEM
- **Modular, containerized design** — launch everything with a single docker compose up
- **Realistic networking** — segmented process and enterprise zones with controllable traffic flow

---

### Network Diagram

[![GRFICSv3 Diagram](https://github.com/Fortiphyd/GRFICSv3/raw/main/images/diagram.png)](https://github.com/Fortiphyd/GRFICSv3)

---

### Core Containers & Access Topology

| Container | How to Access | Credentials | Description |
| --- | --- | --- | --- |
| **Simulation** | [http://localhost](http://localhost) | — | 3D chemical plant visualization |
| **Engineering Workstation** | [http://localhost:6080](http://localhost:6080) | — | HMI and PLC configuration |
| **Kali** | [http://localhost:6088](http://localhost:6088) | `kali : kali` | Attacker VM for exploitation and scanning |
| **Caldera** | [http://localhost:8888](http://localhost:8888) | `red : fortiphyd-red` | MITRE Caldera with OT plugin |
| **PLC (OpenPLC)** | [http://localhost:8080](http://localhost:8080) or [http://192.168.95.2:8080](http://192.168.95.2:8080) | `openplc : openplc` | Programmable logic controller |
| **HMI** | [http://localhost:6081](http://localhost:6081) or [http://192.168.90.107:8080](http://192.168.90.107:8080) | `admin : admin` | Operator interface |
| **Router / Firewall UI** | [http://192.168.90.200:5000](http://192.168.90.200:5000) or [http://192.168.95.200:5000](http://192.168.95.200:5000) | `admin : password` | View or modify firewall rules |
| **Wazuh SIEM** *(optional)* | [http://localhost:5601](http://localhost:5601) | `admin : admin` | SIEM dashboard — security events, alerts |

---

### Modbus Write Function Codes Reference

| Function Code (Hex) | Function Code (Decimal) | Name | Data Type | Operation Description |
| :---: | :---: | :--- | :--- | :--- |
| **`0x05`** | `05` | Write Single Coil | 1-bit (Boolean) | Force a single discrete output bit (ON or OFF) |
| **`0x06`** | `06` | Write Single Register | 16-bit Word | Change the value of one internal holding register |
| **`0x0F`** | `15` | Write Multiple Coils | 1-bit blocks | Forces a sequential block of contiguous bits ON or OFF |
| **`0x10`** | `16` | Write Multiple Registers | 16-bit blocks | Writes a sequential block of contiguous holding registers |
| **`0x16`** | `22` | Mask Write Register | Bit-level mask | Modifies specific bits inside a single register using an AND/OR mask |
| **`0x17`** | `23` | Read/Write Multiple Registers | 16-bit blocks | Executes a read loop and a write loop in a single instruction |

---

### Core Attack Vectors & Technical References

| Vector Type | Protocol Vulnerability | Tooling & Tactics | Reference Resources |
| :--- | :--- | :--- | :--- |
| **Command Injection** | Zero native authentication or client identity verification. | Sending unauthorized `FC06`/`FC10` requests over port 502 to alter device setpoints. | [Software Toolbox: Modbus Function Codes Guide](https://softwaretoolbox.com/blog/opc-modbus-function-codes) |
| **Man-in-the-Middle** | Lack of encryption allowing cleartext packet parsing and payload altering. | ARP spoofing to intercept traffic, modify register payloads inside `FC16` on-the-fly, and rewrite CRCs. | [SANS Institute: MitM Against Modbus TCP Illustrated with Wireshark](https://www.sans.org/white-papers/38095) |
| **Packet Layout Analysis** | Static header constraints making signature forgery simple. | Constructing raw Application Protocol (MBAP) headers for manual script execution. | [SANS Poster: Modbus RTU / TCP Packet Reference Structures](https://www.sans.org/posters/modbus-rtu-tcp) |
| **Replay & DoS Attacks** | No cryptographic sequence tokens or timestamp verification. | Sniffing working baselines, spamming malformed functions, or running diagnostic loops (`FC08`) to fault a PLC. | [Veridify Analysis: Modbus Security Failures and Mitigation Risks](https://www.veridify.com/article/modbus-security-issues-and-how-to-mitigate-cyber-risks/) |

---

### Conclusion / Takeaways

Exploring the GRFICSv3 cyber-physical simulation lab by Fortiphyd demonstrates a fundamental truth of operational technology: in a network running legacy Modbus TCP, an attacker does not need elegant zero-day exploits because the industrial protocol itself is the attack vector. Because Modbus completely lacks native authentication, encryption, or client verification, achieving authoritative control over a physical plant is reduced to a game of network positioning and structural register mapping—meaning anyone capable of routing an unauthenticated write command (`FC06` or `FC10`) to port `502` can directly cause kinetic damage on the facility. To truly understand how these legacy protocol vulnerabilities bridge the gap between digital command lines and real-world disaster without risking production downtime, I highly recommend cloning the Fortiphyd GRFICSv3 GitHub Repository to spin up your own containerized chemical plant and practice threat emulation safely.

