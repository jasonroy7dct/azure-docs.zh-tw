---
title: 建立 Azure Marketplace 的使用者 VM 映射
description: 列出建立使用者 VM 映像所需的步驟和參考。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: e63f09dc538c5e66b244826cf3b5f92ac388b6a9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818689"
---
# <a name="create-a-user-vm-image"></a>建立使用者 VM 映像

本文說明從一般化 VHD 建立非受控映像所需的兩個一般步驟。  參考的提供是為了引導您完成每個步驟：擷取映像，並將映像一般化。


## <a name="capture-the-vm-image"></a>擷取 VM 映像

請按照下列文章中的說明，擷取與您存取方式對應的 VM：

-  PowerShell：[如何從 Azure VM 建立非受控 VM 映像](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI：[如何建立虛擬機器或 VHD 映像](../../../virtual-machines/linux/capture-image.md)
-  API：[虛擬機器 - 擷取](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>將 VM 映像一般化

因為您已用先前經過一般化的 VHD 產生了使用者映像，應也能將之一般化。  同樣請在下方選擇對應存取機制的文章。  (您可能已在擷取磁碟時，將您的磁碟一般化了。)

-  PowerShell：[一般化 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI：[步驟 2：建立 VM 映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API：[虛擬機器 - 一般化](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>後續步驟

接下來您將[建立憑證](cpp-create-key-vault-cert.md)，並將其儲存在新的 Azure Key Vault 中。  與 VM 建立安全 WinRM 連線需要這個憑證。
