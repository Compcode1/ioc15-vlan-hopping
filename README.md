This case study examines VLAN hopping—a network segmentation bypass technique where an attacker crafts specially tagged Ethernet frames to escape their assigned VLAN and access restricted segments. The attack exploits trust assumptions in Layer 2 switch configurations, especially mismanaged trunk ports and native VLAN behaviors.

 **Overview**

The attacker begins from within a trusted VLAN, using a compromised host to inject double-tagged 802.1Q packets. These packets are designed to manipulate how switches interpret VLAN tags. If misconfigurations exist (such as native VLAN overlaps or unfiltered trunk ports), the outer VLAN tag is stripped and the inner tag is forwarded, causing the frame to be delivered into a VLAN the attacker should never reach. This undermines logical segmentation and creates lateral movement opportunities.

## Key Concepts Covered

- How 802.1Q VLAN tagging works and why double-tagging can be dangerous
- The role of trunk ports and native VLAN configuration in switch behavior
- Host-based forensic triage for VLAN manipulation from inside a compromised system
- Network detection strategies for identifying crafted VLAN tag abuse
- Cross-layer pivot logic from host execution to network-level exploitation

## Investigative Structure

This IOC case study follows a structured triage methodology:

- **Primary Triage Protocol**: Host-Based IOC Triage – due to crafted frame generation from an attacker-controlled system
- **Canonical Investigative Tools**:
  - **Windows Event Logs** – interface and authentication anomalies
  - **EDR Telemetry** – detection of tools like Scapy, Yersinia, or raw NIC usage
  - **File System/Registry Inspection** – driver-level or tool-based persistence
  - **Volatile Memory Analysis** – extraction of double-tag payload buffers or crafted frame evidence
- **Operating System Layers Involved**:
  - Layer 1: Execution of packet tools
  - Layer 2: Startup or persistence mechanisms
  - Layer 6: Low-level network manipulation
- **OSI Layers Impacted**:
  - Layer 2: Direct VLAN tag abuse
  - Layer 3: Resulting cross-VLAN traffic redirection
  - Layer 1: Physical NIC misused for broadcast injection

## Tools & Techniques Observed

- **802.1Q double-tagged packet crafting**
- **Scapy** or **Yersinia** (custom packet creation frameworks)
- **Misuse of trunk ports on switches that fail to validate inner VLAN tags or enforce VLAN boundaries based on ingress port.**
- **Use of raw socket access or NIC driver modification** for Ethernet frame control

Raw socket access – enables direct Ethernet frame crafting outside of the standard TCP/IP stack, used to insert VLAN tags manually for double-tagging.

NIC driver modification or abuse – alters how the network interface handles traffic, potentially bypassing OS-level restrictions and enabling unauthorized frame injection at Layer 2.



## Defender Response Framework

- Isolate the suspected host and extract forensic evidence from volatile and persistent layers
- Review and correct switch trunk port configurations
- Ensure native VLANs are not shared or open to unauthorized interfaces
- Enable VLAN tag validation and ACLs to reject double-tagged frames
- Deploy NetFlow or packet capture systems tuned to detect 802.1Q anomalies

## Strategic Takeaways

VLAN hopping is not an initial compromise—it’s a **post-compromise pivot tactic** used by attackers to move laterally within a segmented environment. It bypasses network logic through **crafted deception at Layer 2**, exploiting trust relationships between switch ports and the traffic they forward. Understanding how attackers manipulate switch behavior using host-controlled NICs is key to detecting and stopping such lateral expansions before they escalate into privilege abuse or data access.

## Files

- `ioc15-vlan-hopping.ipynb`: Full case study with detailed narrative, technical analysis, and layered triage methodology

#


