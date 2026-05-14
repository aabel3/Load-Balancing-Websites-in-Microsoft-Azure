## Troubleshooting Guide

### Issue 1: VM Deployment Fails — Disk Policy Error

**Error:** `RequestDisallowedByPolicy` — Premium SSD not allowed

**Solution:**
- Change **OS disk type** to **Standard HDD (Standard_LRS)**
- Seneca's Azure policy blocks Premium SSD disks

---

### Issue 2: VMs Slow or IIS Unresponsive

**Problem:** Standard_B1s (1 vCPU, 1 GB RAM) is too small for Windows Server 2019 + IIS

**Solution:**
- Resize to **Standard_B2s** (2 vCPUs, 4 GB RAM)
- Stop VM in Azure Portal → Click **"Size"** → Select Standard_B2s → Click **"Resize"**

---

### Issue 3: VNet Peering Fails — Address Space Overlap

**Error:** "Address space overlaps with already peered virtual network"

**Solution:**
- VNet1 and VNet2 must use completely non-overlapping address ranges
- Use the first /25 of your /24 for VNet1 and the second /25 for VNet2
  - VNet1: `xx.xx.xx.0/25` (hosts 0–127)
  - VNet2: `xx.xx.xx.128/25` (hosts 128–255)

---

### Issue 4: Capacity Error — Cannot Start VM in Availability Zone

**Error:** `ZonalAllocationFailed` — No capacity in availability zone

**Solutions:**
1. Try starting the VM again — zone capacity frees up frequently
2. If VM must be recreated:
   - Delete the VM *(keep the disk!)*
   - Recreate from existing disk
   - Re-add the NIC to the load balancer backend pool
3. Try a different VM size (D-series often has better zone availability)

---

### Issue 5: Load Balancer Only Showing One Server

**Problem:** After VM recreation, the VM is missing from the backend pool

**Solution:**
- Go to **MST300-lb** → **Backend pools** → **MST300-BackendPool**
- Click **"+ Add"** and select the missing VM's NIC
- Click **"Save"**

---

### Issue 6: Website Not Accessible from Client VM

Work through this checklist in order:

- [ ] VNet peering status shows **"Connected"** on both sides
- [ ] Private DNS zone is linked to both VNet1 and VNet2
- [ ] A record `www` points to the correct load balancer frontend IP
- [ ] Both webservers are running and listed in the backend pool
- [ ] Health probe status shows both VMs as healthy
- [ ] Browser cache cleared / using InPrivate window
- [ ] FQDN used matches your DNS zone name exactly

---
