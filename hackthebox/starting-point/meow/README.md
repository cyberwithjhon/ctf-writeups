# Meow — HackTheBox Writeup

**Platform:** HackTheBox — Starting Point

**Difficulty:** Very Easy

**Category:** Telnet / Misconfiguration

**Author:** Jhon Jairo Martínez Loyo (@cyberwithjhon)

---

## Lab Description

Meow is the first lab in HTB's Starting Point track. Its goal is to get familiar with the working environment (VPN, terminal, basic reconnaissance tools) and exploit a Telnet misconfiguration that allows root access with a blank password.

**Main concept:** an exposed service with no authentication is an open door, regardless of how sophisticated the rest of the system is.

---

## Tasks

### Task 1 — In cybersecurity, isolated environments—like Pwnbox or the vulnerable target machines—are often VMs. What does VM stand for?

**Answer: `Virtual Machine`**

In cybersecurity we constantly work with isolated environments — both to protect the host system and to easily revert any changes made during testing. A **Virtual Machine** is an operating system that runs in isolation on top of another.

---

### Task 2 — What tool do we use to interact with the operating system in order to issue commands via the command line, such as the one to start our VPN connection? It's also known as a console or shell.

**Answer: `terminal`**

The **terminal** (also known as console or shell) is the interface from which we run all our commands. On Linux, the most common shell is **Bash**. Throughout the entire pentesting process the terminal is our primary tool.

---

### Task 3 — What service do we use to form our VPN connection into HTB labs?

**Answer: `openvpn`**

HTB operates on an isolated private network. To reach the target machines we need to establish a VPN tunnel using **OpenVPN**. The process is as follows:

1. Download the `.ovpn` file from the HTB panel (Starting Point → Download VPN)

<img width="438" height="465" alt="Captura de pantalla 2026-06-26 a las 19 00 31" src="https://github.com/user-attachments/assets/fd6c3706-18b0-48b7-9e13-5a78723c17fa" />


2. Establish the connection from the terminal:

```bash
sudo openvpn path/to/file.ovpn
```

<img width="2940" height="740" alt="image" src="https://github.com/user-attachments/assets/ab978f14-8637-4e30-8549-1d0056b914fb" />

It is essential to keep this session active throughout the lab — closing it drops the connection to the target machine.

---

### Task 4 — What tool do we use to test our connection to the target with an ICMP echo request?

**Answer: `ping`**

Before launching any scan, we verify that we can reach the target machine. **Ping** sends ICMP Echo Request packets and waits for a reply — if one is received, the machine is alive and reachable:

```bash
ping <target_IP>
```

<img width="514" height="164" alt="image" src="https://github.com/user-attachments/assets/9cc3fc00-a011-4ee1-8be6-9346fca87835" />

---

### Task 5 — What is the name of the most common tool for finding open ports on a target?

**Answer: `nmap`**

**Nmap** is a port and service scanner. It allows us to identify which ports are open on the target machine and what services are running on each one:

```bash
nmap -sV <target_IP>
```

The `-sV` flag enables service version detection, giving us more detailed information than a plain port scan.

<img width="538" height="95" alt="image" src="https://github.com/user-attachments/assets/1a4b5752-6661-423b-b203-e2a407a45b52" />

---

### Task 6 — What service do we identify on port 23/tcp during our scans?

**Answer: `telnet`**

The Nmap scan reveals:

```
23/tcp  open  telnet
```

<img width="539" height="175" alt="image" src="https://github.com/user-attachments/assets/8ee12f47-0b30-41be-a309-9bda1f406e70" />

**Telnet** is a remote access protocol that transmits everything in plaintext — credentials included. It was the standard before SSH replaced it. Its presence on a machine is already a clear sign of misconfiguration and an obvious attack surface.

---

### Task 7 — What username is able to log into the target over Telnet with a blank password?

**Answer: `root`**

With the service identified, we attempt to connect:

```bash
telnet <target_IP>
```

<img width="317" height="73" alt="image" src="https://github.com/user-attachments/assets/ba8311bd-7958-4ea0-94b2-e54ebe26056f" />

The system prompts for a login. We try the `root` user — the highest-privilege account on Linux — with a blank password:

<img width="539" height="146" alt="image" src="https://github.com/user-attachments/assets/1048cdb4-581d-4389-8770-bfded57bc6d1" />

The system grants access. This confirms that Telnet is critically misconfigured: it allows authentication as `root` with no credentials at all. In a real-world environment, this means full control of the server without needing to exploit any technical vulnerability.

---

### Task 8 — Capture the flag

Once inside the system as `root`, we locate and read the flag:

```bash
pwd         # Check current directory
ls -la      # List everything, including hidden files
cat flag.txt
```

The `ls -la` command is important here — the `-a` flag reveals hidden files (those starting with `.`) that would be missed in a standard listing.

<img width="635" height="285" alt="image" src="https://github.com/user-attachments/assets/b75eeffb-c519-46f8-a8bb-d6beabde8af8" />

**Flag:** `b40abdfe23665f766f9c61ecba8a4c19`

---

## Lessons Learned

**1. Telnet should not be used in modern environments**

It transmits credentials and data in plaintext. Any attacker with access to the network can capture the entire session using Wireshark. SSH has been the standard replacement.

**2. Root access with a blank password is a critical misconfiguration**

No matter how well the rest of the system is configured. If the most privileged account requires no authentication, anyone who reaches the port has immediate full control.

**3. Reconnaissance is the foundation of everything**

Without the Nmap scan we would never have identified port 23.
