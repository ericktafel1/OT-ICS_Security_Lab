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

### Key Attack Vector Breakdowns

Exploring this lab environment highlights exactly how legacy industrial design flaws translate into catastrophic real-world failures. The attack lifecycle generally follows three distinct stages:

1. **OT Reconnaissance & Register Mapping**
Because Modbus TCP lacks inherent authentication, an attacker can gain a foothold in the OT subnet and use tools like `mbtget` or `pymodbus` to sweep the entire address table. Then an adversary would decode the **UINT16 scaling** of the process data. By taking passive Wireshark captures of HMI traffic or pulling configuration scripts directly from an exposed Engineering Workstation (EWS), an attacker can map human-readable labels (like `pressure_setpoint` or `run_bit`) directly to specific Modbus holding registers (e.g., `HR 1026`).

2. **Process Manipulation (The "Insecure by Design" Flaw)**
Once the register map is known, an attacker can mirror historically devastating ICS incidents using pure protocol functionality rather than malware:
* **The Setpoint Slam (Oldsmar Style):** Forcing a high-value write to a memory word (such as jumping a chemical composition setting past normal tolerance) and allowing the PLC's native logic to drive the plant to an unstable state.
* **The Run-Bit Kill (FrostyGoop Style):** Commanding a brute-force shutdown by toggling the PLC runtime control bit, instantly disabling core safety automation and cutting off critical utility output.

3. **Visual & Operational Destruction**
By maintaining malicious register states or manipulating sensor feedback loops (preventing the HMI from seeing real pressure rises), the physical system safety margins quickly erode. In the 3D process simulation, a sustained high-pressure hold eventually causes a visual reactor vessel rupture, proving that unauthenticated network access to lower-level controllers results in absolute command over physical consequences.

---

### Conclusion / Takeaways

* **The Fallacy of Air-Gaps:** Modern critical infrastructure relies heavily on multi-tier interconnectivity. As shown by the GRFICS architecture, a compromised corporate network or an exposed interface on a DMZ router serves as a direct pivot point into lower-level operational subnets.
* **Protocol Vulnerabilities Over CVEs:** In industrial control security, an attacker rarely needs an unpatched software exploit. Because legacy industrial automation protocols like Modbus TCP possess zero native cryptographic verification, encryption, or identity validation, any device capable of routing a packet to port `502` can exercise complete authoritative control over the physical process.
* **The Imperative for Network Segmentation & Monitoring:** Defense in depth cannot rely on endpoint antivirus inside an OT network. Mitigating these attack vectors requires rigorous zoning via active firewalls, disabling unused communication directions, and leveraging specialized intrusion detection engines (like Suricata or industrial SIEM configurations) to alert on uncharacteristic Modbus write codes (`FC6`, `FC16`) coming from rogue host addresses.

