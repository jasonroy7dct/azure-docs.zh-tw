---
title: LinkedIn 帳戶連線的系統管理員同意-Azure AD |Microsoft Docs
description: 說明如何在中啟用或停用 Microsoft 應用程式中的 LinkedIn 整合帳戶連接 Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf65f69d9dcaf6de2236c98b56b58ec7e021099
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025404"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>在 Azure Active Directory 中整合 LinkedIn 帳戶連接

您可以讓組織中的使用者存取其在某些 Microsoft 應用程式內的 LinkedIn 連線。 在使用者同意連接其帳戶之前，不會共用任何資料。 您可以在 Azure Active Directory （Azure AD）系統[管理中心](https://aad.portal.azure.com)內整合您的組織。

> [!IMPORTANT]
> LinkedIn 帳戶連線設定目前正向 Azure AD 的組織推出。 當您的組織推出時，預設會啟用此功能。
> 
> 例外狀況：
> * 此設定不適用於使用 Microsoft Cloud for US Government、Microsoft Cloud Germany 或中國 21Vianet 管理之 Azure 和 Office 365 的客戶。
> * 對於佈建於德國的租用戶，此設定預設為關閉。 請注意，此設定不適用於使用 Microsoft Cloud Germany 的客戶。
> * 對於佈建於法國的租用戶，此設定預設為關閉。
>
> 當您的組織啟用 LinkedIn 帳戶連線之後，帳戶連線會在使用者同意應用程式代表他們存取公司資料時生效。 如需使用者同意設定的相關資訊，請參閱[如何移除使用者對應用程式的存取權](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)。

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>在 Azure 入口網站中啟用 LinkedIn 帳戶連線

您只能針對您想要擁有存取權的使用者，啟用 LinkedIn 帳戶連線，從整個組織到您組織中的選取使用者。

1. 使用 Azure AD 組織的全域管理員帳戶登入[Azure AD 系統管理中心](https://aad.portal.azure.com/)。
1. 選取 [使用者]。
1. 在 [使用者] 刀鋒視窗上，選取 [使用者設定]。
1. 在 [ **LinkedIn 帳戶**連線] 底下，允許使用者連線其帳戶，以存取其在某些 Microsoft 應用程式內的 LinkedIn 連線。 在使用者同意連接其帳戶之前，不會共用任何資料。

    * 選取 **[是]** ，為組織中的所有使用者啟用服務
    * 選取 [**選取的群組**] 僅針對您組織中的一組選取的使用者啟用服務
    * 選取 [**否**] 以撤銷貴組織中所有使用者的同意

    ![在組織中整合 LinkedIn 帳戶連線](./media/linkedin-integration/linkedin-integration.png)

1. 當您完成時，請選取 [**儲存**] 來儲存您的設定。

> [!Important]
> 您的使用者在同意連接其帳戶之前，不會完全啟用 LinkedIn 整合。 當您為使用者啟用帳戶連接時，不會共用任何資料。

### <a name="assign-selected-users-with-a-group"></a>使用群組指派選取的使用者
我們已取代 [選取的] 選項，指定使用者清單以選取使用者群組，讓您可以為單一群組（而不是許多個人使用者）連接 LinkedIn 和 Microsoft 帳戶。 如果您未針對選取的個別使用者啟用 LinkedIn 帳戶連線，則不需要執行任何動作。 如果您先前已針對選取的個別使用者啟用 LinkedIn 帳戶連線，您應該：

1. 取得個人使用者的目前清單
1. 將目前啟用的個別使用者移至群組
1. 在 Azure AD 系統管理中心，使用 [LinkedIn 帳戶連線] 設定中的 [從上一個群組] 做為選取的群組。

> [!NOTE]
> 即使您未將目前選取的個別使用者移至群組，他們仍然可以在 Microsoft 應用程式中看到 LinkedIn 資訊。

### <a name="get-the-current-list-of-selected-users"></a>取得所選使用者的目前清單

1. 使用您的系統管理員帳戶登入 Microsoft 365。
1. 移至 https://linkedinselectedusermigration.azurewebsites.net/。 您會看到針對 LinkedIn 帳戶連線選取的使用者清單。
1. 將清單匯出至 CSV 檔案。

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>將目前選取的個別使用者移至群組

1. 啟動 PowerShell
1. 藉由執行 `Install-Module AzureAD` 安裝 Azure AD 模組
1. 執行下列指令碼：

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

若要在 Azure AD 系統管理中心的 [LinkedIn 帳戶連線] 設定中，使用步驟二中的群組做為選取的群組，請參閱[Azure 入口網站中的 [啟用 linkedin 帳戶](#enable-linkedin-account-connections-in-the-azure-portal)連線]。

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>使用群組原則來啟用 LinkedIn 帳戶連線

1. 下載 [Office 2016 系統管理範本檔案 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. 解壓縮 **ADMX** 檔案，並將其複製到中央存放區。
1. 開啟群組原則管理。
1. 使用下列設定建立群組原則物件：[使用者設定] > [系統管理範本] > [Microsoft Office 2016] > [其他] > [在 Office 應用程式中顯示 LinkedIn 功能]。
1. 選取 [啟用] 或 [停用]。
  
   State | 效果
   ------ | ------
   **已啟用** | Office 2016 選項中的 [在 Office 應用程式中顯示 LinkedIn 功能] 設定已啟用。 您組織中的使用者可以在其 Office 2016 應用程式中使用 LinkedIn 功能。
   **已停用** | Office 2016 選項中的 [在 Office 應用程式中顯示 LinkedIn 功能] 設定已停用，而且終端使用者無法變更此設定。 組織中的使用者無法在其 Office 2016 應用程式中使用 LinkedIn 功能。

這個群組原則只會影響本機電腦的 Office 2016 應用程式。 如果使用者在其 Office 2016 應用程式中停用 LinkedIn，他們仍然可以在 Office 365 中看到 LinkedIn 功能。

## <a name="next-steps"></a>後續步驟

* [LinkedIn 的使用者同意和資料共用](linkedin-user-consent.md)

* [Microsoft 應用程式中的 LinkedIn 資訊和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 說明中心](https://www.linkedin.com/help/linkedin)

* [在 Azure 入口網站中檢視您目前的 LinkedIn 整合設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
