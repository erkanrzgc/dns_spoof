# DNS Spoofing Script

This Python script demonstrates DNS spoofing by intercepting DNS queries and modifying responses to redirect traffic to a specified IP address. It uses `netfilterqueue` to capture packets and `scapy` to manipulate and inject spoofed DNS responses.

## Disclaimer

This script is provided for **educational purposes only**. Unauthorized use of DNS spoofing to intercept or manipulate network traffic is illegal and unethical. Ensure you have explicit permission to test and demonstrate this script in a controlled and authorized environment.

**Use responsibly and at your own risk.**

---

## Requirements

This script requires the following:

- Python 3
- `scapy` library (can be installed via `pip install scapy`)
- `netfilterqueue` library (can be installed via `pip install netfilterqueue`)
- Administrator/root privileges
- Proper iptables configuration to redirect DNS traffic to the netfilter queue

---

## How It Works

1. **Packet Interception:** The script uses `netfilterqueue` to intercept packets using a Linux iptables rule.
2. **DNS Query Detection:** The `process_packet` function checks if the packet contains a DNS query (`DNSQR` layer).
3. **Spoofing:** If the query matches a specified domain (e.g., `www.vulnweb.com`), it modifies the DNS response (`DNSRR`) to redirect the target to a spoofed IP address.
4. **Packet Modification:** After spoofing, the script recalculates packet checksums and lengths before reinjecting the modified packet into the network.

---

## Usage

### 1. Set Up iptables Rules

Before running the script, set up iptables rules to redirect DNS traffic to the `netfilterqueue`:

```bash
sudo iptables -I FORWARD -j NFQUEUE --queue-num 0
```

For local testing:

```bash
sudo iptables -I OUTPUT -j NFQUEUE --queue-num 0
sudo iptables -I INPUT -j NFQUEUE --queue-num 0
```

### 2. Modify the Script

Replace `192.168.68.136` with the IP address you want to redirect traffic to:

```python
answer = scapy.DNSRR(rrname=qname, rdata="192.168.68.136")
```

### 3. Run the Script

Execute the script with administrator/root privileges:

```bash
sudo python3 dns_spoof.py
```

### 4. Stop iptables Rules

After testing, restore iptables rules to avoid unintended traffic manipulation:

```bash
sudo iptables --flush
```

---

## Example

If you want to redirect traffic for `www.vulnweb.com` to the IP `192.168.1.100`, update the script:

```python
if "www.vulnweb.com" in qname:
    answer = scapy.DNSRR(rrname=qname, rdata="192.168.1.100")
```

Run the script and ensure iptables rules are configured.

---

## Important Notes

- This script only works on Linux systems with iptables.
- Ensure you run the script in a testing environment where you have permission.
- The script does not perform ARP spoofing or other network manipulation; you may need additional setup to redirect DNS traffic in a switched network.
- Always clean up iptables rules after use to avoid network issues.

---

## License

This project is licensed under the MIT License. See the LICENSE file for more details.

