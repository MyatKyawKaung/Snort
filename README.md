<h2 align="center"> Snort Intrusion Detection Lab </h2>
SNORT is an open-source, rule-based Network Intrusion Detection and Prevention System (NIDS/NIPS). It has three main use models.

- Sniffer Mode - Read IP packets and prompt them in the console application.
- Packet Logger Mode - Log all IP packets (inbound and outbound) that visit the network.
- NIDS (Network Intrusion Detection System)  and NIPS (Network Intrusion Prevention System) Modes - Log/drop the packets that are deemed as malicious according to the user-defined rules.

In this demo, I will be using Snort3 as an IDS which logs the malicious traffics that are generated from my Kali attack machine, according to the predefined snort's rule.

This step-by-step guide will help you set up Snort3 using Ubuntu 22.04 server and Kali Linux for traffic generation and testing.

### Overview
*SNORT* consists of 4 main components.
1. **Sniffer**, Data Acquisition Software (DAC): Gathers the traffics from the interface and prepares them for further analysis.
2. **Preprocessors**: Prepare the data packets for inspection.
3. **Detection Engine**: Processes the rule against the packets.
4. **Output Modules**: Generate alerts information.



### Prerequisites
* **Ubuntu server**: This will host the Snort IDS. </br>
* **Kali Linux** : This will use to generate network traffic to test Snort.
* Ensure your Ubuntu server and Kali Linux are updated.
* Make sure you have administrative access to both systems.
