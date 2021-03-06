---
title: 使用 Site Recovery 將 VMWare VM 容錯移轉至 Azure
description: 了解如何在 Azure Site Recovery 中將 VMware VM 容錯移轉至 Azure
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: 8501bb1a998eb08984a118bfa5d52d1e3f3e4f84
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498075"
---
# <a name="fail-over--vmware-vms"></a>容錯移轉 VMware VM

本文說明如何透過 [Azure Site Recovery](site-recovery-overview.md) 將內部部署 VMware 虛擬機器 (VM) 容錯移轉至 Azure。

這是一系列中的第五個教學課程，說明如何為內部部署機器設定 Azure 的災害復原。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 確認 VMware VM 屬性符合 Azure 需求。
> * 將特定 VM 容錯移轉至 Azure。

> [!NOTE]
> 這些教學課程示範案例的最簡單部署路徑。 在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 如果您想要詳細了解容錯移轉，請參閱[對 VM 和實體伺服器進行容錯移轉](site-recovery-failover.md)。

[了解](failover-failback-overview.md#types-of-failover)不同類型的容錯移轉。 如果您想要容錯移轉復原方案中的多個 VM，請參閱[本文](site-recovery-failover.md)。

## <a name="before-you-start"></a>開始之前

完成先前的教學課程：

1. 確保您已[設定 Azure](tutorial-prepare-azure.md)，以便將內部部署 VMware VM、Hyper-V VM 和實體機器災害復原至 Azure。
2. 準備內部部署 [VMware](vmware-azure-tutorial-prepare-on-premises.md) 環境以便進行災害復原。 
3. 為 [VMware VM](vmware-azure-tutorial.md) 設定災害復原。
4. 執行[災害復原演練](tutorial-dr-drill-azure.md)，以確定一切如預期般運作。

## <a name="verify-vm-properties"></a>驗證 VM 屬性

執行容錯移轉之前，請先檢查 VM 屬性以確定 VM 符合 [Azure 需求](vmware-physical-azure-support-matrix.md#replicated-machines)。

如下所示，驗證屬性：

1. 在 [受保護的項目]  中選取 [複寫的項目]  ，然後選取您要驗證的 VM。

2. 在 [複寫的項目]  窗格中，將會呈現 VM 資訊、健康情況狀態，以及最新可用復原點的摘要。 選取 [屬性]  以檢視詳細資料。

3. 在 [計算和網路]  中，您可以視需要修改下列屬性：
    * Azure 名稱
    * 資源群組
    * 目標大小
    * [可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)
    * 受控磁碟設定

4. 您可以檢視和修改網路設定，包括：

    * Azure VM 在容錯移轉之後所在的網路和子網路。
    * 將指派給 VM 的 IP 位址。

5. 在 [磁碟]  中，您可以看見 VM 上作業系統和資料磁碟的相關資訊。

## <a name="run-a-failover-to-azure"></a>執行容錯移轉到 Azure

1. 在 [設定]   > [複寫的項目]  中，選取要容錯移轉的 VM，然後選取 [容錯移轉]  。
2. 在 [容錯移轉]  中，選取要容錯移轉的目標**復原點**。 您可以使用下列其中一個選項：
   * **最新**：此選項會先處理所有傳送至 Site Recovery 的資料。 它會提供最低的復原點目標 (RPO)，因為在容錯移轉後建立的 Azure VM 具有在觸發容錯移轉時複寫到 Site Recovery 的所有資料。
   * **最近處理**：此選項會將 VM 容錯移轉至 Site Recovery 所處理的最新復原點。 此選項提供低 RTO (復原時間目標)，因為無須花費時間處理未處理的資料。
   * **最新應用程式一致**：此選項會將 VM 容錯移轉至 Site Recovery 所處理的最新應用程式一致復原點。
   * [自訂]  ：此選項可讓您指定復原點。

3. 選取 [先將機器關機再開始容錯移轉]  ，以嘗試在觸發容錯移轉之前將來源 VM 關機。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業]  頁面上追蹤容錯移轉進度。

在某些情況下，容錯移轉需要額外的處理，這大約需要 8 到 10 分鐘才會完成。 您可能會發現下列各項的測試容錯移轉時間比較久：

* 執行的行動服務版本早於 9.8 的 VMware VM。
* 實體伺服器。
* VMware Linux VM。
* 視同實體伺服器受到保護的 Hyper-V VM。
* 未啟用 DHCP 服務的 VMware VM。
* 沒有下列開機驅動程式的 VMware VM：storvsc、vmbus、storflt、intelide、atapi。

> [!WARNING]
> 請勿取消正在進行中的容錯移轉。 在啟動容錯移轉之前，已停止 VM 複寫。 如果您取消正在進行的容錯移轉，容錯移轉會停止，但 VM 不會再次複寫。

## <a name="connect-to-failed-over-vm"></a>連線至容錯移轉的 VM

1. 如果您在容錯移轉後想要使用遠端桌面通訊協定 (RDP) 和安全殼層 (SSH) 連線至 Azure VM，[請確認已符合需求]((ailover-failback-overview.md#connect-to-azure-after-failover)。
2. 容錯移轉之後，請移至 VM 並[連線](../virtual-machines/windows/connect-logon.md)至該 VM 進行驗證。
3. 如果您想要在容錯移轉後使用不同的復原點，請使用 [變更復原點]  。 在下一個步驟中認可容錯移轉之後，將無法再使用此選項。
4. 在驗證之後，選取 [認可]  以完成容錯移轉後的 VM 復原點。
5. 在認可之後，系統會刪除所有其他可用的復原點。 此步驟會完成容錯移轉作業。

>[!TIP]
> 如果您在容錯移轉後遇到任何連線問題，請遵循[疑難排解指南](site-recovery-failover-to-azure-troubleshoot.md)。

## <a name="next-steps"></a>後續步驟

在容錯移轉之後，在內部部署環境中重新保護 Azure VM。 然後，在 VM 受到重新保護並複寫到內部部署網站後，就會在您準備就緒時從 Azure 容錯回復。

> [!div class="nextstepaction"]
> [重新保護 Azure VM](vmware-azure-reprotect.md)
> [從 Azure 容錯回復](vmware-azure-failback.md)
