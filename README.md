# Azure Files with AD authentication using Private endpoint with Azure Firewall as DNS forwarder

## Use Case
- Customer that intend to move File server to the cloud and must keep their users (file consumers) on-premises.

- So … As Azure Storage account originally is exposed to the internet, Customer may consider Private endpoints to keep the communication between on-premises and Azure privately.

- However … In order to use Private endpoint services and keep using on-premises DNS server, a forwarder need to be considered, and so, Azure Firewall may be used as a DNS forwarder, as a great option for it, as it is a PaaS service, easy to implement, manage and it still may be used as a Firewall, its main function.

## Architecture



## Prerequisites
It is assumed that you already have:

- Azure Subscription
- RBAC permission to perform all implementation
- AD permission to perform AD domain joined and DNS administration
- AD synchronized with Azure AD
- VPN or ExpressRoute implemented between on-premises and Azure
- Network topology that works


## Implementation step by step
AZURE SIDE

- Create a VNET
  - Create at least two subnets, one for the Private Endpoint and one for the Azure Firewall
  - Set custom DNS at VNET (pointing to on-premises DNS IP)
- Create Storage Account
  - Create File Share
- Create Private endpoint
- Create Azure Firewall
  - Set up Azure Firewall to work as DNS proxy (then perform as a forwarder)
  - Set custom DNS
- Set up Azure File to be authenticated with on-premises AD
  - Enable ADDS authentication for Azure file (run PS command)
  - Assign share-level permission (RBAC)
  - Configure directory and file level permission over SMB (Windows ACL)
 
 ON-PREMISES SIDE
 
 - create a DNS Conditional Forwarder at your DNS server
 
 ## Azure Files features and best practices
Important features
- General purpose v1 (HDD based):
  - not recommended
  - doesn’t support lots of new features
- General purpose v2 (HDD based):
  - only SMB
  - LRS, ZRS, GRS, GZRS
  - 5TiB by default or 100TiB only LRS, ZRS
- FileStorage storage account (SSD based)
  - SMB or NFS / 
  - LRS and ZRS
  - up to 100TiB by default (in GPv2)
  - not available in all region
  
Best practices

- only use the File Share in a storage account (don't mix with blob or others)
- pay attention in the storage account IOPS limitation
- ideally map file share 1:1 (one file share per storage account) or
- group file shares that are not so active in the same storage account and highly active separated
- don’t use GPv1

## Private Endpoint Architecture

Features

- DNS query must be originated from the VNET to Azure DNS.

- Three options to DNS forwarder:
  - Win VM with DNS Server
  - Linux VM with DNS Server
  - Azure Firewall
  
## Private endpoint implementation

- Configure Private endpoint for Storage
  - Test access with nslookup
- Configure Azure Firewall with DNS proxy
  - Set DNS proxy with Azure DNS IP (168.63.129.16)
  - Az Firewall > Firewall manager > Az FW Policies > DNS
  - Capture Azure Firewall private IP
- At your DNS Server on-premises set Conditional forwarder
  - use Azure Firewall private IP
  - Set conditional forwarder to domain “file.core.windows.net”
- Test access again with nslookup
- Set storage account networking 


## Azure File Authenticated with ADDS

- create Storage Account and File share

- enable ADDS authentication for your Azure File share
  - download AzFilesHybrid module to use with Powershell
  - run Join-AzStorageAccountForAuth
  - ** run as Administrator **

- configure share-level permissions to the users/groups (through RBAC)

- configure directory and file level permission (ACL) over SMB

- test it.






 

