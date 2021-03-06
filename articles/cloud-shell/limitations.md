---
title: Azure Cloud Shell 限制 | Microsoft Docs
description: Azure Cloud Shell 限制的概觀
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951474"
---
# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell 限制

Azure Cloud Shell 具有下列已知限制：

## <a name="general-limitations"></a>一般限制

### <a name="system-state-and-persistence"></a>系統狀態和持續性

提供 Cloud Shell 工作階段的電腦只是暫時性，在工作階段閒置 20 分鐘後就會回收。 Cloud Shell 需要掛接 Azure 檔案共用。 因此，您的訂用帳戶必須能夠設定儲存體資源，才可存取 Cloud Shell。 其他考量包括：

* 在掛接的儲存體中，只會保存 `$Home` 目錄內的修改。
* 只能從您的[已指派區域](persisting-shell-storage.md#mount-a-new-clouddrive)內掛接 Azure 檔案共用。
  * 在 Bash 中，會執行 `env` 來尋找設定為 `ACC_LOCATION` 的區域。

### <a name="browser-support"></a>瀏覽器支援

Cloud Shell 支援最新版的 Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox 及 Apple Safari。 不支援 Safari 私密瀏覽模式。

### <a name="copy-and-paste"></a>複製和貼上

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>針對指定的使用者，只能有一個殼層作用中

使用者一次只能啟動一種類型的 Shell：**Bash** 或 **PowerShell**。 不過，您可能一次會執行多個 Bash 或 PowerShell 執行個體。 使用功能表在 Bash 或 PowerShell 之間交換，會導致 Cloud Shell 重新開機，這會終止現有的會話。 或者，您可以輸入 `bash`，在 PowerShell 內執行 bash，也可以輸入 `pwsh`在 bash 內執行 PowerShell。

### <a name="usage-limits"></a>使用方式限制

Cloud Shell 主要用於互動式的使用案例。 因此，任何長時間執行而沒有互動的工作階段會在不發出警告的情況下結束。

## <a name="bash-limitations"></a>Bash 限制

### <a name="user-permissions"></a>使用者權限

權限設定為沒有 sudo 存取權的一般使用者。 不會保存 `$Home` 目錄之外的任何安裝。

### <a name="editing-bashrc-or-profile"></a>編輯. .bashrc 或 $PROFILE

編輯 .bashrc 或 PowerShell 的 $PROFILE 檔案時，請務必注意，這樣做可能會導致 Cloud Shell 發生未預期的錯誤。

## <a name="powershell-limitations"></a>PowerShell 限制

### <a name="azuread-module-name"></a>`AzureAD` 模組名稱

`AzureAD` 模組名稱目前為 `AzureAD.Standard.Preview`，該模組提供同樣的功能。

### <a name="sqlserver-module-functionality"></a>`SqlServer` 模組功能

包含在 Cloud Shell 中的 `SqlServer` 模組只提供發行前版本的 PowerShell Core 支援。 具體來說，`Invoke-SqlCmd` 目前還無法使用。

### <a name="default-file-location-when-created-from-azure-drive"></a>從 Azure 磁碟機建立時的預設檔案位置：

使用 PowerShell Cmdlet 時，使用者不能在 Azure：磁片磁碟機底下建立檔案。 當使用者使用其他工具 (例如 vim 或 nano) 來建立新檔案時，檔案預設會儲存至 `$HOME` 資料夾。 

### <a name="gui-applications-are-not-supported"></a>不支援 GUI 應用程式

如果使用者執行的命令會建立 Windows 對話方塊，則會看到一則錯誤訊息，例如： `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`。

### <a name="large-gap-after-displaying-progress-bar"></a>顯示進度列之後出現過大間距

如果使用者執行的動作會顯示進度列（例如在 `Azure:` 磁片磁碟機中完成時的索引標籤），則可能是因為游標未正確設定，而且先前的進度列出現間距。

## <a name="next-steps"></a>後續步驟

[針對 Cloud Shell 進行疑難排解](troubleshooting.md) <br>
[Bash 的快速入門](quickstart.md) <br>
[PowerShell 的快速入門](quickstart-powershell.md)
