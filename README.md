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

### Core Containers & Access Points| Container | How to Access | Credentials | Description |
| --- | --- | --- | --- |
| **Simulation** | [http://localhost](http://localhost) | — | 3D chemical plant visualization |
| **Engineering Workstation** | [http://localhost:6080](http://localhost:6080) | — | HMI and PLC configuration |
| **Kali** | [http://localhost:6088](http://localhost:6088) | `kali : kali` | Attacker VM for exploitation and scanning |
| **Caldera** | [http://localhost:8888](http://localhost:8888) | `red : fortiphyd-red` | MITRE Caldera with OT plugin |
| **PLC (OpenPLC)** | [http://localhost:8080](http://localhost:8080) or `192.168.95.2:8080` | `openplc : openplc` | Programmable logic controller |
| **HMI** | [http://localhost:6081](http://localhost:6081) or `192.168.90.107:8080` | `admin : admin` | Operator interface |
| **Router / Firewall UI** | `192.168.90.200:5000` or `192.168.95.200:5000` | `admin : password` | View or modify firewall rules |
| **Wazuh SIEM** *(optional)* | [http://localhost:5601](http://localhost:5601) | `admin : admin` | SIEM dashboard — security events, alerts |

---

After following the setup from the [github](https://github.com/Fortiphyd/GRFICSv3), I began by enumerating the network:

```bash
nmap -sS -p- --open -Pn 192.168.95.0/24
```




---

### Conclusion / Takeaways

