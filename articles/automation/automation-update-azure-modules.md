---
title: 更新 Azure 自動化中的 Azure 模組
description: 本文說明現在要如何更新 Azure 自動化中預設提供的通用 Azure PowerShell 模組。
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3d7eaae452f307b350c111452b819576cf7f17e5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420478"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何更新 Azure 自動化中的 Azure PowerShell 模組

若要更新您的自動化帳戶中的 Azure 模組，您需要使用「[更新 Azure 模組」 runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)，其以開放原始碼的形式提供。 若要開始使用 **Update-AutomationAzureModulesForAccount** Runbook 來更新 Azure 模組，請從 GitHub 上的[更新 Azure 模組 Runbook 存放庫](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) \(英文\) 下載它。 您接著便可以將它匯入到自動化帳戶，或是以指令碼的形式執行它。 若要瞭解如何在您的自動化帳戶中匯入 runbook，請參閱匯[入 runbook](manage-runbooks.md#import-a-runbook)。

預設會在每個自動化帳戶中提供最常見的 AzureRM PowerShell 模組。 Azure 小組會定期更新 Azure 模組，因此若要保持最新狀態，您會想要使用[update-automationazuremodulesforaccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook 來更新自動化帳戶中的模組。

由於產品小組會定期更新模組，因此，內含的 Cmdlet 可能會發生變更。 此動作可能會根據變更的類型 (例如，重新命名參數或完全淘汰 Cmdlet) 對您的 Runbook 產生負面影響。

為了避免影響 Runbook 以及它們所自動化的流程，請先測試和驗證，然後再繼續。 如果您沒有適用於此用途的專用自動化帳戶，請考慮建立一個，讓您可以在開發 Runbook 期間測試許多不同的案例。 此測試應該包含更新 PowerShell 模組之類的反覆變更。

如果您在本機開發您的指令碼，建議您在測試時於本機使用您自動化帳戶中的相同模組版本，以確保您會收到相同的結果。 結果已經過驗證且您已套用所需的任何變更之後，您就能將變更移到生產環境。

> [!NOTE]
> 新的自動化帳戶可能不會包含最新的模組。

> [!NOTE]
> 您將無法刪除全域模組-自動化提供現成可用的模組。

## <a name="considerations"></a>考量

下列為在使用此程序來更新 Azure 模組時應納入考量的項目：

* 根據預設，此 runbook 支援更新**Azure**和**AzureRm**模組。 此 runbook 也支援更新**Az**模組。 如需使用此 runbook 更新 `Az` 模組的詳細資訊，請參閱[更新 Azure 模組 RUNBOOK 讀我檔案](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md)。 在您的自動化帳戶中使用 `Az` 模組時，您必須考慮其他重要因素，若要深入瞭解，請參閱在[您的自動化帳戶中使用 Az 模組](az-modules.md)。

* 在啟動此 Runbook 之前，請確定您的自動化帳戶已建立 [Azure 執行身分帳戶認證](manage-runas-account.md)。

* 您可以使用此程式碼做為一般的 PowerShell 腳本，而不是 runbook：只需先使用[AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount)命令登入 Azure，然後將 `-Login $false` 傳遞至腳本。

* 若要在主權雲端上使用此 Runbook，請使用 `AzureRmEnvironment` 參數來將正確的環境傳遞給 Runbook。  允許的值為 **AzureCloud**、**AzureChinaCloud**、**AzureGermanCloud** 和 **AzureUSGovernment**。 這些值可以使用 `Get-AzureRmEnvironment | select Name` 來擷取。 如果您未傳遞值給這個參數，Runbook 將會預設為 Azure 公用雲端 **AzureCloud**。

* 如果您想要使用特定的 Azure PowerShell 模組版本，而不是 PowerShell 資源庫上提供的最新版本，可將這些版本傳遞給 **Update-AutomationAzureModulesForAccount** Runbook 的選擇性 `ModuleVersionOverrides` 參數。 如需範例，請參閱 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) \(英文\) Runbook。 `ModuleVersionOverrides` 參數中未提及的 Azure PowerShell 模組會以 PowerShell 資源庫上的最新模組版本來更新。 如果您未傳遞任何項目給 `ModuleVersionOverrides` 參數，則所有模組都會以 PowerShell 資源庫上的最新模組版本來更新。 此行為與 [更新 Azure 模組] 按鈕相同。

## <a name="next-steps"></a>後續步驟

造訪開放原始碼的[更新 Azure 模組 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) \(英文\) 來深入了解它。
