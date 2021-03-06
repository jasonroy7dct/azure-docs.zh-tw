---
title: 部署 ISO 27001 藍圖範例
description: 獨立 ISO 27001 藍圖範例的部署步驟，其中包括藍圖成品參數的詳細資料。
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: f5920541d49c454bd6648318c04ed607eada1dfb
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922513"
---
# <a name="deploy-the-iso-27001-blueprint-sample"></a>部署 ISO 27001 藍圖範例

若要部署 Azure 藍圖 ISO 27001 藍圖範例，您必須採取下列步驟：

> [!div class="checklist"]
> - 從範例建立新的藍圖
> - 將您的範例複本標記為**已發佈**
> - 將您的藍圖複本指派給現有的訂用帳戶

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>從範例建立藍圖

首先，若要實作藍圖範例，請使用範例作為起點，在您的環境中建立新藍圖。

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 在左側的 [快速入門]  頁面上，選取 [建立藍圖]  **下方的 [建立]** 按鈕。

1. 在 [其他範例]  下方尋找 **ISO 27001** 藍圖範例，然後選取 [使用此範例]  。

1. 輸入藍圖範例的 [基本資料]  ：

   - **藍圖名稱**：為 ISO 27001 藍本範例複本提供名稱。
   - **定義位置**：使用省略符號，然後選取要作為範例複本儲存位置的管理群組。

1. 在頁面頂端選取 [成品]  索引標籤，或在頁面底部選取 **[下一步：成品]** 。

1. 檢閱構成此藍圖範例的成品清單。 許多成品都具有我們稍後會定義的參數。 當您檢閱完藍圖範例時，請選取 [儲存草稿]  。

## <a name="publish-the-sample-copy"></a>發佈範例複本

您的環境中現已建立了藍圖範例複本。 該複本會以**草稿**模式建立，而且必須先**發佈**，才能進行指派和部署。 您可以根據環境和需求來自訂藍圖範例複本，但是這樣的修改可能會使其不符合 ISO 27001 標準。

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 選取左側的 [藍圖定義]  頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取頁面頂端的 [發佈藍圖]  。 在右側的新窗格中，提供藍圖範例複本的**版本**。 如果您稍後會進行修改，此屬性十分實用。 提供 [變更附註]  ，例如「First version published from the ISO 27001 blueprint sample」。 然後選取頁面底部的 [發佈]  。

## <a name="assign-the-sample-copy"></a>指派範例複本

成功**發佈**藍圖範例複本後，可以將藍圖定義指派給其所在管理群組中的訂用帳戶。 此步驟用於提供參數，以建立每個專屬的藍圖範例複本部署。

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 選取左側的 [藍圖定義]  頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取藍圖定義頁面頂端的 [指派藍圖]  。

1. 提供用於指派藍圖的參數值：

   - 基本概念

     - **訂用帳戶**：在用來儲存藍圖範例複本的管理群組中，選取一或多個訂用帳戶。 如果您選取多個訂用帳戶，輸入的參數就會用來為每個訂用帳戶建立指派。
     - **指派名稱**：名稱會根據藍圖名稱預先填入。
       視需要變更，或保持原狀。
     - **位置**：選取要在其中建立受控識別的區域。 Azure 藍圖會使用此受控識別來部署指派的藍圖之中的所有成品。 若要深入了解，請參閱[適用於 Azure 資源的受控識別](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **藍圖定義版本**：挑選**已發佈**版本的藍圖範例複本。

   - 鎖定指派

     為您的環境選取藍圖鎖定設定。 如需詳細資訊，請參閱[藍圖資源鎖定](../../concepts/resource-locking.md)。

   - 受控識別

     保留預設的 [系統指派]  受控識別選項。

   - 藍圖參數

     許多藍圖定義中的成品都會使用這一節定義的參數，藉此提供一致性。

     - **資源和資源群組的允許位置**：指出資源群組和資源可用的位置值。

   - 構件參數

     本節中定義的參數會套用至其定義所屬的成品。 這些參數是[動態參數](../../concepts/parameters.md#dynamic-parameters)，因為定義於藍圖指派期間。 如需完整清單或成品參數及其說明，請參閱[成品參數資料表](#artifact-parameters-table)。

1. 輸入所有參數後，選取頁面底部的 [指派]  。 藍圖指派會隨即建立，並且開始部署成品。 部署需要大約一小時的時間。 若要檢查部署的狀態，請開啟藍圖指派。

> [!WARNING]
> Azure 藍圖服務和內建藍圖範例皆是**免費**項目。 Azure 資源會[依據產品計價](https://azure.microsoft.com/pricing/)。 請使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估此藍圖範例所部署資源的執行成本。

## <a name="artifact-parameters-table"></a>成品參數資料表

下表提供藍圖成品參數的清單：

|成品名稱|成品類型|參數名稱|描述|
|-|-|-|-|
|\[預覽\]：為 Linux VM 擴展集 (VMSS) 部署 Log Analytics 代理程式|原則指派|Linux VM 擴展集 (VMSS) 的 Log Analytics 工作區|如果工作區是在指派的範圍之外，您必須手動將「Log Analytics 參與者」權限 (或類似權限) 授與原則指派的主體識別碼。|
|\[預覽\]：為 Linux VM 擴展集 (VMSS) 部署 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Linux OS|空陣列可以用來表示沒有選擇性參數：\[\]|
|\[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式|原則指派|Linux VM 的 Log Analytics 工作區|如果工作區是在指派的範圍之外，您必須手動將「Log Analytics 參與者」權限 (或類似權限) 授與原則指派的主體識別碼。|
|\[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Linux OS|空陣列可以用來表示沒有選擇性參數：\[\]|
|\[預覽\]：為 Windows VM 擴展集 (VMSS) 部署 Log Analytics 代理程式|原則指派|Windows VM 擴展集 (VMSS) 的 Log Analytics 工作區|如果工作區是在指派的範圍之外，您必須手動將「Log Analytics 參與者」權限 (或類似權限) 授與原則指派的主體識別碼。|
|\[預覽\]：為 Windows VM 擴展集 (VMSS) 部署 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Windows OS|空陣列可以用來表示沒有選擇性參數：\[\]|
|\[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式|原則指派|Windows VM 的 Log Analytics 工作區|如果工作區是在指派的範圍之外，您必須手動將「Log Analytics 參與者」權限 (或類似權限) 授與原則指派的主體識別碼。|
|\[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Windows OS|空陣列可以用來表示沒有選擇性參數：\[\]|
|允許的儲存體帳戶 SKU|原則指派|允許的儲存體 SKU 清單|可為儲存體帳戶指定之 SKU 的清單。|
|允許的虛擬機器 SKU|原則指派|允許的虛擬機器 SKU 清單|可為虛擬機器指定之 SKU 的清單。|
|ISO 27001 的藍圖方案|原則指派|應啟用診斷記錄的資源類型清單|診斷記錄設定未啟用時，可稽核的資源類型清單。 您可以在 [Azure 監視器診斷記錄結構描述](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)中找到可用值。|

## <a name="next-steps"></a>後續步驟

您已檢閱過 ISO 27001 藍圖範例的部署步驟，接下來請瀏覽下列文章以了解架構和控制對應：

> [!div class="nextstepaction"]
> [ISO 27001 藍圖 - 概觀](./index.md)
> [ISO 27001 藍圖 - 控制項對應](./control-mapping.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
