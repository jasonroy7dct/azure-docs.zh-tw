---
title: 因為靜態 IP 而無法將遠端桌面連線到 Azure 虛擬機器 | Microsoft Docs
description: 了解如何針對 Microsoft Azure 中靜態 IP 所造成的 RDP 問題進行疑難排解。| Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 7756d4c808e104b0de14e1cea4af5e843cff755c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981774"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>因為靜態 IP 而無法將遠端桌面連線到 Azure 虛擬機器

本文說明在 VM 中設定靜態 IP 之後，您無法將遠端桌面連線至 Azure Windows 虛擬機器 (VM) 的問題。

> [!NOTE]
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../../azure-resource-manager/management/deployment-models.md)。 本文說明如何使用 Resource Manager 部署模型，我們建議將它用於新部署，而非用於傳統部署模型。

## <a name="symptoms"></a>徵狀

當您對 Azure 中的 VM 進行 RDP 連線時，您會收到下列錯誤訊息：

**因為下列其中一個原因，遠端桌面無法連線到遠端電腦：**

1. **未啟用伺服器的遠端存取**

2. **遠端電腦已關閉**

3. **遠端電腦無法在網路上使用**

**請確定遠端電腦已開啟並連線到網路，而且已啟用遠端存取。**

當您檢查 Azure 入口網站中[開機診斷](../troubleshooting/boot-diagnostics.md)的螢幕擷取畫面時，您會在登入畫面中看到 VM 正常開機並等候認證。

## <a name="cause"></a>原因

VM 具有在 Windows 中的網路介面上定義的靜態 IP 位址。 此 IP 位址不同於 Azure 入口網站中所定義的位址。

## <a name="solution"></a>解決方案

在遵循下列步驟之前，請擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。

若要解決此問題，請使用序列控制來為 VM 啟用 DHCP 或[重設網路介面](reset-network-interface.md)。

### <a name="use-serial-control"></a>使用序列主控台

1. 連線至[序列主控台並開啟 CMD 執行個體](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
)。 如果未在 VM 上啟用序列主控台，請參閱[重設網路介面](reset-network-interface.md)。
2. 請檢查是否已在網路介面上停用 DHCP：

        netsh interface ip show config
3. 如果已停用 DHCP，請您網路介面的組態還原為使用 DHCP：

        netsh interface ip set address name="<NIC Name>" source=dhc

    比方說，如果交互作用介面名稱為「乙太網路 2」，請執行下列命令：

        netsh interface ip set address name="Ethernet 2" source=dhc

4. 再次查詢 IP 組態，確定現在已正確設定網路介面。 新的 IP 位址應符合 Azure 所提供的 IP 位址。

        netsh interface ip show config

    此時您不需要重新啟動 VM。 您可回頭連線 VM。

之後，如果您要設定 VM 的靜態 IP，請參閱[設定 VM 的靜態 IP 位址](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)。