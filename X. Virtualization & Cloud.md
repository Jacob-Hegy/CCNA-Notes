# Types of Virtualization
- Type 1: bare metal hypervisor (ex. Hyper-V); also called a Native hypervisor
- Type 2: software hypervisor (ex. Virtualbox, VMWare, etc.)
# Connecting VMs to the Network
- VMs connect to each other by connecting their virtual NICs to a Virtual Switch
- The virtual switch separates the VMs into VLANs (not by default, as specified)
- The virtual switch connects to the physical NICs of the server with trunk interfaces
# Cloud Services
### On-Premises:
- All infrastructure is located on company property and owned by the company
- Company is responsible for maintaining the hardware
### Colocation:
- Data centers rent out space for customers' infrastructure
- The data center provides the space, electricity, and cooling; the customer takes care of the rest
# Five Essential Characteristics of Cloud
### On-demand self-service
- The customer can use the service freely without directly communicating with the service provider
### Broad network access
- The service can be accessed through standard network connection across many different kinds of devices
### Resource pooling
- The service provider provides a pool of resources that fulfills requests by allocating the necessary resources based on the task
### Rapid elasticity
- Resources quickly expand and retract based on the needs of a service
### Measured service
- The service provider tracks the customer's usage and charges them based on that
# Cloud Service Models
### SaaS
- Provider supplies an application running on cloud infrastructure that clients can interface with and use
### PaaS
- Provider supplies infrastructure that the customer can deploy developed/acquired applications onto
### IaaS
- Provider supplies hardware which the customer can use to deploy different operating systems and applications

|          | Hosted Applications | Development Tools | Operating Systems | Services and Storage | Networking and Security | Physical Location |
| -------- | ------------------- | ----------------- | ----------------- | -------------------- | ----------------------- | ----------------- |
| **SaaS** | Provider            | Provider          | Provider          | Provider             | Provider                | Provider          |
| **PaaS** | Customer            | Provider          | Provider          | Provider             | Provider                | Provider          |
| **IaaS** | Customer            | Customer          | Customer          | Provider             | Provider                | Provider          |
# Cloud Deployment Models
### Private
- A cloud specifically for one single business (ex. AWS providing a private cloud for the DoD)
### Public
- A cloud that's publicly available (ex. Google Drive, Azure, AWS, GCP, etc.)
### Community
- Like a private cloud but used by a group of likeminded businesses
### Hybrid
- Combination of any of the previous three
