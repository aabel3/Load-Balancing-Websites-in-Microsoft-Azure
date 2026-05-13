# MST300 Project 2 – Load Balancing Websites in Azure

Welcome to MST300 Project 2!

In this project, you will design and implement an Azure cloud infrastructure featuring an internal load balancer, multiple virtual networks, and a private DNS zone. The focus is on load balancing, internal networking, VNet peering, and private DNS resolution, preparing you for real-world high-availability web service deployments.

📚 Project Objectives
+ Design and deploy a multi-VNet Azure architecture
+ Configure Azure Bastion for secure VM access without public IPs
+ Deploy and configure an Azure Internal Load Balancer
+ Set up two IIS web servers across separate availability zones
+ Configure a backend pool, health probe, and load balancing rule
+ Join a client VM in a separate VNet via VNet Peering
+ Create and configure an Azure Private DNS Zone
+ Access the load-balanced website from the client VM using a fully qualified domain name (FQDN)
+ Document all configuration steps with screenshots and commands

🏗 Project Components
You will build the following Azure resources:
+ 1 Resource Group (MST300-project2-rg)
+ 2 Virtual Networks (MST300-vnet1, MST300-vnet2)
+ 3 Subnets (vnet1-subnet1, AzureBastionSubnet, vnet2-subnet1)
+ 1 Azure Bastion Host (MST300-BastionHost)
+ 2 Web Server VMs running IIS (webserver1-vm, webserver2-vm)
+ 1 Client VM running Windows 10 (client-vm)
+ 1 Azure Internal Load Balancer (MST300-lb)
+ Virtual Network Peering between MST300-vnet1 and MST300-vnet2
+ 1 Azure Private DNS Zone (<studentID>.mst300.com)

🧩 Tips & Best Practices
+ Ensure VNet Peering is fully configured in both directions before testing connectivity.
+ Verify the Private DNS Zone is linked to both VNets to enable name resolution on client-vm.
+ Add an A record in the Private DNS Zone pointing to the Load Balancer's frontend IP.
+ Use Incognito / InPrivate mode when testing load balancing — the browser must not cache the session to observe switching between servers.
+ Take screenshots at every major step for grading proof.
+ Delete unused resources after each session to minimize Azure charges (especially Azure Bastion, which cannot be stopped).


🧪 What This Project Demonstrates
+ High-availability web architecture using Azure Load Balancer
+ Internal (private) load balancing across availability zones
+ Multi-VNet Azure design with network segmentation
+ Secure remote access to private VMs via Azure Bastion
+ Private DNS resolution for internal services
+ FQDN-based load-balanced service access from a client in a separate network

📖 References
+ Azure Bastion Documentation (https://learn.microsoft.com/en-us/azure/bastion/)
+ Azure Virtual Network Documentation (https://learn.microsoft.com/en-us/azure/virtual-network/)
+ Azure Virtual Network Peering (https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview)
+ Azure Load Balancer Documentation (https://learn.microsoft.com/en-us/azure/load-balancer/)
+ Create an Internal Load Balancer (https://learn.microsoft.com/en-us/azure/load-balancer/quickstart-load-balancer-standard-internal-portal)
+ Azure Private DNS Documentation (https://learn.microsoft.com/en-us/azure/dns/private-dns-overview)
+ Create a Private DNS Zone (https://learn.microsoft.com/en-us/azure/dns/private-dns-getstarted-portal)
+ Microsoft Learn – Azure Virtual Machines (https://learn.microsoft.com/en-us/azure/virtual-machines/)
