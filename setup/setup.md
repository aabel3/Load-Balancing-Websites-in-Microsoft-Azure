# MST300 Project 2: Load Balancing Websites in Azure

## Part 1: Resource Group Setup

A resource group is a logical container for Azure resources.

### Steps:

1. **Login to Azure Portal**
   - Go to https://portal.azure.com
   - Sign in with your CloudLab credentials (ODL account)

2. **Create Resource Group**
   - Click **"Resource groups"** from the left menu
   - Click **"+ Create"**
   - Fill in the details:
     - **Subscription:** Azure for Students
     - **Resource group name:** `MST300-project2-rg`
     - **Region:** (US) East US (or your assigned region)
   - Click **"Review + create"**
   - Click **"Create"**

---

# Part 2: Virtual Networks Configuration

Virtual Networks (VNets) provide isolated network environments for your VMs.

### Important: Use Your Calculated Subnets

Refer to your assigned network address space from Blackboard. You should plan:
- VNet1: covers web servers and Bastion — use the first /25 of your /24
- VNet2: covers the client VM — use the second /25 of your /24

> ⚠️ Do **NOT** use overlapping address spaces between VNet1 and VNet2.

### Example Subnetting Plan

| Component | Address | CIDR | Notes |
|---|---|---|---|
| MST300-vnet1 | xx.xx.xx.0 | /25 | 0–127 range |
| vnet1-subnet1 | xx.xx.xx.0 | /27 | Web servers + LB |
| AzureBastionSubnet | xx.xx.xx.64 | /27 | Bastion only |
| MST300-vnet2 | xx.xx.xx.128 | /25 | 128–255 range |
| vnet2-subnet1 | xx.xx.xx.128 | /26 | Client VM |

### VNet 1: Web Server + Bastion Network

1. **Navigate to Virtual Networks**
   - Search for "Virtual Networks" in the top search bar
   - Click **"+ Create"**

2. **Basics Tab**
   - **Subscription:** Azure for Students
   - **Resource group:** MST300-project2-rg
   - **Virtual network name:** `MST300-vnet1`
   - **Region:** (US) East US
   - Click **"Next: IP Addresses"**

3. **IP Addresses Tab**
   - **IPv4 address space:** Enter your assigned /25 network
     - Example: `131.131.131.0/25`
     - **Use YOUR assigned network here!**
   
   - Azure will automatically create a default subnet — **DELETE IT**
   - Click the trash icon to delete the default subnet

   **Create First Subnet:**
   - Click **"+ Add subnet"**
   - **Subnet name:** `vnet1-subnet1`
   - **Subnet address range:** First /27 of your space
     - Example: `131.131.131.0/27`
   - Click **"Add"**

   **Create Second Subnet (for Bastion):**
   - Click **"+ Add subnet"**
   - **Subnet name:** `AzureBastionSubnet` *(MUST be exactly this name — capitals and no spaces matter!)*
   - **Subnet address range:** Second /27 of your space
     - Example: `131.131.131.64/27`
   - Click **"Add"**

4. **Review + Create**
   - Verify both subnets are listed
   - Verify address spaces don't overlap
   - Click **"Review + create"**
   - Click **"Create"**

### VNet 2: Client Network

1. **Create VNet**
   - Click **"+ Create"** virtual network
   - **Name:** `MST300-vnet2`
   - **Region:** (US) East US

2. **IP Addresses Tab**
   - **IPv4 address space:** Second /25 of your assigned space
     - Example: `131.131.131.128/25`
     - **Use YOUR assigned network here — must not overlap with VNet1!**

   - Delete default subnet if present
   - Click **"+ Add subnet"**
   - **Subnet name:** `vnet2-subnet1`
   - **Subnet address range:** A /26 within the /25
     - Example: `131.131.131.128/26`
   - Click **"Add"**

3. **Review + Create**

### Your Final Network Layout Should Look Like:

**Example using 131.131.131.0/24:**

```
MST300-vnet1 (131.131.131.0/25)
├── vnet1-subnet1       (131.131.131.0/27)   ← Web servers + Load Balancer here
└── AzureBastionSubnet  (131.131.131.64/27)  ← Bastion here

MST300-vnet2 (131.131.131.128/25)
└── vnet2-subnet1       (131.131.131.128/26) ← Client VM here
```

---

# Part 3: Azure Bastion Setup

Azure Bastion provides secure RDP/SSH access to VMs through SSL without exposing them to the public internet.

> ⏱️ **Start Bastion early — it takes 10–15 minutes to deploy!**
> ⚠️ **Azure Bastion cannot be stopped and incurs continuous charges. Delete it when done.**

### Steps:

1. **Create Bastion Host**
   - Search for "Bastions" in Azure Portal
   - Click **"+ Create"**

2. **Configure Bastion**
   - **Subscription:** Azure for Students
   - **Resource group:** MST300-project2-rg
   - **Name:** `MST300-BastionHost`
   - **Region:** (US) East US *(must match VNet region)*
   - **Tier:** Basic
   - **Virtual network:** MST300-vnet1
   - **Subnet:** AzureBastionSubnet *(should auto-select)*

3. **Public IP Address**
   - **Public IP address:** Create new
   - **Public IP address name:** `MST300-BastionIP`
   - **Public IP address SKU:** Standard
   - **Assignment:** Static

4. **Review + Create**
   - Click **"Review + create"**
   - Click **"Create"**
   - ⏱️ **Wait 10–15 minutes for deployment**

---

# Part 4: Azure Load Balancer

An internal load balancer distributes incoming traffic across multiple backend VMs within a private virtual network.

### Part 4.1: Create the Load Balancer

1. **Navigate to Load Balancers**
   - Search for "Load balancers" in Azure Portal
   - Click **"+ Create"**

2. **Basics Tab**
   - **Subscription:** Azure for Students
   - **Resource group:** MST300-project2-rg
   - **Name:** `MST300-lb`
   - **Region:** (US) East US
   - **SKU:** Standard
   - **Type:** Internal
   - **Tier:** Regional

3. **Frontend IP Configuration Tab**
   - Click **"+ Add a frontend IP configuration"**
   - **Name:** `LoadBalancerFrontEnd`
   - **Virtual network:** MST300-vnet1
   - **Subnet:** vnet1-subnet1
   - **IP address assignment:** Dynamic
   - **Availability zone:** Zone-redundant
   - Click **"Add"**

4. **Review + Create**
   - Click **"Review + create"**
   - Click **"Create"**

### Part 4.2: Configure Backend Pool

1. **Navigate to MST300-lb**
   - Go to your load balancer resource
   - Click **"Backend pools"** in the left menu
   - Click **"+ Add"**

2. **Configure Backend Pool**
   - **Name:** `MST300-BackendPool`
   - **Virtual network:** MST300-vnet1
   - Click **"Save"**
   
   > ℹ️ VMs will be added to this pool during VM creation in Part 5.

### Part 4.3: Configure Health Probe

1. **Navigate to Health Probes**
   - In MST300-lb, click **"Health probes"**
   - Click **"+ Add"**

2. **Configure Health Probe**
   - **Name:** `MST300-HealthProbe`
   - **Protocol:** HTTP
   - **Port:** 80
   - **Interval:** 15
   - **Unhealthy threshold:** 3
   - Click **"Save"**

### Part 4.4: Configure Load Balancing Rule

1. **Navigate to Load Balancing Rules**
   - In MST300-lb, click **"Load balancing rules"**
   - Click **"+ Add"**

2. **Configure Rule**
   - **Name:** `MST300-HTTPRule`
   - **IP Version:** IPv4
   - **Frontend IP address:** LoadBalancerFrontEnd
   - **Protocol:** TCP
   - **Port:** 80
   - **Backend port:** 80
   - **Backend pool:** MST300-BackendPool
   - **Health probe:** MST300-HealthProbe (HTTP:80)
   - **Session persistence:** None
   - **Idle timeout (minutes):** 15
   - **TCP reset:** Enabled
   - **Floating IP:** Disabled
   - Click **"Save"**

---

# Part 5: Web Server VMs

Create 2 virtual machines running Windows Server 2019 with IIS. Each server is placed in a different availability zone and registered in the load balancer backend pool.

> ⚠️ **IMPORTANT:** Seneca's Azure policy requires **Standard HDD (Standard_LRS)** disk type. Premium SSD will cause deployment to fail!

### Webserver 1

1. **Create Virtual Machine**
   - Search for "Virtual machines"
   - Click **"+ Create"** → **"Azure virtual machine"**

2. **Basics Tab**
   - **Subscription:** Azure for Students
   - **Resource group:** MST300-project2-rg
   - **Virtual machine name:** `webserver1-vm`
   - **Region:** (US) East US
   - **Availability options:** Availability zone
   - **Availability zone:** Zone 1
   - **Security type:** Standard
   - **Image:** Windows Server 2019 Datacenter – Gen2 *(use Gen1 if Gen2 is unavailable)*
   - **Size:** Standard_B2s *(minimum recommended for IIS performance — B1s may be sluggish)*
   - **Username:** `azureuser`
   - **Password:** [Create a strong password — save it!]
   - **Public inbound ports:** None

3. **Disks Tab**
   - **OS disk type:** Standard HDD (Standard_LRS)

4. **Networking Tab**
   - **Virtual network:** MST300-vnet1
   - **Subnet:** vnet1-subnet1
   - **Public IP:** None
   - **NIC network security group:** Basic
   - **Load balancing options:** Azure load balancer
   - **Select a load balancer:** MST300-lb
   - **Select a backend pool:** MST300-BackendPool

5. **Review + Create**
   - Click **"Review + create"**
   - Click **"Create"**
   - ⏱️ Wait 3–5 minutes for deployment

### Webserver 2

Repeat all the same steps with the following changes:
- **Virtual machine name:** `webserver2-vm`
- **Availability zone:** Zone 2
- Everything else identical

---

# Part 6: IIS Configuration

Install Internet Information Services (IIS) on both web servers and customize the default pages so you can visually confirm which server is serving requests.

### Part 6.1: Install IIS Role

Perform these steps on **both webserver1-vm and webserver2-vm**.

1. **Connect to the VM via Bastion**
   - Go to the VM in Azure Portal
   - Click **"Connect"** → **"Bastion"**
   - **Username:** `azureuser`
   - **Password:** [your password]
   - Click **"Connect"**

2. **Open Server Manager**
   - Server Manager should open automatically on login
   - If not: Start → Server Manager

3. **Add Roles and Features**
   - Click **"Add roles and features"**
   - Click **"Next"** (Before You Begin)
   - **Installation Type:** Role-based or feature-based installation
   - Click **"Next"**
   - **Server Selection:** Select the current VM
   - Click **"Next"**

4. **Select Server Roles**
   - Check ☑️ **"Web Server (IIS)"**
   - Click **"Add Features"** when prompted
   - Click **"Next"**

5. **Features / Role Services**
   - Keep all defaults
   - Click **"Next"** through remaining screens

6. **Confirmation**
   - Click **"Install"**
   - ⏱️ Wait 3–5 minutes
   - Click **"Close"**

### Part 6.2: Customize the Default IIS Page — Webserver 1

1. **Open File Explorer on webserver1-vm**
   - Navigate to: `C:\inetpub\wwwroot`

2. **Edit iisstart.htm**
   - Right-click `iisstart.htm` → **Open with** → **Notepad**

3. **Add the following near the top of the `<body>` tag:**
   ```html
   <title>studentID's Windows Server</title>
   ```
   Replace `studentID` with your actual student ID (e.g., `asabra1's Windows Server`)

4. **Add a background colour and body text** by modifying the `<body>` tag:
   ```html
   <body style="background-color: green;">
       <p>This webpage is hosted on webserver1-vm</p>
   ```

5. **Save the file**
   - File → Save
   - Close Notepad

6. **Test locally**
   - Open Internet Explorer or Edge on the VM
   - Navigate to: `http://localhost`
   - Verify the green background and correct text appear

### Part 6.3: Customize the Default IIS Page — Webserver 2

Repeat on **webserver2-vm** with these differences:
- **Title:** `studentID's WebPage`
- **Background color:** `yellow`
- **Body text:** `This webpage is hosted on webserver2-vm`

Test locally with `http://localhost` before moving on.

---

# Part 7: Client VM

Create a Windows 10 VM in VNet2. This VM will be used to test the load balancer from a separate virtual network.

### Steps:

1. **Create Virtual Machine**
   - Go to Virtual machines → **"+ Create"**

2. **Basics Tab**
   - **Subscription:** Azure for Students
   - **Resource group:** MST300-project2-rg
   - **Virtual machine name:** `client-vm`
   - **Region:** (US) East US
   - **Availability options:** No infrastructure redundancy required
   - **Image:** Windows 10 Pro, Version 20H2 – Gen2 *(use another version if unavailable)*
   - **Size:** Standard_B1s
   - **Username:** `azureuser`
   - **Password:** [same password as webservers for simplicity]
   - **Licensing:** Check ☑️ the Windows Client license box
   - **Public inbound ports:** None

3. **Disks Tab**
   - **OS disk type:** Standard HDD (Standard_LRS)

4. **Networking Tab**
   - **Virtual network:** MST300-vnet2
   - **Subnet:** vnet2-subnet1
   - **Public IP:** None

5. **Review + Create**
   - Click **"Review + create"**
   - Click **"Create"**

---

# Part 8: Virtual Network Peering

Connect VNet1 and VNet2 so the client VM can reach the load balancer, and so Azure Bastion in VNet1 can connect to the client VM in VNet2.

### Peering: vnet1 ↔ vnet2

1. **Navigate to MST300-vnet1**
   - Go to Virtual Networks → MST300-vnet1
   - Click **"Peerings"** in the left menu
   - Click **"+ Add"**

2. **Configure Peering**
   - **This virtual network — Peering link name:** `vnet1-to-vnet2`
   - **Remote virtual network — Peering link name:** `vnet2-to-vnet1`
   - **Virtual network:** MST300-vnet2
   - **Traffic to remote virtual network:** Allow
   - **Traffic forwarded from remote virtual network:** Allow
   - **Virtual network gateway or Route Server:** None
   - Click **"Add"**

3. **Verify**
   - Wait for peering status to show **"Connected"** on both sides
   - Check both MST300-vnet1 and MST300-vnet2 peering lists

---

# Part 9: Private DNS Zone

Create a private DNS zone so the client VM can resolve the load balancer by its FQDN rather than using a raw IP address.

### Part 9.1: Create the DNS Zone

1. **Navigate to Private DNS Zones**
   - Search for "Private DNS zones" in Azure Portal
   - Click **"+ Create"**

2. **Configure DNS Zone**
   - **Subscription:** Azure for Students
   - **Resource group:** MST300-project2-rg
   - **Name:** `studentID.mst300.com` *(replace studentID with yours, e.g., `asabra1.mst300.com`)*
   - Click **"Review + create"**
   - Click **"Create"**

### Part 9.2: Link DNS Zone to VNets

The DNS zone must be linked to both VNets so all VMs can resolve the domain name.

1. **Navigate to your DNS Zone**
   - Go to the newly created Private DNS zone
   - Click **"Virtual network links"** in the left menu

2. **Link to VNet1**
   - Click **"+ Add"**
   - **Link name:** `vnet1-link`
   - **Virtual network:** MST300-vnet1
   - **Enable auto registration:** ✓ Checked
   - Click **"OK"**

3. **Link to VNet2**
   - Click **"+ Add"**
   - **Link name:** `vnet2-link`
   - **Virtual network:** MST300-vnet2
   - **Enable auto registration:** ✓ Checked
   - Click **"OK"**

### Part 9.3: Create an A Record for the Load Balancer

1. **Get the Load Balancer Frontend IP**
   - Go to MST300-lb → **Overview**
   - Note the **Private IP address** shown under Frontend IP configuration

2. **Create A Record**
   - In your Private DNS zone, click **"Record sets"** (or **"+ Record set"**)
   - Click **"+ Add"**
   - **Name:** `www`
   - **Type:** A
   - **TTL:** 1 Hour
   - **IP address:** [Your load balancer frontend IP]
   - Click **"OK"**

   > This creates the FQDN: `www.studentID.mst300.com` pointing to the load balancer.

---

# Part 10: Testing and Verification

Verify all components are working correctly before recording your video.

### Test 1: Connect to Client VM via Bastion

1. **Go to client-vm in Azure Portal**
   - Click **"Connect"** → **"Bastion"**
   - **Username:** `azureuser`
   - **Password:** [your password]
   - Click **"Connect"**
   - Should connect successfully through VNet peering

### Test 2: Load Balancer via FQDN

1. **On client-vm, open a browser (Edge or Internet Explorer)**

2. **Navigate to:**
   ```
   http://www.studentID.mst300.com
   ```
   *(Replace studentID with yours, e.g., `http://www.asabra1.mst300.com`)*

3. **Force refresh multiple times**
   - Press `Ctrl + Shift + Delete` to clear browser cache, or open an **InPrivate/Incognito window**
   - Reload the page several times with `F5`
   - You should see the page alternate between:
     - 🟩 **Green background** — served by webserver1-vm
     - 🟨 **Yellow background** — served by webserver2-vm

   > ℹ️ This confirms load balancing is working, VNet peering is functioning, and Private DNS is resolving correctly.

### Test 3: DNS Resolution

1. **On client-vm, open PowerShell**
   ```powershell
   # Verify DNS resolves to the load balancer IP
   nslookup www.studentID.mst300.com

   # Test HTTP connectivity to load balancer
   Test-NetConnection -ComputerName www.studentID.mst300.com -Port 80
   ```

### Test 4: Verify Backend Pool Health

1. **In Azure Portal, go to MST300-lb**
   - Click **"Backend pools"** → **MST300-BackendPool**
   - Verify both webserver1-vm and webserver2-vm are listed

2. **Check Health Probes**
   - Click **"Insights"** or **"Metrics"** on the load balancer
   - Both VMs should show as healthy
