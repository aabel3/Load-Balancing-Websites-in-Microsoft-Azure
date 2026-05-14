# Network Topology
```
MST300-project2-rg
├── MST300-vnet1 (Web Server + Bastion Network)
│   ├── vnet1-subnet1 (webserver1-vm, webserver2-vm, MST300-lb)
│   └── AzureBastionSubnet (MST300-BastionHost)
└── MST300-vnet2 (Client Network)
    └── vnet2-subnet1 (client-vm)
```
> VNet peering is configured between MST300-vnet1 and MST300-vnet2 (bi-directional).

# Virtual Machines Topology
| VM Name | OS | Purpose | Network | Availability Zone |
|---|---|---|---|---|
| webserver1-vm | Windows Server 2019 Datacenter | IIS Web Server (Load Balanced) | MST300-vnet1 | Zone 1 |
| webserver2-vm | Windows Server 2019 Datacenter | IIS Web Server (Load Balanced) | MST300-vnet1 | Zone 2 |
| client-vm | Windows 10 Pro | Client Workstation | MST300-vnet2 | None |

# Load Balancer Topology
| Component | Name | Setting |
|---|---|---|
| Load Balancer | MST300-lb | Internal, Standard SKU |
| Frontend IP | LoadBalancerFrontEnd | Dynamic, vnet1-subnet1 |
| Backend Pool | MST300-BackendPool | webserver1-vm + webserver2-vm |
| Health Probe | MST300-HealthProbe | HTTP, Port 80, Interval 15s |
| Load Balancing Rule | MST300-HTTPRule | TCP, Port 80, Idle Timeout 15 min |

# DNS Configuration
- **Private DNS Zone:** `studentID.mst300.com` (replace `studentID` with your actual student ID)
- **A Record:** `www` → Load Balancer Frontend IP
- **Full FQDN used for testing:** `http://www.studentID.mst300.com`
- **DNS Zone linked to:** MST300-vnet1 and MST300-vnet2 (auto-registration enabled)

# Subnet Calculation Table Topology
| Subnet Name | Network Address | Usable Range | Broadcast | CIDR | Hosts |
|---|---|---|---|---|---|
| vnet1-subnet1 | x.x.x.0 | x.x.x.1 – x.x.x.30 | x.x.x.31 | /27 | 30 |
| AzureBastionSubnet | x.x.x.64 | x.x.x.65 – x.x.x.94 | x.x.x.95 | /27 | 30 |
| vnet2-subnet1 | x.x.x.128 | x.x.x.129 – x.x.x.190 | x.x.x.191 | /26 | 62 |

> ⚠️ VNet1 uses the first /25 of your assigned /24. VNet2 uses the second /25. Address spaces must not overlap.

# Example with Actual IP (131.131.131.0/24)
If your assigned network is `131.131.131.0/24`, your subnets would be:

| Subnet Purpose | Subnet Address | Address Range | VNet |
|---|---|---|---|
| Web Servers + Load Balancer | 131.131.131.0/27 | 131.131.131.1 – 131.131.131.30 | MST300-vnet1 |
| Azure Bastion | 131.131.131.64/27 | 131.131.131.65 – 131.131.131.94 | MST300-vnet1 |
| Client VM | 131.131.131.128/26 | 131.131.131.129 – 131.131.131.190 | MST300-vnet2 |

# VNet Peering Topology
| Peering Link Name | From | To | Status |
|---|---|---|---|
| vnet1-to-vnet2 | MST300-vnet1 | MST300-vnet2 | Connected |
| vnet2-to-vnet1 | MST300-vnet2 | MST300-vnet1 | Connected |
