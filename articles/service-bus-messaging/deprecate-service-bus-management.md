---
title: Azure 訊息服務-Service Manager 至 Resource Manager
description: 本文提供已淘汰的 Azure Service Manager REST API & PowerShell Cmdlet 對應至 Resource Manager REST API & PowerShell Cmdlet。
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589905"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Azure Service Manager 支援 Azure 服務匯流排、轉送和事件中樞

Resource Manager，我們下一代的雲端基礎結構堆疊會完全取代「傳統」的 Azure 服務管理模型（傳統部署模型）。 因此，傳統部署模型 REST Api 和服務匯流排、轉送和事件中樞的支援將于2021年11月1日淘汰。 這項淘汰最初是在[Microsoft Tech 社區公告](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)上宣佈的，但我們最近決定在原始公告的時間延長淘汰期間兩年。 為了方便識別，這些 Api 在其 URI 中有 `management.core.windows.net`。 請參閱下表，以取得已淘汰的 Api 及其 Azure Resource Manager API 版本的清單，供您立即使用。

若要繼續使用服務匯流排、轉送和事件中樞，請在2021年10月31日前移至 Resource Manager。 我們鼓勵所有仍在使用舊 Api 的客戶儘快切換，以利用 Resource Manager 的額外優點，包括資源群組、標籤、簡化的部署和管理程式，以及更細緻的存取使用角色型存取控制（RBAC）來控制。

如需有關 Azure Resource Manager 與 Azure Service Manager 的詳細資訊，請參閱[TechNet Blog](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)（英文）。

如需適用于 Azure 服務匯流排、轉送和事件中樞之 Service Manager 和 Resource Manager Api 的詳細資訊，請參閱我們的 REST API 檔：

- [Azure 服務匯流排](/rest/api/servicebus/)
- [Azure 事件中樞](/rest/api/eventhub/)
- [Azure 轉送](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Service Manager REST API Resource Manager REST API

| Service Manager Api （已淘汰） | Resource Manager 服務匯流排 API | Resource Manager 事件中樞 API | Resource Manager-轉送 API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **命名空間-GetNamespaceAsync** <br/>[取得命名空間服務匯流排](/rest/api/servicebus/get-namespace)<br/>[事件中樞取得命名空間](/rest/api/eventhub/get-event-hub)<br/>[轉送取得命名空間](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [獲取](/rest/api/servicebus/namespaces/get) | [獲取](/rest/api/eventhub/namespaces/get) | [獲取](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>服務匯流排/事件中樞/轉送 GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/namespaces/listkeys) | [listkeys](/rest/api/eventhub/namespaces/listkeys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **主題-GetTopicsAsync**<br/>服務匯流排<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **佇列-GetQueueAsync** <br/>服務匯流排<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [獲取](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **轉送-GetRelaysAsync**<br/>[取得轉送](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>服務匯流排/事件中樞/轉送 GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/namespaces/getauthorizationrule) |[getauthorizationrule](/rest/api/eventhub/namespaces/getauthorizationrule) | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **命名空間-DeleteNamespaceAsync**<br/>[服務匯流排刪除命名空間](/rest/api/servicebus/delete-namespace)<br/>[事件中樞刪除命名空間](/rest/api/eventhub/delete-event-hub)<br/>[轉送刪除命名空間](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [delete](/rest/api/servicebus/namespaces/delete) | [delete](/rest/api/eventhub/namespaces/delete) | [delete](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-GetPlanAsync**<br/>服務匯流排/事件中樞/轉送取得命名空間<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [獲取](/rest/api/servicebus/namespaces/get) | [獲取](/rest/api/eventhub/namespaces/get) | [獲取](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>服務匯流排/事件中樞/轉送取得命名空間<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNamespaceAuthorizationRuleAsync**<br/>服務匯流排/事件中樞/轉送取得命名空間<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
服務匯流排/事件中樞/轉送<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[取得命名空間服務匯流排](/rest/api/servicebus/get-namespace)<br/>[取得命名空間事件中樞](/rest/api/eventhub/get-event-hub)<br/>[轉送取得命名空間](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [獲取](/rest/api/servicebus/namespaces/get) | [獲取](/rest/api/eventhub/namespaces/get) | [獲取](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>服務匯流排/EventHub/轉送取得命名空間<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | [listbysku](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>服務匯流排/EventHub/轉送<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[清單事件中樞](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[取得事件中樞](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [獲取](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>服務匯流排/事件中樞/轉送<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>服務匯流排/EventHub/轉送<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauthorizationrules](/rest/api/servicebus/namespaces/listauthorizationrules) | [listauthorizationrules](/rest/api/eventhub/namespaces/listauthorizationrules) | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceAvailable**<br/>[服務匯流排命名空間可用性](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/namespaces/checknameavailability) | [checknameavailability](/rest/api/eventhub/namespaces/checknameavailability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **命名空間-CreateOrUpdateNamespaceAsync**<br/>服務匯流排/事件中樞/轉送<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **主題-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [獲取](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Service Manager PowerShell-Resource Manager PowerShell
| Service Manager PowerShell 命令（已淘汰） | 新的 Resource Manager 命令 | 較新的 Resource Manager 命令 |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-azurermservicebusauthorizationrule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [AzureRmServiceBusGeoDRConfiguration](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [AzServiceBusGeoDRConfiguration](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [新增-Get-azuresbauthorizationrule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [新增-Get-azurermservicebusauthorizationrule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [新增-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [新增-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [新增-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [移除-AzureRmRelayAuthorizationRule](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [移除-Get-azurermeventhubauthorizationrule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [移除-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [移除-New-azuresbnamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [移除-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [移除-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [設定-Get-azuresbauthorizationrule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [設定-Get-azurermservicebusauthorizationrule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [設定-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>後續步驟
請參閱下列文件： 

- 最新的 REST API 檔
    - [Azure 服務匯流排](/rest/api/servicebus/)
    - [Azure 事件中樞](/rest/api/eventhub/)
    - [Azure 轉送](/rest/api/relay/)
- 最新的 PowerShell 檔
    - [Azure 服務匯流排](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Azure 事件中樞](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [事件格線](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
