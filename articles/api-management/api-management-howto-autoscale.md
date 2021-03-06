---
title: 設定 Azure API 管理執行個體的自動調整 | Microsoft Docs
description: 本主題描述如何設定 Azure API 管理執行個體的自動調整行為。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 8c1c96fdb1f4f42c7592791881b855f74d411171
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018274"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>自動調整 Azure API 管理執行個體  

Azure API 管理服務執行個體可以根據一組規則進行自動調整。 此行為可透過 Azure 監視器來啟用及設定，而且只能在 Azure API 管理服務的**標準**與**進階**層中加以支援。

本文將逐步解說設定自動調整的程序，並建議自動調整規則的最佳設定。

> [!NOTE]
> 取用層中的 API管理服務會根據流量自動調整, 而不需要任何額外的設定。

## <a name="prerequisites"></a>必要條件

若要依照本文中的步驟進行，您必須：

+ 擁有有效的 Azure 訂用帳戶。
+ 擁有 Azure API 管理執行個體。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 了解 [Azure API 管理執行個體的容量](api-management-capacity.md)的概念。
+ 了解 [Azure API 管理執行個體的手動調整程序](upgrade-and-scale.md)，包括成本後果。

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Azure API 管理自動調整限制

設定自動調整行為之前，需要先考量調整決策的某些限制與後果。

+ 自動調整僅能針對 Azure API 管理服務的**標準**與**進階**層加以啟用。
+ 定價層也會指定服務執行個體的單位數目上限。
+ 調整程序至少需要 20 分鐘的時間。
+ 如果服務已由另一個作業鎖定，調整要求將會失敗並自動重試。
+ 如果服務具有多區域部署，則只能調整**主要位置**的單位。 無法調整其他位置的單位。

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>啟用及設定 Azure API 管理服務的自動調整

請依照下列步驟來設定 Azure API 管理服務的自動調整：

1. 在 Azure 入口網站中，瀏覽至**監視器**執行個體。

    ![Azure 監視器](media/api-management-howto-autoscale/01.png)

2. 從左側功能表中選取 [自動調整]。

    ![Azure 監視器的自動調整資源](media/api-management-howto-autoscale/02.png)

3. 在下拉式功能表中，根據篩選條件找出您的 Azure API 管理服務。
4. 選取所需的 Azure API 管理服務執行個體。
5. 在新開啟的區段中，按一下 [啟用自動調整規模] 按鈕。

    ![Azure 監視器的啟用自動調整](media/api-management-howto-autoscale/03.png)

6. 在 [規則] 區段中，按一下 [+ 新增規則]。

    ![Azure 監視器中自動調整的新增規則](media/api-management-howto-autoscale/04.png)

7. 定義新的相應放大規則。

   例如，當過去 30 分鐘的平均容量計量超過 80% 時，相應放大規則可能會觸發 Azure API 管理單位的增加。 下表提供這類規則的設定。

    | 參數             | 值             | 注意                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 計量來源         | 目前的資源  | 根據目前的 Azure API 管理資源計量定義規則。                                                                                                                                                                                                     |
    | *準則*            |                   |                                                                                                                                                                                                                                                                                 |
    | 時間彙總      | Average           |                                                                                                                                                                                                                                                                                 |
    | 計量名稱           | 容量          | 容量計量是一個 Azure API 管理計量，可反映 Azure API 管理執行個體的資源使用量。                                                                                                                                                            |
    | 時間粒度統計資料  | Average           |                                                                                                                                                                                                                                                                                 |
    | 運算子              | 大於      |                                                                                                                                                                                                                                                                                 |
    | 閾值             | 80%               | 平均容量計量的閾值。                                                                                                                                                                                                                                 |
    | 持續時間 (分鐘) | 30                | 平均容量計量的時間範圍僅適用於使用模式。 時間週期越長，反應將更順暢；間歇性尖峰對相應放大決策所造成的影響將更低。 不過，它也會延遲相應放大觸發程序。 |
    | *動作*              |                   |                                                                                                                                                                                                                                                                                 |
    | 運算             | 將計數增加 |                                                                                                                                                                                                                                                                                 |
    | 執行個體計數        | 1                 | 將 Azure API 管理執行個體相應放大 1 個單位。                                                                                                                                                                                                                          |
    | 緩和時間 (分鐘)   | 60                | 至少需要 20 分鐘的時間，才能將 Azure API 管理服務相應放大。在大部分情況下，60 分鐘的緩和期可防止觸發多個相應放大作業。                                                                                                  |

8. 按一下 [新增] 以儲存規則。

    ![Azure 監視器的相應放大規則](media/api-management-howto-autoscale/05.png)

9. 再按一下 [+ 新增規則]。

    這次，需定義相應縮小規則。 它將可在 API 的使用量減少時，確保資源不會浪費。

10. 定義新的相應縮小規則。

    例如，當過去 30 分鐘的平均容量計量低於 35% 時，相應縮小規則可能會觸發 Azure API 管理單位的移除。 下表提供這類規則的設定。

    | 參數             | 值             | 注意                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 計量來源         | 目前的資源  | 根據目前的 Azure API 管理資源計量定義規則。                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *準則*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 時間彙總      | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 計量名稱           | 容量          | 與針對相應放大規則所使用之容量相同的計量。                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | 時間粒度統計資料  | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 運算子              | 小於         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 閾值             | 35%               | 與相應放大規則類似，此值絕大部分取決於 Azure API 管理的使用模式。 |
    | 持續時間 (分鐘) | 30                | 與針對相應放大規則所使用之持續時間相同的值。                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *動作*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 運算             | 將計數減少 | 相對於針對相應放大規則所使用的作業。                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | 執行個體計數        | 1                 | 與針對相應放大規則所使用之持續時間相同的值。                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | 緩和時間 (分鐘)   | 90                | 相應縮小應該比相應放大更保守，因此緩和期應該更長。                                                                                                                                                                                                                                                                                                                                                                                                    |

11. 按一下 [新增] 以儲存規則。

    ![Azure 監視器的相應縮小規則](media/api-management-howto-autoscale/06.png)

12. 設定 Azure API 管理單位的**最大**數目。

    > [!NOTE]
    > Azure API 管理具有執行個體可相應放大的單位限制。 此限制取決於服務層級。

    ![Azure 監視器的相應縮小規則](media/api-management-howto-autoscale/07.png)

13. 按一下 [儲存]。 您已設定自動調整。

## <a name="next-steps"></a>後續步驟

+ [如何將 Azure API 管理服務執行個體部署到多個 Azure 區域](api-management-howto-deploy-multi-region.md)
