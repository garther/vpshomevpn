# Home OpenVPN Server with no Public IP using VPS server
## Concept
As not all ISPs are granting public IP to their customers, publishing services over the internet is getting more difficult. Here we are going to provision a OpenVPN server for home network access (and it's resources) using a VPS as our public (and static) entry point. Communication between the VPS and home router will go via l2tp VPN connection with IPSEC encryption enabled to provide secure transport.
> **For security reasons**, the VPN server will be running on the edge home router. By doing this, the VPS will have no access to your home network so if compromised, there will be no harm to privacy of your data hosted there.

## Diagram
(vpn_conn.png)

Above diagram shows how traffic will be passing in the configuration we are about to make.
- End user connects to VPS server public IP
- VPS using firewall NAT rules, forwards traffic to home router IP
- Whole communication secured with encryption


## Requirements
- VPS server (anything with linux and public IP)
- Router capable of hosting OpenVPN server

In this tutorial I am using:
- VPS with 1 core and 2gb ram running Debian 9
- Mikrotik RB2011UiAS-RM

# Configuration
## VPN Server
0. Choose two passwords: one for IPSEC transport, second one for "vpn" user
1. Firs, let's install needed software
```
$ sudo apt-get install xl2tpd strongswan ppp
```
2. Clone this repository and copy file to /etc directory
3. Create secret files with passwords
**/etc/ipsec.secrets**
```
: PSK "ipsec_pass"
vpn : EAP "vpn_user_pass"
vpn : XAUTH "vpn_user_pass"
```
**/etc/ppp/chap-secrets**
```
# This file holds secrets for L2TP authentication.
# Username  Server  Secret  Hosts
"vpn" "*" "vpn_user_pass" "*"
```
## Home Router
