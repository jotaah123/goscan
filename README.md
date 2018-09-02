# GoScan

**GoScan** is an interactive network scanner client, featuring auto-completion, which provides abstraction and automation over nmap.

Although it started as a small side-project I developed in order to learn [@golang](https://twitter.com/golang), GoScan can now be used to perform host discovery, port scanning, and service enumeration not only in situations where being stealthy is not a priority and time is limited (think at CTFs, OSCP, exams, etc.), but also (with a few tweaks in its configuration) during professional engagements.

GoScan is also particularly suited for unstable environments (think unreliable network connectivity, lack of "`screen`", etc.), given that it fires scans and maintain their state in an SQLite database. Scans runs in the background (detached from the main thread), so even if connection to the box running GoScan is lost, results can be uploaded from one of the `load` commands (more on this below). That is, data can be imported into GoScan at different stages of the process, without the need to restart the entire process from scratch if something goes wrong.


![demo](https://raw.githubusercontent.com/marco-lancini/goscan/master/.github/demo.gif)



# Installation

#### Binary installation (Recommended)

Binaries are available from the [Release](https://github.com/marco-lancini/goscan/releases) page.

```bash
# macOS (darwin)
$ wget https://github.com/marco-lancini/goscan/releases/download/v1.5/goscan_1.5_darwin_amd64.zip
$ unzip goscan_1.5_darwin_amd64.zip

# Linux
$ wget https://github.com/marco-lancini/goscan/releases/download/v1.5/goscan_1.5_linux_amd64.zip
$ unzip goscan_1.5_linux_amd64.zip

# After that, place the executable in your PATH
$ chmod +x goscan
$ sudo mv ./goscan /usr/local/bin/goscan
```

#### Build from source

```bash
$ git clone https://github.com/marco-lancini/goscan.git
$ cd goscan/goscan/
$ make setup
$ make build
```

To create a multi-platform binary, use the cross command via make:

```bash
$ make cross
```


#### Docker

```bash
$ git clone https://github.com/marco-lancini/goscan.git
$ cd goscan/
$ docker-compose up --build
```



# Usage

GoScan supports all the main steps of network enumeration:

![process](https://raw.githubusercontent.com/marco-lancini/goscan/master/.github/goscan_process.png)


1. **Load targets**
    * Add a single target via the CLI (must be a /32): `load target SINGLE <IP>`
    * Upload multiple targets from a text file or folder: `load target MULTI <path-to-file>`
2. **Host Discovery**
    * Perform a Ping Sweep: `sweep <TYPE> <TARGET>`
    * OR load results from a previous discovery:
        - Add a single alive host via the CLI (must be a /32): `load alive SINGLE <IP>`
        - Upload multiple alive hosts from a text file or folder: `load alive MULTI <path-to-file>`
3. **Port Scanning**
    * Perform a port scan: `portscan <TYPE> <TARGET>`
    * OR upload nmap results from XML files or folder: `load portscan <path-to-file>`
4. **Service Enumeration**
    * Dry Run (only show commands, without performing them): `enumerate <TYPE> DRY <TARGET>`
    * Perform enumeration of detected services: `enumerate <TYPE> <POLITE/AGGRESSIVE> <TARGET>`

5. **Special Scans**
   	* *EyeWitness*
        - Take screenshots of websites, RDP services, and open VNC servers (KALI ONLY): `special eyewitness`
        - `EyeWitness.py` needs to be in the system path
	* *Extract (Windows) domain information from enumeration data*
		- `special domain <users/hosts/servers>`
	* *DNS*
		- Enumerate DNS (nmap, dnsrecon, dnsenum): `special dns DISCOVERY <domain>`
		- Bruteforce DNS: `special dns BRUTEFORCE <domain>`
		- Reverse Bruteforce DNS: `special dns BRUTEFORCE_REVERSE <domain> <base_IP>`



In addition, it has a few utils:
* Show results: `show <targets/hosts/ports`
* Change the output folder (by default `~/goscan`): `set output_folder <PATH>`
* Modify the default nmap switches: `set nmap_switches <SWEEP/TCP_FULL/TCP_STANDARD/TCP_VULN/UDP_STANDARD> <SWITCHES>`
* Modify the default wordlists: `set_wordlists <FINGER_USER/FTP_USER/...> <PATH>`





## External Integrations

The _Service Enumeration_ phase currently supports the following integrations:

| WHAT | INTEGRATION |
| ---- | ----------- |
| ARP  | <ul><li>nmap</li></ul> |
| DNS  | <ul><li>nmap</li><li>dnsrecon</li><li>dnsenum</li><li>host</li></ul> |
| FINGER  | <ul><li>nmap</li><li>finger-user-enum</li></ul> |
| FTP  | <ul><li>nmap</li><li>ftp-user-enum</li><li>hydra [AGGRESSIVE]</li></ul> |
| HTTP | <ul><li>nmap</li><li>nikto</li><li>dirb</li><li>sqlmap [AGGRESSIVE]</li><li>fimap [AGGRESSIVE]</li></ul> |
| RDP  | <ul><li>nmap</li></ul> |
| SMB  | <ul><li>nmap</li><li>enum4linux</li><li>nbtscan</li><li>samrdump</li></ul> |
| SMTP | <ul><li>nmap</li><li>smtp-user-enum</li></ul> |
| SNMP | <ul><li>nmap</li><li>snmpcheck</li><li>onesixtyone</li><li>snmpwalk</li></ul> |
| SSH  | <ul><li>hydra [AGGRESSIVE]</li></ul> |
| SQL  | <ul><li>nmap</li></ul> |




# License

GoScan is released under a MIT License. See the `LICENSE` file for full details.
