---
title: 教學課程：將受控實例新增至容錯移轉群組
description: 瞭解如何為您的 Azure SQL Database 受控實例設定容錯移轉群組。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: bf83155e971061f22e5f5fc33d216b58621c9249
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462644"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>教學課程：將 SQL Database 受控實例新增至容錯移轉群組

將 SQL Database 受控實例新增至容錯移轉群組。 在本文中，您將了解如何：

> [!div class="checklist"]
> - 建立主要受控實例
> - 建立次要受控實例作為[容錯移轉群組](sql-database-auto-failover-group.md)的一部分。 
> - 測試容錯移轉

  > [!NOTE]
  > - 進行本教學課程時，請確定您是使用[為受控實例設定容錯移轉群組的必要條件來設定](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)您的資源。 
  > - 建立受控實例可能需要很長的時間。 因此，本教學課程可能需要數小時才能完成。 如需布建時間的詳細資訊，請參閱[受控實例管理作業](sql-database-managed-instance.md#managed-instance-management-operations)。 
  > - 參與容錯移轉群組的受控實例需要[ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)或兩個連線的 VPN 閘道。 本教學課程提供建立和連接 VPN 閘道的步驟。 如果您已設定 ExpressRoute，請略過這些步驟。 


## <a name="prerequisites"></a>必要條件

# <a name="portal"></a>[入口網站](#tab/azure-portal)
若要完成本教學課程，請確定您具有下列項目︰ 

- Azure 訂用帳戶。 如果您還沒有帳戶，請[建立一個免費帳戶](https://azure.microsoft.com/free/)。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要完成本教學課程，請確定您有下列專案：

- Azure 訂用帳戶。 如果您還沒有帳戶，請[建立一個免費帳戶](https://azure.microsoft.com/free/)。
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1-建立資源群組和主要受控實例
在此步驟中，您將使用 Azure 入口網站或 PowerShell，為您的容錯移轉群組建立資源群組和主要受控實例。 


# <a name="portal"></a>[入口網站](#tab/azure-portal) 

使用 Azure 入口網站建立資源群組和您的主要受控實例。 

1. 在 Azure 入口網站的左側功能表中，選取 [Azure SQL]。 如果**AZURE SQL**不在清單中，請選取 [**所有服務**]，然後在搜尋方塊中輸入 azure sql。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取 [+ 新增] 以開啟 [選取 SQL 部署選項] 頁面。 您可以選取 [資料庫] 磚上的 [顯示詳細資料]，以查看不同資料庫的其他資訊。
1. 選取 [ **SQL 受控實例**] 圖格上的 [**建立**]。 

    ![選取受控實例](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. 在 [**建立 Azure SQL Database 受控執行個體**] 頁面上的 [**基本**] 索引標籤
    1. 在 [**專案詳細資料**] 下，從下拉式選單選取您的**訂**用帳戶，然後選擇 [**建立新**的資源群組]。 輸入資源群組的名稱，例如 [`myResourceGroup`]。 
    1. 在 [**受控執行個體詳細資料**] 下，提供受控實例的名稱，以及您想要部署受控實例的區域。 將 [**計算 + 儲存體**] 保留為 [預設值]。 
    1. 在 [**系統管理員帳戶**] 下，提供管理員登入（例如 `azureuser`）和複雜的管理員密碼。 

    ![建立主要 MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. 將其餘設定保留為預設值，然後選取 [**審查 + 建立**] 來檢查您的受控實例設定。 
1. 選取 [**建立**] 以建立您的主要受控實例。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 建立您的資源群組和主要受控實例。 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary managed instance created successfully."
   ```

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立 Azure 資源群組。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 對虛擬網路新增子網路組態。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得資源群組中的虛擬網路。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路中的子網路。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 建立網路安全性群組。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 建立路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虛擬網路的子網路組態。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虛擬網路。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 取得網路安全性群組。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 將網路安全性規則設定新增到網路安全性群組中。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新網路安全性群組。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 將路由新增到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 建立 Azure SQL Database 受控執行個體。  |

---

## <a name="2---create-secondary-virtual-network"></a>2-建立次要虛擬網路
如果您使用 Azure 入口網站建立受控實例，您必須另外建立虛擬網路，因為主要和次要受控實例的子網不會有重迭的範圍。 如果您使用 PowerShell 來設定受控實例，請跳至步驟3。 

# <a name="portal"></a>[入口網站](#tab/azure-portal) 
若要確認主要虛擬網路的子網範圍，請遵循下列步驟：
1. 在  [Azure 入口網站](https://portal.azure.com)中，流覽至您的資源群組，然後選取主要實例的虛擬網路。 
1. 選取 [**設定**] 底下的 [**子網**]，並記下**位址範圍**。 次要受控實例之虛擬網路的子網位址範圍不能重迭。 


   ![主要子網](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

若要建立虛擬網路，請遵循下列步驟：

1. 在  [Azure 入口網站](https://portal.azure.com)中，選取 **建立資源**] 並搜尋 [*虛擬網路*]。 
1. 選取 Microsoft 所發行的**虛擬網路**選項，然後在下一頁選取 [**建立**]。 
1. 填寫必要欄位，為您的次要受控實例設定虛擬網路，然後選取 [**建立**]。 

   下表顯示次要虛擬網路所需的值：

    | **欄位** | 值 |
    | --- | --- |
    | **名稱** |  次要受控實例所要使用之虛擬網路的名稱，例如 `vnet-sql-mi-secondary`。 |
    | **位址空間** | 虛擬網路的位址空間，例如 `10.128.0.0/16`。 | 
    | **訂用帳戶** | 主要受控實例和資源群組所在的訂用帳戶。 |
    | **區域** | 您將部署次要受控實例的位置。 |
    | **子網路** | 子網的名稱。 預設會為您提供 `default`。 |
    | **位址範圍**| 子網的位址範圍。 這必須與主要受控實例的虛擬網路所使用的子網位址範圍不同，例如 `10.128.0.0/24`。  |
    | &nbsp; | &nbsp; |

    ![次要虛擬網路值](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

只有當您使用 Azure 入口網站來部署受控實例時，才需要執行此步驟。 如果您使用的是 PowerShell，請直接跳到步驟3。 

---

## <a name="3---create-a-secondary-managed-instance"></a>3-建立次要受控實例
在此步驟中，您將在 Azure 入口網站中建立次要受控實例，這也會設定兩個受控實例之間的網路功能。 

您的第二個受控實例必須：
- 是空的。 
- 具有與主要受控實例不同的子網和 IP 範圍。 

# <a name="portal"></a>[入口網站](#tab/azure-portal) 

使用 Azure 入口網站建立次要受控實例。 

1. 在 Azure 入口網站的左側功能表中，選取 [Azure SQL]。 如果**AZURE SQL**不在清單中，請選取 [**所有服務**]，然後在搜尋方塊中輸入 azure sql。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取 [+ 新增] 以開啟 [選取 SQL 部署選項] 頁面。 您可以選取 [資料庫] 磚上的 [顯示詳細資料]，以查看不同資料庫的其他資訊。
1. 選取 [ **SQL 受控實例**] 圖格上的 [**建立**]。 

    ![選取受控實例](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. 在 [**建立 Azure SQL Database 受控執行個體**] 頁面的 [**基本**] 索引標籤上，填寫必要的欄位以設定次要受控實例。 

   下表顯示次要受控實例所需的值：
 
    | **欄位** | 值 |
    | --- | --- |
    | **訂用帳戶** |  您的主要受控實例所在的訂用帳戶。 |
    | **資源群組**| 您的主要受控實例所在的資源群組。 |
    | **受控執行個體名稱** | 新次要受控實例的名稱，例如 `sql-mi-secondary`  | 
    | **區域**| 次要受控實例的位置。  |
    | **受控執行個體系統管理員登入** | 您想要用於新的次要受控實例的登入，例如 `azureuser`。 |
    | **密碼** | 新的次要受控實例的系統管理員登入所使用的複雜密碼。  |
    | &nbsp; | &nbsp; |

1. 在 **網路**功能 索引標籤下，針對 **虛擬網路**，從下拉式選單選取您為次要受控實例所建立的虛擬網路。

   ![次要 MI 網路](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. 在 [**其他設定**] 索引標籤下，針對 [**異地**複寫] 選擇 **[是]** ，_做為容錯移轉次要資料庫使用_。 從下拉式選單中選取 [主要受控實例]。 
    1. 請確定定序和時區符合主要受控實例的範圍。 在本教學課程中建立的主要受控實例會使用 `SQL_Latin1_General_CP1_CI_AS` 定序和 `(UTC) Coordinated Universal Time` 時區的預設值。 

   ![次要 MI 網路](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. 選取 [審核] [ **+ 建立**] 來檢查次要受控實例的設定。 
1. 選取 [**建立**] 以建立次要受控實例。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 建立次要受控實例。 

   ```powershell-interactive
   # Configure secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary managed instance created successfully."
   ```

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立 Azure 資源群組。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 對虛擬網路新增子網路組態。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得資源群組中的虛擬網路。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路中的子網路。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 建立網路安全性群組。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 建立路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虛擬網路的子網路組態。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虛擬網路。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 取得網路安全性群組。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 將網路安全性規則設定新增到網路安全性群組中。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新網路安全性群組。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 將路由新增到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 建立 Azure SQL Database 受控執行個體。  |

---

## <a name="4---create-primary-gateway"></a>4-建立主要閘道 
若要讓兩個受控實例參與容錯移轉群組，必須在兩個受控實例的虛擬網路之間設定 ExpressRoute 或閘道，以允許網路通訊。 如果您選擇設定[ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) ，而不是連接兩個 VPN 閘道，請直接跳至[步驟 7](#7---create-a-failover-group)。  

本文提供建立兩個 VPN 閘道並加以連線的步驟，但如果您已設定 ExpressRoute，可以直接跳到建立容錯移轉群組。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站，為您的主要受控實例的虛擬網路建立閘道。 


1. 在  [Azure 入口網站](https://portal.azure.com)中，移至您的資源群組，然後選取主要受控實例的**虛擬網路**資源。 
1. 選取 [**設定**] 底下的 [**子網**]，然後選取以新增新的**閘道子網**。 保留預設值。 

   ![為主要受控實例新增閘道](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 建立子網閘道之後，從左側流覽窗格中選取 [**建立資源**]，然後在搜尋方塊中輸入 `Virtual network gateway`。 選取**Microsoft**發佈的**虛擬網路閘道**資源。 

   ![建立新的虛擬網路閘道](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 填寫必要欄位，以設定您的主要受控實例閘道。 

   下表顯示主要受控實例的閘道所需的值：
 
    | **欄位** | 值 |
    | --- | --- |
    | **訂用帳戶** |  您的主要受控實例所在的訂用帳戶。 |
    | **名稱** | 虛擬網路閘道的名稱，例如 `primary-mi-gateway`。 | 
    | **區域** | 次要受控實例所在的區域。 |
    | **閘道類型** | 選取 [VPN]。 |
    | **VPN 類型** | 選取以**路由為基礎的** |
    | **SKU**| 保留預設值 [`VpnGw1`]。 |
    | **位置**| 您的主要受控實例和主要虛擬網路所在的位置。   |
    | **虛擬網路**| 選取在第2節中建立的虛擬網路，例如 `vnet-sql-mi-primary`。 |
    | **公用 IP 位址**| 選取 [建立新的]。 |
    | **公用 IP 位址名稱**| 輸入 IP 位址的 [名稱]，例如 `primary-gateway-IP`。 |
    | &nbsp; | &nbsp; |

1. 將其他值保留為 [預設]，然後選取 [**檢查 + 建立**] 來檢查虛擬網路閘道的設定。

   ![主要閘道設定](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. 選取 [**建立**] 以建立新的虛擬網路閘道。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 為您的主要受控實例的虛擬網路建立閘道。 

   ```powershell-interactive
   # Create primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得資源群組中的虛擬網路。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 對虛擬網路新增子網路組態。 | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虛擬網路。  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路中的子網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 建立虛擬網路閘道的 IP 組態 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 建立虛擬網路閘道 |


---


## <a name="5---create-secondary-gateway"></a>5-建立次要閘道 
在此步驟中，請使用 Azure 入口網站，為次要受控實例的虛擬網路建立閘道。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站，重複上一節中的步驟，以建立次要受控實例的虛擬網路子網和閘道。 填寫必要欄位，為您的次要受控實例設定閘道。 

   下表顯示次要受控實例的閘道所需的值：

   | **欄位** | 值 |
   | --- | --- |
   | **訂用帳戶** |  次要受控實例所在的訂用帳戶。 |
   | **名稱** | 虛擬網路閘道的名稱，例如 `secondary-mi-gateway`。 | 
   | **區域** | 次要受控實例所在的區域。 |
   | **閘道類型** | 選取 [VPN]。 |
   | **VPN 類型** | 選取以**路由為基礎的** |
   | **SKU**| 保留預設值 [`VpnGw1`]。 |
   | **位置**| 次要受控實例和次要虛擬網路所在的位置。   |
   | **虛擬網路**| 選取在第2節中建立的虛擬網路，例如 `vnet-sql-mi-secondary`。 |
   | **公用 IP 位址**| 選取 [建立新的]。 |
   | **公用 IP 位址名稱**| 輸入 IP 位址的 [名稱]，例如 `secondary-gateway-IP`。 |
   | &nbsp; | &nbsp; |

   ![次要閘道設定](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 為次要受控實例的虛擬網路建立閘道。 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得資源群組中的虛擬網路。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 對虛擬網路新增子網路組態。 | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虛擬網路。  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路中的子網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 建立虛擬網路閘道的 IP 組態 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 建立虛擬網路閘道 |

---


## <a name="6---connect-the-gateways"></a>6-連接閘道
在此步驟中，請在兩個虛擬網路的兩個閘道之間建立雙向連線。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)

使用 Azure 入口網站來連接這兩個閘道。 


1. 從 [ [Azure 入口網站](https://portal.azure.com)中選取 [**建立資源**]。
1. 在搜尋方塊中輸入 `connection`，然後按 enter 鍵進行搜尋，這會帶您前往 Microsoft 所發佈的**連線資源。**
1. 選取 [**建立**] 以建立連接。 
1. 在 [**基本**] 索引標籤上，選取下列值，然後選取 **[確定]** 。 
    1. 針對 [連線**類型**] 選取 [`VNet-to-VNet`]。 
    1. 從下拉式清單中選取訂用帳戶。 
    1. 在下拉式選單中，選取受控實例的資源群組。 
    1. 從下拉式選單選取主要受控實例的位置 
1. 在 [**設定**] 索引標籤上，選取或輸入下列值，然後選取 **[確定]** ：
    1. 選擇**第一個虛擬網路閘道**的主要網路閘道，例如 [`Primary-Gateway`]。  
    1. 選擇**第二個虛擬網路閘道**的 [次要網路閘道]，例如 [`Secondary-Gateway`]。 
    1. 選取 [**建立雙向連接**] 旁的核取方塊。 
    1. 請保留預設的主要連線名稱，或將它重新命名為您選擇的值。 
    1. 提供連接的**共用金鑰（PSK）** ，例如 `mi1m2psk`。 

   ![建立閘道連線](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. 在 [**摘要**] 索引標籤上，檢查雙向連線的設定，然後選取 **[確定]** 以建立您的連線。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 來連接這兩個閘道。 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 建立兩個虛擬網路閘道之間的連線。   |

---


## <a name="7---create-a-failover-group"></a>7-建立容錯移轉群組
在此步驟中，您將建立容錯移轉群組，並將這兩個受控實例新增至其中。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)
使用 Azure 入口網站建立容錯移轉群組。 


1. 在 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [Azure SQL]。 如果**AZURE SQL**不在清單中，請選取 [**所有服務**]，然後在搜尋方塊中輸入 azure sql。 (選用) 選取 **Azure SQL** 旁的星號將其設為最愛，並新增為左側導覽中的項目。 
1. 選取您在第一節中建立的主要受控實例，例如 `sql-mi-primary`。 
1. 在 [**設定**] 下，流覽至 [**實例容錯移轉群組**]，然後選擇 [**新增群組**] 以開啟 [**實例容錯移轉群組**] 頁面。 

   ![新增容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. 在 [**實例容錯移轉群組**] 頁面上，輸入容錯移轉群組的名稱（例如 `failovergrouptutorial`），然後從下拉式選單選擇次要受控實例，例如 [`sql-mi-secondary`]。 選取 [**建立**] 以建立您的容錯移轉群組。 

   ![建立容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 容錯移轉群組部署完成後，您會回到 [**容錯移轉群組**] 頁面。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 建立容錯移轉群組。 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 建立新的 Azure SQL Database 受控執行個體容錯移轉群組。  |


---


## <a name="8---test-failover"></a>8-測試容錯移轉
在此步驟中，您會將容錯移轉群組容錯移轉到次要伺服器，然後使用 Azure 入口網站進行容錯回復。 


# <a name="portal"></a>[入口網站](#tab/azure-portal)
使用 Azure 入口網站測試容錯移轉。 


1. 流覽至[Azure 入口網站](https://portal.azure.com)內您的_次要_受控實例，然後選取 [設定] 底下的 [**實例容錯移轉群組**]。 
1. 檢查哪個受控實例是主要複本，以及哪個受控實例是次要複本。 
1. 選取 [**容錯移轉**]，然後在關於 TDS 會話中斷連線的警告上選取 **[是]** 。 

   ![容錯移轉群組](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 檢查哪一個受控實例是主要複本，哪個實例是次要複本。 如果故障轉換成功，這兩個實例應該已切換角色。 

   ![受控實例在容錯移轉後已切換角色](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. 移至新的_次要_受控實例，然後再次選取 [**容錯移轉**]，將主要實例容錯回復至主要角色。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 測試容錯移轉。 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


將容錯移轉群組還原至主要伺服器：

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | 取得或列出受控執行個體容錯移轉群組。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | 執行受控執行個體容錯移轉群組的容錯移轉。 | 

---



## <a name="clean-up-resources"></a>清除資源
藉由先刪除受控實例、虛擬叢集、任何剩餘的資源，以及最後的資源群組來清除資源。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 瀏覽至您在 [Azure 入口網站](https://portal.azure.com)中的資源群組。 
1. 選取受控執行個體，然後選取 [刪除]。 在文字方塊中輸入 `yes` 以確認您要刪除資源，然後選取 [刪除]。 此程式可能需要一些時間才能在背景中完成，而且在完成之前，您將無法刪除*虛擬叢集*或任何其他相依資源。 監視 [活動] 索引標籤中的刪除，確認您的受控執行個體已刪除。 
1. 受控執行個體刪除後，請在資源群組中選取虛擬叢集，然後選擇 [刪除]，加以刪除。 在文字方塊中輸入 `yes` 以確認您要刪除資源，然後選取 [刪除]。 
1. 刪除任何剩餘的資源。 在文字方塊中輸入 `yes` 以確認您要刪除資源，然後選取 [刪除]。 
1. 選取 [刪除資源群組]、輸入資源群組的名稱 `myResourceGroup`，然後選取 [刪除]，以刪除資源群組。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

您將需要移除資源群組兩次。 第一次移除資源群組時，將會移除受控執行個體和虛擬叢集，但接著會失敗並出現錯誤訊息：`Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`。 請再次執行 Remove-AzResourceGroup 命令，以移除所有剩餘的資源及資源群組。

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

本教學課程的這個部分會使用下列 PowerShell Cmdlet：

| Command | 注意 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組。 |

---

## <a name="full-script"></a>完整指令碼

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立 Azure 資源群組。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 對虛擬網路新增子網路組態。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得資源群組中的虛擬網路。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路中的子網路。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 建立網路安全性群組。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 建立路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虛擬網路的子網路組態。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虛擬網路。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 取得網路安全性群組。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 將網路安全性規則設定新增到網路安全性群組中。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新網路安全性群組。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 將路由新增到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 建立 Azure SQL Database 受控執行個體。  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| 傳回 Azure SQL 受控資料庫執行個體的資訊。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 建立虛擬網路閘道的 IP 組態 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 建立虛擬網路閘道 |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 建立兩個虛擬網路閘道之間的連線。   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 建立新的 Azure SQL Database 受控執行個體容錯移轉群組。  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | 取得或列出受控執行個體容錯移轉群組。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | 執行受控執行個體容錯移轉群組的容錯移轉。 | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組。 | 

# <a name="portal"></a>[入口網站](#tab/azure-portal) 

沒有可供 Azure 入口網站使用的腳本。

---

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定兩個受控實例之間的容錯移轉群組。 您已了解如何︰

> [!div class="checklist"]
> - 建立主要受控實例
> - 建立次要受控實例作為[容錯移轉群組](sql-database-auto-failover-group.md)的一部分。 
> - 測試容錯移轉

前往下一個快速入門，以瞭解如何連線到您的受控實例，以及如何將資料庫還原到您的受控實例： 

> [!div class="nextstepaction"]
> 連線[到您的受控實例](sql-database-managed-instance-configure-vm.md)
> 將[資料庫還原至受控實例](sql-database-managed-instance-get-started-restore.md)


