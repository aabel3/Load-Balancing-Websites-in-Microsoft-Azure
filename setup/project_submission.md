## Project Submission

## Video Recording Requirements
Create a video demonstration **(under 7 minutes)** showing all components below.
+ ⚠️ **Your video MUST have your camera and microphone on throughout.**
+ ⚠️ **The recording must clearly capture your CloudLab account (ODL account) and your computer's date and time.**
+ ⚠️ **Do NOT use an admin account to log into client-vm — use `azureuser` with your local credentials as required.**

### 1. Introduction (30 seconds)

- Show your face on camera
- State your name and student ID
- Show your **CloudLab account** (ODL account — visible in Azure Portal)
- Show your **computer's date and time**
- Confirm it is your own work

### 2. Azure Bastion Login (1 minute)

- In Azure Portal, navigate to **client-vm**
- Click **Connect → Bastion**
- Log in using the **local admin account:**
  - **Username:** `azureuser`
  - **Password:** your VM password
- Show the successful connection to the Windows 10 desktop
- This demonstrates Bastion connectivity through VNet peering from VNet1 to VNet2

### 3. Load Balancer Website Access (2 minutes)

- From **client-vm**, open a browser in **InPrivate / Incognito mode**
- Navigate to:
  ```
  http://www.studentID.mst300.com
  ```
  *(Replace `studentID` with your actual student ID, e.g. `http://www.asabra1.mst300.com`)*
- Show the page loading with the correct **title**, **body text**, and **background colour**
- **Force refresh the page multiple times (F5)** without closing the InPrivate window
- Show the page alternating between:
  - 🟩 **Green background** — `This webpage is hosted on webserver1-vm`
  - 🟨 **Yellow background** — `This webpage is hosted on webserver2-vm`
- This demonstrates the load balancer distributing traffic and Private DNS resolving the FQDN

> ℹ️ If the page does not switch, clear the browser cache and retry in a fresh InPrivate window. Session persistence must be set to **None** in the load balancing rule.

### 4. Virtual Networks & Peering (1.5 minutes)

In **Azure Portal**, demonstrate the following:

- Navigate to **Virtual Networks** and show both:
  - `MST300-vnet1` — with subnets `vnet1-subnet1` and `AzureBastionSubnet`
  - `MST300-vnet2` — with subnet `vnet2-subnet1`
- Click into **MST300-vnet1 → Peerings**
  - Show `vnet1-to-vnet2` with status **"Connected"**
- Click into **MST300-vnet2 → Peerings**
  - Show `vnet2-to-vnet1` with status **"Connected"**
- This confirms network segmentation and peering are correctly configured

### 5. Load Balancer & Private DNS Configuration (1.5 minutes)

In **Azure Portal**, demonstrate the following:

**Load Balancer:**
- Navigate to **MST300-lb**
- Show **Backend pools → MST300-BackendPool** with both `webserver1-vm` and `webserver2-vm` listed
- Show **Health probes → MST300-HealthProbe** (HTTP, port 80)
- Show **Load balancing rules → MST300-HTTPRule** (TCP, port 80, session persistence: None)

**Private DNS Zone:**
- Navigate to your **Private DNS Zone** (`studentID.mst300.com`)
- Show the **A record** (`www`) pointing to the load balancer frontend IP
- Show **Virtual network links** — both `vnet1-link` and `vnet2-link` listed and enabled

### 6. Conclusion (30 seconds)

- Summarize what was demonstrated:
  - Internal load balancer distributing traffic across two availability zones
  - Private DNS resolving the FQDN from a client in a separate VNet
  - VNet peering connecting the two virtual networks
  - Azure Bastion providing secure access without public IPs
- Thank the viewer
