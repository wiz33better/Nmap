# nmap-cheatsheet

### 🛠️ General Flags

| **Command**                               | **Description**                                                                                                                                                                              |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `nmap -sn [target]`                       | Ping scan only — disable port scan, discover live hosts                                                                                                                                      |
| `nmap -v [target]`                        | Verbose output — show all hosts and ports as found                                                                                                                                           |
| `nmap -vv [target]`                       | Extra verbose — more detail on scan progress                                                                                                                                                 |
| `nmap -sV [target]`                       | Service/version detection — probe open ports for running software                                                                                                                            |
| `nmap -sV --version-intensity 9 [target]` | added Maximum version detection intensity (0–9, default 7)                                                                                                                                   |
| `nmap -O [target]`                        | OS detection — requires at least one open and one closed port                                                                                                                                |
| `nmap -A [target]`                        | fixed Aggressive scan: OS detection (`-O`), version (`-sV`), scripts (`-sC`), traceroute. Use only with permission. Was incorrectly labelled 'ACK flag' in original — `-A` means aggressive. |
| `nmap -p 80,443 [target]`                 | Scan specific ports; use `-p-` to scan all 65535 ports                                                                                                                                       |
| `nmap -p 1-1024 [target]`                 | Scan a port range                                                                                                                                                                            |
| `nmap --open [target]`                    | added Show only open ports in results                                                                                                                                                        |
| `nmap -T0 … -T5 [target]`                 | Timing template: T0=paranoid (IDS evasion), T3=default, T4=fast, T5=insane                                                                                                                   |
| `nmap -n [target]`                        | added Skip DNS resolution — speeds up scans significantly                                                                                                                                    |
| `nmap -Pn [target]`                       | added Skip host discovery — treat all hosts as online                                                                                                                                        |

### 🌐 Host Discovery

| **Command**             | **Description**                                                                                                 |
| ----------------------- | --------------------------------------------------------------------------------------------------------------- |
| `nmap -sn -PR [target]` | ARP ping scan — most reliable on local LAN (layer 2)                                                            |
| `nmap -sn -PU [target]` | UDP ping scan — active host returns UDP response or ICMP error                                                  |
| `nmap -sn -PE [target]` | ICMP echo ping — useful to check if ICMP passes through a firewall                                              |
| `nmap -sn -PP [target]` | ICMP timestamp ping — alternative when echo requests are blocked                                                |
| `nmap -sn -PM [target]` | ICMP address mask ping — another fallback to ICMP echo                                                          |
| `nmap -sn -PA [target]` | TCP ACK ping — empty ACK packet; RST reply means host is up                                                     |
| `nmap -sn -PS [target]` | added TCP SYN ping — SYN packet; SYN/ACK or RST reply means host is up                                          |
| `nmap -sn -PO [target]` | IP protocol ping — sends probes of multiple IP protocols; any reply = alive                                     |
| `nmap -sP [target]`     | fixed Ping sweep (deprecated alias for `-sn`, avoid in new scripts) Deprecated since Nmap 7. Use `-sn` instead. |

### 🔍 Port Scan Types

| **Command**                  | **Description**                                                                                |
| ---------------------------- | ---------------------------------------------------------------------------------------------- |
| `nmap -sT [target]`          | TCP connect scan — full three-way handshake; logged by target, no root needed                  |
| `nmap -sS [target]`          | SYN/stealth scan — half-open; faster, less likely to be logged; requires root                  |
| `nmap -sU [target]`          | UDP scan — no handshake; open port = no response, closed = ICMP unreachable                    |
| `nmap -sF [target]`          | added FIN scan — sends FIN flag; closed port replies with RST, open port is silent             |
| `nmap -sN [target]`          | added Null scan — no flags set; same response logic as FIN scan                                |
| `nmap -sX [target]`          | Xmas scan — FIN, URG, PUSH flags set; silent = open, RST = closed                              |
| `nmap -sM [target]`          | Maimon scan — FIN/ACK probe; no response = open\|filtered, RST = closed                        |
| `nmap -sA [target]`          | ACK scan — maps firewall rules; no response = filtered (stateful FW present), RST = unfiltered |
| `nmap -sI [zombie] [target]` | Idle/zombie scan — uses a third-party idle host to spoof source address                        |
| `nmap -sZ [target]`          | SCTP COOKIE ECHO scan — no response = open, ABORT = closed                                     |
| `nmap -sY [target]`          | added SCTP INIT scan — equivalent of SYN scan for SCTP protocol                                |

### 💾 Output Formats

| **Command**                      | **Description**                                           |
| -------------------------------- | --------------------------------------------------------- |
| `nmap -oN output.txt [target]`   | added Normal human-readable output to file                |
| `nmap -oX output.xml [target]`   | XML output — machine-parseable, ideal for post-processing |
| `nmap -oG output.gnmap [target]` | added Grepable output — easy to parse with grep/awk       |
| `nmap -oA basename [target]`     | added Output in all three formats simultaneously          |

### 📜 Scripts & Enumeration (NSE)

| **Command**                                                                    | **Description**                                                                                                                         |
| ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| `nmap -sC [target]`                                                            | added Run default NSE scripts (equivalent to `--script=default`)                                                                        |
| `nmap --script smb-os-discovery.nse [target]`                                  | SMB OS discovery — detects OS, hostname, domain, workgroup, time                                                                        |
| `nmap -sV --script nbstat.nse [target]`                                        | NetBIOS enumeration — names, MAC address, workgroup                                                                                     |
| `nmap -sV --script http-enum [target]`                                         | HTTP enumeration — finds apps, directories, and files on a web server                                                                   |
| `nmap --script http-trace -d [target]`                                         | HTTP TRACE — checks if the TRACE method is enabled (XST risk)                                                                           |
| `nmap -p80 --script http-frontpage-login [target]`                             | fixed FrontPage login check — tests for exposed FrontPage auth. Original had a stray space in `--script =` (invalid syntax).            |
| `nmap --script http-passwd --script-args http-passwd.root=/ [target]`          | fixed HTTP passwd — tries to retrieve `/etc/passwd` via traversal. Original had a stray space before `/`.                               |
| `nmap -p80 --script http-waf-detect [target]`                                  | WAF detection — checks if a web application firewall is present                                                                         |
| `nmap --script hostmap-bfk --script-args hostmap-bfk.prefix=hostmap- [target]` | fixed Hostmap BFK — discovers virtual hosts sharing the IP. Original had `-script-args` (single dash). Correct form is `--script-args`. |
| `nmap --script=sniffer-detect [target]`                                        | Promiscuous/sniffer detection — identifies NICs in promiscuous mode                                                                     |

### 🛡️ Firewall & IDS Evasion

| **Command**                          | **Description**                                                                                                                                |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `nmap -f [target]`                   | Fragment packets — splits probe packets so reassembly evades filters                                                                           |
| `nmap -g 80 [target]`                | Source port manipulation — spoofs source port to trusted ports like 80 (HTTP)                                                                  |
| `nmap --mtu 8 [target]`              | fixed Custom MTU — packet size must be multiple of 8; evades detection/filtering. Original used `-mtu` (single dash). Correct flag is `--mtu`. |
| `nmap -D RND:10 [target]`            | Decoy scan — generates 10 random decoy IPs; real IP hidden among them                                                                          |
| `nmap --data 0xdeadbeef [target]`    | Binary payload — appends raw binary data to packets                                                                                            |
| `nmap --data-string 'text' [target]` | String payload — appends an ASCII string to packets                                                                                            |
| `nmap --data-length 5 [target]`      | Random padding — adds N random bytes to most packets                                                                                           |
| `nmap --randomize-hosts [target]`    | Random host order — scans targets in random sequence to avoid pattern detection                                                                |
| `nmap --badsum [target]`             | Bad checksums — sends packets with invalid TCP/UDP checksums; useful for testing stateful FWs                                                  |

### ⚠️ Vulnerability Scanning

| **Command**                                                            | **Description**                                                          |
| ---------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| `nmap -6 -n -Pn -sSU -pT:0-65535,U:0-65535 -v -A -oX out.xml [target]` | Full IPv6 scan — all TCP and UDP ports, OS/version detection, XML output |
| `nmap -n -Pn -sSU -pT:0-65535,U:0-65535 -v -A -oX out.xml [target]`    | Full dual TCP+UDP scan across all 65535 ports with detailed output       |
| `nmap -n -Pn -sS -pT:0-65535 -v -A -oX out.xml [target]`               | Full TCP SYN scan across all ports with OS/version detection             |
| `nmap --script vuln [target]`                                          | added Run all vulnerability-detection NSE scripts against target         |

### ⚙️ ICS / SCADA Systems

| **Command**                                                            | **Description**                                                                                                                                                                                                                                          |
| ---------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `nmap -Pn -sT -p 46824 [target]`                                       | HMI discovery — port used by common HMI systems                                                                                                                                                                                                          |
| `nmap -Pn -sT --scan-delay 1s --max-parallelism 1 -p [ports] [target]` | fixed Safe ICS scan — slow, serialized probes to avoid disrupting industrial equipment. Original used non-existent `--mac-parallelism`. Correct flag is `--max-parallelism`. Also: `--scan-delay` takes a time value (e.g. 1s, 500ms), not '1d' (1 day). |
| `nmap -Pn -sT -p 102 --script s7-info [target]`                        | Siemens S7 PLCs — Modbus port 102, retrieves device info                                                                                                                                                                                                 |
| `nmap -Pn -sU -p 44818 --script enip-info [target]`                    | EtherNet/IP devices — common in Allen-Bradley/Rockwell PLCs                                                                                                                                                                                              |
| `nmap -Pn -sT -p 502 --script modbus-discover [target]`                | Modbus devices — widely used industrial protocol                                                                                                                                                                                                         |
| `nmap -Pn -sU -p 47808 --script bacnet-info [target]`                  | BACnet devices — building automation and control networks                                                                                                                                                                                                |
| `nmap -Pn -sT -p 1911,4911 --script fox-info [target]`                 | Niagara Fox devices — used in HVAC and building management                                                                                                                                                                                               |
| `nmap -Pn -sT -p 9600 --script omron-info [target]`                    | Omron PLC devices — Japanese industrial automation controllers                                                                                                                                                                                           |
| `nmap -Pn -sT -p 1962 --script pcworx-info [target]`                   | PCWorx devices — Phoenix Contact ICS controllers                                                                                                                                                                                                         |
| `nmap -Pn -sT -p 20547 --script proconos-info [target]`                | ProConOS devices — KW-Software PLC runtime environment                                                                                                                                                                                                   |
