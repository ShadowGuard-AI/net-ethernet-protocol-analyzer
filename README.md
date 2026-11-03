![Banner](banner.png)

# net-ethernet-protocol-analyzer
Identifies the Ethernet protocol types used in network traffic (IPv4, IPv6, ARP, and anything else as Unknown) and prints statistics on their usage. Focused on Basic network operations.

## Install
```bash
git clone https://github.com/ShadowGuard-AI/net-ethernet-protocol-analyzer.git
cd net-ethernet-protocol-analyzer
```

Python 3, standard library only. There is nothing to install. Capturing packets needs the usual raw-socket privileges; without them the run logs a socket error and exits 1.

## Usage
```bash
python3 main.py [-i INTERFACE] [-n NUM_PACKETS] [-p] [-l LOG_FILE]
```

## Parameters
- `-i`, `--interface`: Network interface to capture traffic from. Default: `eth0`.
- `-n`, `--num_packets`: Number of packets to capture. Default: 100.
- `-p`, `--promiscuous`: Enable promiscuous mode.
- `-l`, `--log_file`: Specify the log file path. Optional.
- `-h`, `--help`: Show help message and exit.

## Limits, as the code states them
- Only the outer EtherType is read, so VLAN-tagged frames land in Unknown: the tool never steps past an 802.1Q tag.
- A log file handler is added at DEBUG level, but the root logger stays at INFO, so per-packet debug lines are filtered out before they can reach the file.

## Fix in Wave 12
The EtherType was passed through `socket.ntohs` after `struct.unpack` with `!` had already converted it to host order, so on a little-endian host every known protocol counted as Unknown. Verified before the fix with crafted frames: IPv4, IPv6, and ARP all counted as Unknown. After removing the extra swap, the same frames count as IPv4, IPv6, and ARP, with an unrecognised EtherType still counting as Unknown. A live capture run here is blocked by raw-socket privileges, and privilege escalation is forbidden in this wave, so no live capture is claimed.

## License
MIT. See [LICENSE](LICENSE).
