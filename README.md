# Workshop Day: Networking Avançado do Azure

Este é o repositório que contém os materiais de apoio do Workshop Day

Para realizar as atividades do Hands-on Lab estamos utilizando o Portal do Azure no idioma Inglês a fim de manter o padrão e não haver erros, e se necessário caso tenha alguma dificuldade no entendimento você pode abrir o Google Tradutor para melhor entendimento.

## Workshop Day
## Exercise #01.1 - Deploy Virtual Networks for Hub (10 minutes)

1. In the Azure portal, search for and select **Virtual networks**, and, on the **Virtual networks** blade, click **+ Add**.

1. Create a virtual network with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you will be using in this lab |
    | Resource Group | (create new) **WGVNetRG1** |
    | Name | **WGVNet1** |
    | Region | the name of any Azure region available in the subscription you will use in this lab |
    | IPv4 address space | **10.7.0.0/20** |
    | Subnet name | **Management** |
    | Subnet address range | **10.7.2.0/25** |
     | | |
      
1. Accept the defaults and click **Review and Create**. Let validation occur, and hit **Create** again to submit your deployment.

1. Once the deployment completes browse for **Virtual Networks** in the portal search bar. Within **Virtual networks** blade, click on the newly created virtual network.

1. On the virtual network blade, click **Subnets** and then click **+ Subnet**. 

1. Create a subnet with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Name | **AzureFirewallSubnet** |
    | Address range (CIDR block) | **10.7.1.0/24** |
    | Network security group | **None** |
    | Route table | **None** |
    | | |

1. Explore properties to Virtual networks.

## Exercise #01.2 - Deploy Virtual Machine for Hub (10 minutes)

1. In the Azure portal, search for and select **Virtual machines** and, on the **Virtual machines** blade, click **+ Add**.

1. On the **Basics** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | Subscription | the name of the Azure subscription you will be using in this lab |
    | Resource group | **WGVNetRG1** |
    | Virtual machine name | **WGVM1** |
    | Region | select same region the Resouce group | 
    | Availability options | **No** |
    | Image | **Ubuntu Server 18.04 LTS - Gen1** |
    | Azure Spot instance | **No** |
    | Size | **Standard_B1ms or other** |
    | Authentication type | Password |
    | Username | **admaz** |
    | Password | **Arqu1t3t0Cl0ud*** |
    | Public inbound ports | **SSH (22) and HTTP (80)** |
     | | |

1. Click **Next: Disks >** and, on the **Disks** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | OS disk type | **Standard SSD** |
    | Enable Ultra Disk compatibility | **No** |

1. Click **OK** and, back on the **Networking** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | Virtual Network | **WGVNet1** |
    | Subnet | **Default** |
    | Public IP | **WGVM1-PI** |
    | NIC network security group | **Basic** |
    | Accelerated networking | **Off** |
	| Inbound Ports | **SSH (22) and HTTP (80)** |
    | Place this virtual machine behind an existing load balancing solution? | **No** |
    | | |

1. Click **Next: Management >** and, on the **Management** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | Boot diagnostics | **Enable with custom storage account** |
    | Diagnostics storage account | create new |
    | Properties storage account | Name: **sadiag####**, Account kind: StorageV2, Performance: Standard, Replication: Locally-redundant-storage (LRS) |
    | Enable auto-shutdown | off |   
    
1. On the **Review + Create** blade, click **Create**.

1. Connect Virtual machine, in the session SSH.

1. In the terminal session, run the following for install NGINX Web Server to **WGVM1**.

   ```shell
   sudo apt-get -y update
   sudo apt-get -y install nginx
   ```

1. Within the computer, start Browser and navigate on **Public IP Address** of the **WGVM1**.

## Exercise #01.3 - Configure Azure DNS for internal name resolution for Hub (10 minutes)

1. In the Azure portal, search for and select **Private DNS zones** and, on the **Private DNS zones** blade, click **+ Add**.

1. Create a private DNS zone with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource Group | **WGVNetRG1** |
    | Name | **woodgrove.corp** |

1. Click Review and Create. Let validation occur, and hit Create again to submit your deployment.

    >**Note**: Wait for the private DNS zone to be created. This should take about 2 minutes.

1. Click **Go to resource** to open the **woodgrove.corp** DNS private zone blade.

1. On the **woodgrove.corp** private DNS zone blade, in the **Settings** section, click **Virtual network links**

1. Click **+ Add** to create a virtual network link with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Link name | **VNL-Hub** |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Virtual network | **WGVNet1** |
    | Enable auto registration | enabled |

1. Click **OK**.

    >**Note:** Wait for the virtual network link to be created. This should take less than 1 minute.

1. On the **woodgrove.corp** private DNS zone blade, in the sidebar, click **Overview**

1. Verify that the DNS records for **WGVM1** appear in the list of record sets as **Auto registered**.

    >**Note:** You might need to wait a few minutes and refresh the page if the record sets are not listed.

1. Switch to the SSH session to **WGVM1**.

1. In the Terminal console, run the following to test internal name resolution of the **WGVM1** DNS record set in the newly created private DNS zone:

   ```shell
   nslookup WGVM1.woodgrove.corp
   ```

1. Verify that the output of the command includes the private IP address of **WGVM1**.

## Exercise #02.1 - Deploy Virtual Networks for Spoke (10 minutes)

1. In the Azure portal, search for and select **Virtual networks**, and, on the **Virtual networks** blade, click **+ Add**.

1. Create a virtual network with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you will be using in this lab |
    | Resource Group | (create new) **WGVNetRG2** |
    | Name | **WGVNet2** |
    | Region | the name of any Azure region available in the subscription you will use in this lab |
    | IPv4 address space | **10.8.0.0/20** |
    | Subnet name | **AppSubnet** |
    | Subnet address range | **10.8.0.0/25** |
     | | |
      
1. Accept the defaults and click **Review and Create**. Let validation occur, and hit **Create** again to submit your deployment.

1. Once the deployment completes browse for **Virtual Networks** in the portal search bar. Within **Virtual networks** blade, click on the newly created virtual network.

1. On the virtual network blade, click **Subnets** and then click **+ Subnet**. 

1. Create a subnet with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Name | **DataSubnet** |
    | Address range (CIDR block) | **10.8.1.0/25** |
    | Network security group | **None** |
    | Route table | **None** |
    | | |

## Exercise #02.1 - Deploy Woodgrove Application for Spoke (30 minutes)

1. Deploy the template **woodgrove.json** to a new resource group. This template deploys a two virtual machines running application and database.

    > **Note:** You can deploy the template by selecting the 'Deploy to Azure' button below. You will need to create a new resource group **WGVNetRG2***. You will also need to select a location **East US or location you hava quotas for VMs**. Then choose **Review + create** followed by **Create**. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmaiaacademy%2Fworkshop-day-networking-avancado-azure%2Fmain%2FAllFiles%2Fwoodgrove.json" target="_blank">![Button to deploy the Woodgrove template to Azure.](/AllFiles/Images/deploy-to-azure.png)</a>

    > **Note:** The template will take around 15 minutes to deploy. Once template deployment is complete, several additional scripts are executed to bootstrap the lab environment. **Allow at least 30 minutes from the start of template deployment for the scripts to run.**


## Exercise #02 - Network Security groups (15 minutes)

1. In the Azure portal, search for and select **Network security groups**, and, on the **Network security groups** blade, click **+ Add**.

1. Create a network security group with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource Group | **WGVNetRG1** |
    | Name | **NSG-WEB** |
    | Region | the name of the Azure region where you deployed all other resources in this lab |

1. On the deployment blade, click **Go to resource** to open the **NSG-WEB** network security group blade. 

1. On the **NSG-WEB** network security group blade, in the **Settings** section, click **Inbound security rules**. 

1. Add an inbound rule with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Source | **Any** |
    | Source port ranges | * |
    | Destination | **Any** |
    | Destination port ranges | **22,80** |
    | Protocol | **TCP** |
    | Action | **Allow** |
    | Priority | **200** |
    | Name | **Allow-Port_22-80** |

1. On the **NSG-WEB** network security group blade, in the **Settings** section, click **Network interfaces** and then click **+ Associate**.

1. Associate the **WGVM1** network security group with the **Network interface**.

    >**Note**: It may take up to 5 minutes for the rules from the newly created Network Security Group to be applied to the Network Interface Card.

1. Go to the Azure portal to view your **Network security groups**. Search for and select Network security groups.

1. Select the name of your Network security group.

**Note:** If necessary desatch associated Network security groups.

1. In the menu bar of the network security group, under Settings, you can view the Inbound security rules, Outbound security rules, Network interfaces, and Subnets that the network security group is associated to.

1. Under **Support + troubleshooting**, you can view Effective security rules.

1. In the Desktop, start Windows PowerShell and, in the **Administrator: Windows PowerShell** window run the following to set connection test. 

   ```powershell
   Test-NetConnection -ComputerName VMPUBLICIPADDRESS -Port 80 -InformationLevel 'Detailed'
   ```
1. Examine the output of the command and verify that the connection was successful.

1. Within the computer, start Browser and navigate to **VMPUBLICIPADDPRESS**.

1. Examine the navegate was successful.

## Exercise #03 - Configure Azure VNET Peering (30 minutes)

1. In the Azure portal, search for and select **Virtual networks**, and, on the **Virtual networks** blade, click **+ Add**.

1. Create a virtual network with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you will be using in this lab |
    | Resource Group | the name of a resource group **RG-Network-Spoke1** |
    | Name | **VNET-Spoke1** |
    | Region | West US 2 |
    | IPv4 address space | **172.16.0.0/16** |
    | Subnet name | **Default** |
    | Subnet address range | **172.16.1.0/24** |
     | | |
      
1. Accept the defaults and click **Review and Create**. Let validation occur, and hit **Create** again to submit your deployment.

1. Once the deployment completes browse for **Virtual Networks** in the portal search bar. Within **Virtual networks** blade, click on the newly created virtual network.

1. In the Azure portal, search for and select **Virtual machines**

1. On the **Virtual machines**, click **Add** and create a new Ubuntu Server Virtual machine, on the **VMSpoke1**.

1. On the **Private DNS Zones**, click **Add** and create a new **Virtual networking links**, on the **VNET-Spoke1**.

1. In the Azure portal, search for and select **Virtual networks**.

1. In the list of virtual networks, click **VNET-Spoke1**.

1. On the **VNET-Spoke1** virtual network blade, in the **Settings** section, click **Peerings** and then click **+ Add**.

1. Specify the following settings (leave others with their default values) and click **Add**:

    | Setting | Value|
    | --- | --- |
    | This virtual network: Peering link name | **To-Hub** |
    | This virtual network: Traffic to remote virtual network | **Allow (default)** |
    | This virtual network: Traffic forwarded from remote virtual network | **Allow (default)** |
    | Virtual network gateway | **None** |
    | Remote virtual network: Peering link name | **To-Spoke1** |    
    | Virtual network deployment model | **Resource manager** |
    | I know my resource ID | unselected |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Virtual network | **WGVNet1** |
    | Traffic to remote virtual network | **Allow (default)** |
    | Traffic forwarded from remote virtual network | **Allow (default)** |
    | Virtual network gateway | **None** |

1. On the **WGVNet1** virtual network blade, in the **Settings** section, click **Peerings** and then click **+ Add**.

1. Add a peering with the following settings (leave others with their default values):

    | Setting | Value|
    | --- | --- |
    | This virtual network: Peering link name | **To-Spoke1** |
    | This virtual network: Traffic to remote virtual network | **Allow (default)** |
    | This virtual network: Traffic forwarded from remote virtual network | ****Allow (default)**** |
    | Virtual network gateway | **None** |
    | Remote virtual network: Peering link name | **To-Hub** |    
    | Virtual network deployment model | **Resource manager** |
    | I know my resource ID | unselected |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Virtual network | **WGVNet1** |
    | Traffic to remote virtual network | **Allow (default)** |
    | Traffic forwarded from remote virtual network | **Allow (default)** |
    | Virtual network gateway | **None** |

1. At the top of the Azure portal, enter the name of a **VMSpoke1** that is in the running state, in the search box. When the name of the VM appears in the search results, select it.

1. Under Settings on the left, select **Networking**, and navigate to the network interface resource by selecting its name. View network interfaces.

1. On the left, select **Effective routes**. The effective routes for a network interface are shown.
    
1. Connect Spoke1 Virtual Machine, in the session SSH.

1. In the terminal session, run the following to test connectivity to **VNET-Spoke1**.

   ```shell
   telnet VMHUBIPADDRESS 22
   ```
    >**Note**: The test uses TCP 22 since this is this port is allowed by default by operating system firewall. 

1. Examine the output of the command and verify that the connection was successful.

1. Switch in Hub Virtual machine, run the following to test connectivity to **WGVM1** 

   ```shell
   telnet VMSPOKE1IPADDRESS 22
   ```
1. Examine the output of the command and verify that the connection was successful.

## Project - Deploy Hub-spoke Archicture (60 minutes)

Implement a Hub-spoke topology

   ![Screenshot of the Hub-spoke](/AllFiles/Images/Hub-Spoke.png)

**Important Notes**
- Three Virtual Networks;
- Gateway VPN on the Hub network;
- VNET Peering connection Hub to spoke and vice verse;
- OPTIONAL: Azure Bastion on the Hub network;
- Azure Firewall on the Hub network;
- Custom Route tables to address prefix "Address Space networking destination" and next hop type to virtual applicance;
- Network rule Azure Firewall all internal traffic.

References: [Hub-spoke network topology](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)

1. End of day and **Workshop Day #02**.

1. Continue in the **Mentoria Arquiteto Cloud**.