# LAB01 - Zabbix server windows client 

## Overview

The goal of this lab was to install and configure a fully functional Zabbix server running on Debian 12, along with Apache2 web server and MariaDB as the backend database. The lab includes setting up Zabbix frontend, verifying connectivity, and monitoring the availability of external or internal devices via ICMP (ping) — with no need for SNMP or Zabbix agent installation.

## Objectives

Deploy and configure Zabbix 7.2 server on Debian 12

Set up Apache2 and MariaDB database for Zabbix frontend

Connect and monitor a virtual test PC using ICMP

Troubleshoot host visibility issues and verify proper data collection

## Environment

### Virtual Machine 1 (Zabbix Server):

Zabbix Server: Debian 12.0

Database: MariaDB 10.11

Web Server: Apache2

Zabbix Version: 7.2

Monitored Targets: Google DNS (8.8.8.8), internal test PC (Windows)

Network Setup: Hyper-V with NAT and external switches

### Virtual Machine 2 (Client):

OS: Windows 11 Pro

Service: Zabbix Agent

Network Setup: Hyper-V with NAT and external switches

![alt text](https://github.com/rivalgames22/infrastructure-labs-portfolio/blob/main/Windows%20Server%20Labs/LAB01-Zabbix%20Server/screenshots/hyperv.png)

## Configuration Process

Step 1: Installing Zabbix Server

Installed Zabbix server and frontend from the official repo

Configured MariaDB and created Zabbix database and user

Set up Apache2 and verified frontend was reachable from host browser

Common issues:

Misconfigured database access permissions

Missing PHP extensions

Timezone not visible in Apache config (manually appended in php.ini)

Step 2: Setting Up Virtual PC

Created second VM as test endpoint

Ensured connectivity by configuring second NIC with static IP

Verified ping from host and from Zabbix server

Faced issue where VM got stuck in PXE boot – fixed by using Generation 1 VM and attaching ISO properly

Step 3: Troubleshooting Internet Access

School Wi-Fi required MAC-based authentication; NAT network on VM could not access internet

Solved by adding another virtual switch connected to host external adapter

Ensured Zabbix server had internet access through that second switch

Step 4: Monitoring via ICMP

Added host in Zabbix with Agent interface (dummy, required)

Assigned Template Module ICMP Ping

Items (icmpping, icmppingsec, icmppingloss) were present, but host showed UNKNOWN

Realized Zabbix only updates host status if Zabbix agent responds, not with simple checks

Solved by creating custom trigger:

{ICMP-Test:icmpping.max(120)}=0

This trigger marked host as DOWN if no ping reply within last 2 minutes

Status transitioned to OK after first response

Step 5: Attempted SNMP Monitoring (Later Abandoned)

Tried to monitor demo.snmplabs.com via SNMPv2

Ping worked, but SNMP failed (UDP port 161 blocked or filtered)

nmap output: 161/udp open|filtered

Concluded SNMP is not feasible in restricted environments
