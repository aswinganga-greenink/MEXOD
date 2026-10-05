# Multi-Tenant eBPF/XDP Orchestration Daemon

A lightweight user-space orchestration daemon for managing eBPF/XDP programs in isolated multi-tenant container environments on a single Linux host.

The project aims to reduce network-stack overhead while safely allowing multiple containers (tenants) to use eBPF-based networking policies without interfering with each other.

## Overview

Traditional container networking can introduce overhead through components such as:

- veth pairs
- Linux bridges
- iptables
- additional kernel network-stack processing
- user-space sidecar proxies

This project explores an alternative approach using **eBPF/XDP** to process packets closer to the network interface and **sockmap** to redirect validated traffic directly toward the target container's socket.

The system is managed by a lightweight user-space orchestration daemon.

## Architecture

The proposed packet-processing pipeline is:

```text
Incoming Packet
      |
      v
+-------------+
|     NIC     |
+-------------+
      |
      v
+-------------+
| XDP Filter  |
+-------------+
      |
      v
+----------------------+
| Dynamic eBPF Chaining|
+----------------------+
      |
      v
+-------------+
|   Sockmap   |
+-------------+
      |
      v
+----------------------+
| Target Container     |
| Socket / Application |
+----------------------+