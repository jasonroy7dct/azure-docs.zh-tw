---
title: 所有 Azure 資訊安全中心建議的參考表
description: 本文列出 Azure 資訊安全中心的安全性建議，可協助您保護您的資源。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2019
ms.author: memildin
ms.openlocfilehash: a6a1371553ccd9b810ba4649af448fb8847d0ed8
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604705"
---
# <a name="security-recommendations---a-reference-guide"></a>安全性建議-參考指南

本文列出您可能會在 Azure 資訊安全中心中看到的建議。 您的環境中所顯示的建議取決於您要保護的資源和自訂的設定。

若要瞭解如何回應這些建議，請參閱[Azure 資訊安全中心中的補救建議](security-center-remediate-recommendations.md)。

您的安全分數是根據您已緩和的資訊安全中心建議數而定。 若要優先處理建議以解決問題，請考慮每一個的嚴重性。

## <a name="recs-network"></a>網路建議

|建議|描述 & 相關原則|Severity|已啟用快速修正？（[深入瞭解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|資源類型|
|----|----|----|----|----|
|**即時網路存取控制應套用在虛擬機器上**|套用即時（JIT）虛擬機器（VM）存取控制，以永久鎖定所選埠的存取權，並讓已授權的使用者只能在有限的時間內透過 JIT 開啟它們。<br>（相關原則：您應該在虛擬機器上套用即時網路存取控制）|高|N|虛擬機器|
|**應啟用子網層級上的網路安全性群組**|啟用網路安全性群組，以控制子網中所部署資源的網路存取。<br>（相關原則：子網應與網路安全性群組相關聯。<br>預設會停用此原則）|高/中|N|子網路|
|**應使用網路安全性群組保護網際網路對應的虛擬機器**|啟用網路安全性群組以控制虛擬機器的網路存取。<br>（相關原則：網際網路面向的虛擬機器應該使用網路安全性群組來保護）|高/中|N|虛擬機器|
|**所有網路埠都應該限制在與您的 VM 相關聯的 NSG 上**|藉由限制現有允許規則的存取，強化網際網路對應 Vm 的網路安全性群組。<br>當*所有*來源都開啟任何埠（埠22、3389、5985、5986、80和1443除外）時，就會觸發這項建議。<br>（相關原則：必須限制透過網際網路面向端點的存取）|高|N|虛擬機器|
|**應在網際網路對應虛擬機器中套用自適性網路強化建議**|當自動調整網路強化功能找到過度寬鬆的 NSG 規則時，標準定價層的客戶會看到這項建議。<br>（相關原則：彈性網路強化建議應套用於網際網路對向虛擬機器）|高|N|虛擬機器|
|**IaaS Nsg 上的 web 應用程式規則應該<br/>強化（已淘汰）**|強化執行 web 應用程式之虛擬機器的網路安全性群組（NSG），並使用對 web 應用程式埠而言過於寬鬆的 NSG 規則。<br>（相關原則： IaaS 上 web 應用程式的 Nsg 規則應該要強化）|高|N|虛擬機器|
|**應用程式服務的存取應限制<br/>（已淘汰）**|藉由變更網路設定來限制對應用程式服務的存取，以拒絕範圍太廣的輸入流量。<br>（相關原則： [預覽]：應限制應用程式服務的存取）|高|N|App Service|
|**應關閉虛擬機器上的管理連接埠**|強化虛擬機器的網路安全性群組，以限制對管理埠的存取。<br>（相關原則：應該在虛擬機器上關閉管理埠）|高|N|虛擬機器|
|**應啟用 DDoS 保護標準**|啟用 DDoS 保護服務標準，以保護包含具有公用 Ip 之應用程式的虛擬網路。 DDoS 保護可降低網路體積型和通訊協定攻擊的風險。<br>（相關原則：應啟用 DDoS 保護標準）|高|N|虛擬網路|
|**應停用虛擬機器上的 IP 轉送**|停用 IP 轉送。 在虛擬機器的 NIC 上啟用 IP 轉送時，機器可以接收定址到其他目的地的流量。 IP 轉送很少需要（例如，使用 VM 作為網路虛擬裝置），因此，這應該由網路安全性小組加以檢查。<br>（相關原則： [預覽]：您的虛擬機器上的 IP 轉送應停用）|中|N|虛擬機器|
|**Web 應用程式應只可經由 HTTPS 存取**|啟用 web 應用程式的「僅限 HTTPS」存取權。 使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。<br>（相關原則： Web 應用程式應該只能透過 HTTPS 存取）|中|**Y**|Web 應用程式|
|**函式應用程式應只可經由 HTTPS 存取**|啟用函數應用程式的「僅限 HTTPS」存取。 使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。<br>（相關原則：函數應用程式只能透過 HTTPS 存取）|中|**Y**|函式應用程式|
|**應啟用儲存體帳戶的安全傳輸**|啟用儲存體帳戶的安全傳輸。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，並保護傳輸中的資料免于網路層的攻擊，例如中間人攻擊、竊聽及會話劫持。<br>（相關原則：應啟用安全傳輸至儲存體帳戶）|高|**Y**|儲存體帳戶|
||||||


## <a name="recs-containers"></a>容器建議

|建議|描述 & 相關原則|Severity|已啟用快速修正？（[深入瞭解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|資源類型|
|----|----|----|----|----|
|**以角色為基礎的存取控制應該用來限制 Kubernetes 服務叢集的存取權**|若要提供使用者可執行之動作的細微篩選，請使用角色型存取控制（RBAC）來管理 Kubernetes 服務叢集中的許可權，並設定相關的授權原則。 如需詳細資訊，請參閱[Azure 角色型存取控制](https://docs.microsoft.com/azure/aks/concepts-identity#role-based-access-controls-rbac)。<br>（相關原則： [預覽]：以角色為基礎的存取控制（RBAC）應在 Kubernetes 服務上使用）|中|N|計算資源（容器）|
|**Kubernetes 服務應升級為最新的 Kubernetes 版本**|將 Azure Kubernetes Service 叢集升級為最新的 Kubernetes 版本，以從最新的弱點修補程式中獲益。 如需有關特定 Kubernetes 弱點的詳細資訊，請參閱[Kubernetes cve](https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=kubernetes)。<br>（相關原則： [預覽]： Kubernetes 服務應該升級為不容易受攻擊的 Kubernetes 版本）|高|N|計算資源（容器）|
|**您應該定義 Pod 安全性原則，藉由移除不必要的應用程式許可權來減少攻擊向量（預覽）**|定義 Pod 安全性原則，藉由移除不必要的應用程式許可權來減少攻擊向量。 建議您設定 pod 安全性原則，讓 pod 只能存取允許其存取的資源。<br>（相關原則： [預覽]： Pod 安全性原則應定義于 Kubernetes 服務上）|中|N|計算資源（容器）|
|**Kubernetes 服務管理 API 的存取權應該僅授權特定的 IP 範圍來限制**|僅將 API 存取權授與特定範圍中的 IP 位址，以限制 Kubernetes 服務管理 API 的存取權。 建議您設定授權的 IP 範圍，讓只有來自允許的網路的應用程式可以存取叢集。<br>（相關原則： [預覽]：已授權的 IP 範圍應定義于 Kubernetes 服務上）|高|N|計算資源（容器）|
|**Azure Container Registry 映射中的弱點應予以補救（由 Qualys 提供技術支援）**|容器映射弱點評估會掃描您的登錄是否有每個已推送容器映射的安全性弱點，並公開每個映射的詳細結果。 解決這些弱點可以大幅改善容器的安全性狀態，並保護它們免于遭受攻擊。<br>（沒有相關的原則）|高|N|計算資源（容器）|
||||||


## <a name="recs-appservice"></a>App Service 建議

|建議|描述 & 相關原則|Severity|已啟用快速修正？（[深入瞭解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|資源類型|
|----|----|----|----|----|
|**Web 應用程式應只可經由 HTTPS 存取**|僅限透過 HTTPS 存取 Web 應用程式。<br>（相關原則：）|中|N|App Service|
|**函式應用程式應只可經由 HTTPS 存取**|僅限透過 HTTPS 存取函數應用程式。<br>（相關原則：）|中|N|App Service|
|**API 應用程式應只可經由 HTTPS 存取**|僅限透過 HTTPS 存取 API Apps。<br>（相關原則：）|中|N|App Service|
|**應關閉 Web 應用程式的遠端偵錯**|如果您不再需要使用 Web 應用程式的偵錯功能，請將該功能關閉。 遠端偵錯程式需要在 Web 應用程式上開啟輸入埠。<br>（相關原則：應關閉 Web 應用程式的遠端偵錯程式）|低|**Y**|App Service|
|**應關閉函數應用程式的遠端偵錯程式**|如果您不再需要使用函數應用程式的偵錯功能，請將該功能關閉。 遠端偵錯需要在函式應用程式上開啟輸入連接埠。<br>（相關原則：應關閉函數應用程式的遠端偵錯程式）|低|**Y**|App Service|
|**應關閉 API 應用程式的遠端偵錯**|如果您不再需要使用 API 應用程式，請關閉它的偵錯工具。 遠端偵錯程式需要在 API 應用程式上開啟輸入埠。<br>（相關原則： API 應用程式應關閉遠端偵錯程式）|低|**Y**|App Service|
|**CORS 不應允許每項資源存取您的 Web 應用程式**|請只允許必要網域與您的 Web 應用程式互動。 跨原始資源共用 (CORS) 不應允許所有網域存取 Web 應用程式。<br>（相關原則： CORS 不應允許每個資源存取您的 Web 應用程式）|低|**Y**|App Service|
|**CORS 不應允許每個資源存取您的函數應用程式**|請只允許必要網域與您的函數應用程式互動。 跨原始資源共用 (CORS) 不應允許所有網域存取函式應用程式。<br>（相關原則： CORS 不應允許每個資源存取您的函數應用程式）|低|**Y**|App Service|
|**CORS 不應允許每項資源存取您的 API 應用程式**|僅允許必要網域與您的 API 應用程式互動。 跨原始來源資源分享（CORS）不應允許所有網域存取您的 API 應用程式。<br>（相關原則： CORS 不應允許每個資源存取您的 API 應用程式）|低|**Y**|App Service|
|**應在 App Service 中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>（相關原則：應該啟用應用程式服務中的診斷記錄）</span>|低|N|App Service|
||||||


## <a name="recs-computeapp"></a>計算和應用程式建議

|建議|描述 & 相關原則|Severity|已啟用快速修正？（[深入瞭解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|資源類型|
|----|----|----|----|----|
|**應啟用 Azure 串流分析中的診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>（相關原則：應該啟用 Azure 串流分析中的診斷記錄）|低|**Y**|計算資源 (串流分析)|
|**應啟用 Batch 帳戶中的診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>（相關原則：應啟用 Batch 帳戶中的診斷記錄）|低|**Y**|計算資源 (Batch)|
|**應啟用事件中樞內的診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>（相關原則：應該啟用事件中樞的診斷記錄）|低|**Y**|計算資源 (事件中樞)|
|**應在 Logic Apps 中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>（相關原則： Logic Apps 中的診斷記錄應該啟用）|低|**Y**|計算資源 (Logic Apps)|
|**應在搜尋服務中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>（相關原則：搜尋服務中的診斷記錄應該啟用）|低|**Y**|計算資源 (搜尋)|
|**應在服務匯流排中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>（相關原則：服務匯流排中的診斷記錄應該啟用）|低|**Y**|計算資源 (服務匯流排)|
|**Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證**|在 Service Fabric 中只透過 Azure Active Directory 來執行用戶端驗證。<br>（相關原則： Service Fabric 叢集應該只使用 Azure Active Directory 進行用戶端驗證）|高|N|計算資源 (Service Fabric)|
|**Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign**|Service Fabric 使用主要叢集憑證，為節點對節點的通訊提供三個層級的保護（None、Sign 和 EncryptAndSign）。 設定保護層級可確保所有節點對節點的訊息皆經過加密及數位簽署。<br>（相關原則：應設定要在 Service Fabric 中 EncryptAndSign 的 ClusterProtectionLevel 屬性）|高|N|計算資源 (Service Fabric)|
|**除了 RootManageSharedAccessKey 外，應從服務匯流排命名空間移除所有授權規則**|服務匯流排用戶端不應該使用提供命名空間中所有佇列及主題存取權的命名空間層級存取原則。 若要與最低權限資訊安全模型達成一致，您應在佇列和主題的實體層級建立存取原則，以提供僅限特定實體的存取權。<br>（相關原則：除了 RootManageSharedAccessKey 以外的所有授權規則都應該從服務匯流排命名空間移除）|低|N|計算資源 (服務匯流排)|
|**除了 RootManageSharedAccessKey 外，應從事件中樞命名空間移除所有授權規則**|事件中樞用戶端不應該使用提供命名空間中所有佇列及主題存取權的命名空間層級存取原則。 若要與最低權限資訊安全模型達成一致，您應在佇列和主題的實體層級建立存取原則，以提供僅限特定實體的存取權。<br>（相關原則：除了 RootManageSharedAccessKey 以外的所有授權規則都應該從事件中樞命名空間移除）|低|N|計算資源 (事件中樞)|
|**應定義事件中樞實體的授權規則**|稽核事件中樞實體上的授權規則，以只授與最低權限的存取權。<br>（相關原則：應該定義事件中樞實體的授權規則）|低|N|計算資源 (事件中樞)|
|**在您的虛擬機器上安裝監視代理程式**|安裝 Monitoring Agent，以在每部機器上啟用資料收集、更新掃描、基準掃描及端點保護。<br>（相關原則：監視代理程式應在您的虛擬機器上啟用）|高|**Y**|電腦|
|**監視代理程式健康情況問題應在您的電腦上解決**|為了獲得完整的「資訊安全中心」保護，請依照《疑難排解指南》中的指示，解決機器上的監視代理程式問題<br>（沒有相關的原則-相依于「在您的虛擬機器上安裝監視代理程式」）|中|N|電腦|
|**應在虛擬機器上啟用自適性應用程式控制**|啟用應用程式控制，以控制哪些應用程式可在您位於 Azure 中的 VM 上執行。 這可協助強化您 VM 抵禦惡意程式碼的能力。 「資訊安全中心」會利用機器學習服務來分析在每個 VM 上執行的應用程式，並協助您利用此情報來套用允許規則。 此功能可簡化設定及維護應用程式允許規則的程序。<br>（相關原則：您應該在虛擬機器上啟用彈性應用程式控制項）|高|N|電腦|
|**在您的電腦上安裝 endpoint protection 解決方案**|在您的 Windows 和 Linux 電腦上安裝 endpoint protection 解決方案，以保護其免于遭受威脅和弱點。<br>（相關原則：監視 Azure 資訊安全中心中遺漏的 Endpoint Protection）|中|N|電腦|
|**在虛擬機器上安裝端點保護解決方案**|在虛擬機器上安裝 Endpoint Protection 解決方案，以避免虛擬機器遭受威脅及弱點損害。<br>（沒有相關的原則）|中|N|電腦|
|**應更新雲端服務角色的 OS 版本**|將您雲端服務角色的作業系統 (OS) 版本更新至您 OS 系列可用的最新版本。<br>（沒有相關的原則）|高|N|電腦|
|**您應在機器上安裝系統更新**|安裝缺少的系統安全性與重大更新，以保護您的 Windows 及 Linux 虛擬機器與電腦<br>（相關原則：系統更新應該安裝在您的電腦上）|高|N|電腦|
|**應重新開機您的電腦以套用系統更新**|重新啟動機器，以套用系統更新及避免機器受到弱點損害。<br>（沒有相關的原則-取決於「您的電腦上應該安裝系統更新」）|中|N|電腦|
|**應加密自動化帳戶變數**|儲存敏感性資料時，啟用自動化帳戶變數資產加密。<br>（相關原則：應該在自動化帳戶變數上啟用加密）|高|N|計算資源 (自動化帳戶)|
|**應在虛擬機器上套用磁碟加密**|使用「Azure 磁碟加密」為 Windows 和 Linux 虛擬機器磁碟加密虛擬機器磁碟。 「Azure 磁碟加密」(ADE) 利用 Windows 的業界標準 BitLocker 功能及 Linux 的 DM-Crypt 功能來提供 OS 和資料磁碟加密，以協助您在客戶 Azure Key Vault 中保護資料並滿足組織的安全性與合規性承諾。 當您的合規性與安全性需求要求您使用加密金鑰來進行端對端資料加密 (包括加密暫時磁碟 (本機連結的臨時磁碟)) 時，請使用「Azure 磁碟加密」。 或者，根據預設，受控磁碟會使用 Azure 儲存體服務加密進行加密，其中加密金鑰是 Azure 中由 Microsoft 管理的金鑰。 如果這符合您的合規性與安全性需求，您便可以利用預設的受控磁碟加密來滿足您的需求。<br>（相關原則：磁片加密應套用至虛擬機器）|高|N|電腦|
|**虛擬機器應遷移到新的 Azure Resource Manager 資源**|使用虛擬機器的 Azure Resource Manager 來提供安全性增強功能，例如：更強的存取控制（RBAC）、更佳的審核、Resource Manager 為基礎的部署和治理、受控身分識別的存取、金鑰保存庫的秘密存取權、以 Azure AD 為基礎的驗證和支援標記和資源群組，讓安全性管理更加輕鬆。<br>（相關原則：虛擬機器應該遷移至新的 Azure Resource Manager 資源）|低|N|電腦|
|**弱點評估解決方案應該安裝在您的虛擬機器上**|在您的虛擬機器上安裝弱點評定解決方案<br>（相關原則：弱點評估應該安裝在虛擬機器上）|中|N|電腦|
|**弱點評量解決方案應修復弱點**|針對已部署弱點評定第三方解決方案的虛擬機器，持續評定是否有應用程式和 OS 弱點。 每當找到這類弱點時，都會隨著建議一併提供這些弱點的相關詳細資訊。<br>（相關原則：弱點評估解決方案應補救弱點）|高|N|電腦|
|**您應在機器上修復安全性組態的弱點**|修復機器上安全性設定中的弱點，以避免這些機器遭受攻擊。<br>（相關原則：您電腦上安全性設定中的弱點應予以補救）|低|N|電腦|
|**應補救容器安全性設定中的弱點**|修複已安裝 Docker 之機器上安全性設定中的弱點，以避免這些機器遭受攻擊。<br>（相關原則：應補救容器安全性設定中的弱點）|高|N|電腦|
|**端點保護健康情況問題應在您的電腦上解決**|為了獲得完整的「資訊安全中心」保護，請依照《疑難排解指南》中的指示，解決機器上的監視代理程式問題。<br>（沒有相關的原則-相依于「在您的電腦上安裝 endpoint protection 解決方案」）|中|N|電腦|
||||||


## <a name="recs-vmscalesets"></a>虛擬機器擴展集建議

|建議|描述 & 相關原則|Severity|已啟用快速修正？（[深入瞭解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|資源類型|
|----|----|----|----|----|
|**應在虛擬機器擴展集中啟用診斷記錄**|啟用記錄並保留記錄最長一年。 這可讓您重新建立活動線索來進行調查。 當發生安全性事件或您的網路遭到入侵時，這非常有用。<br>（相關原則：虛擬機器擴展集中的診斷記錄應該啟用）|低|N|虛擬機器擴展集|
|**應在虛擬機器擴展集上補救端點保護健康情況失敗**|修復虛擬機器擴展集的端點保護健康狀態失敗，避免其遭受威脅與弱點的傷害。<br>（沒有相關的原則-相依于「應在虛擬機器擴展集上安裝端點保護解決方案」）|低|N|虛擬機器擴展集|
|**應在虛擬機器擴展集上安裝端點保護解決方案**|在虛擬機器擴展集上安裝端點保護解決方案，避免其遭受威脅及弱點的傷害。<br>（相關原則：端點保護解決方案應該安裝在虛擬機器擴展集上）|高|N|虛擬機器擴展集|
|**應在虛擬機器擴展集上安裝系統更新**|安裝遺漏的系統安全性及重大更新，以保護您的 Windows 與 Linux 虛擬機器擴展集。<br>（相關原則：應該安裝虛擬機器擴展集上的系統更新）|高|N|虛擬機器擴展集|
|**應修復虛擬機器擴展集上安全性組態的弱點**|修復虛擬機器擴展集之安全性設定中的弱點，避免其遭受攻擊。 <br>（相關原則：虛擬機器擴展集上安全性設定中的弱點應予以補救）|高|N|虛擬機器擴展集|
||||||


## <a name="recs-datastorage"></a>資料和儲存體建議

|建議|描述 & 相關原則|Severity|已啟用快速修正？（[深入瞭解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|資源類型|
|----|----|----|----|----|
|**應限制具有防火牆和虛擬網路設定的儲存體帳戶存取權**|稽核儲存體帳戶防火牆設定中不受限制的網路存取。 請改為設定網路規則，只允許來自認可之網路的應用程式存取儲存體帳戶。 若要允許來自特定網際網路或內部部署用戶端的連線，您可以授與存取權給來自特定 Azure 虛擬網路或公用網際網路 IP 位址範圍的流量。<br>（相關原則：對儲存體帳戶進行不受限制的網路存取|低|N|儲存體帳戶|
|**應針對 SQL 伺服器佈建 Azure Active Directory 管理員**|為 SQL Server 佈建 Azure AD 系統管理員，以啟用 Azure AD 驗證。 Azure AD 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。<br>（相關原則：針對 SQL server 的 Azure Active Directory 系統管理員進行 Audit 布建）|高|N|SQL|
|**應啟用 SQL 伺服器上的稽核**|啟用 Azure SQL 伺服器的審核。 (僅限 Azure SQL 服務。 不包含在虛擬機器上執行的 SQ。)<br>（相關原則： SQL Server 上的 advanced data security 設定上應該啟用 [審核]）|低|**Y**|SQL|
|**應在 Azure Data Lake Store 中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>（相關原則： Azure Data Lake 存放區中的診斷記錄應該啟用）|低|**Y**|Data Lake Store|
|**應在 Data Lake Analytics 中啟用診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>（相關原則： Data Lake Analytics 中的診斷記錄應該啟用）|低|**Y**|Data Lake 分析|
|**應該只允許對 Redis Cache 的安全連線**|只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持。<br>（相關原則：只應啟用 Redis 快取的安全連線）|高|N|Redis|
|**應啟用儲存體帳戶的安全傳輸**|安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 HTTPS 可確保伺服器與服務之間的驗證，並保護傳輸中的資料免于網路層的攻擊，例如中間人攻擊、竊聽及會話劫持。<br>（相關原則：應啟用安全傳輸至儲存體帳戶）|高|N|儲存體帳戶|
|**儲存體帳戶應移轉至新的 Azure Resource Manager 資源**|針對您的儲存體帳戶使用新的 Azure Resource Manager 來提供安全性增強功能，例如：更強的存取控制（RBAC）、更佳的審核、Resource Manager 為基礎的部署和治理、受控身分識別的存取、金鑰保存庫的秘密存取權、和以 Azure AD 為基礎的驗證和支援標記和資源群組，讓安全性管理更加輕鬆。<br>（相關原則：儲存體帳戶應遷移至新的 Azure Resource Manager 資源）|低|N|儲存體帳戶|
|**應在 SQL 資料庫上啟用透明資料加密**|啟用透明資料加密，以保護待用資料及符合合規要求。<br>（相關原則：應該啟用 SQL 資料庫上的透明資料加密）|低|**Y**|SQL|
|**弱點評估應於您的 SQL 伺服器上啟用**|弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。<br>（相關原則：弱點評估應在您的 SQL 伺服器上啟用）|高|**Y**|SQL|
|**應修復 SQL 資料庫的弱點**|SQL 弱點評定會掃描您的資料庫是否有安全性弱點，並公開任何與最佳作法的偏差，例如錯誤的錯誤、過多的許可權，以及未受保護的敏感性資料。 解決找到的弱點可以大幅改進資料庫的安全性等級。<br>（相關原則：應該補救 SQL 資料庫上的弱點）|高|N|SQL|
||||||


## <a name="recs-identity"></a>身分識別與存取建議

|建議|描述 & 相關原則|Severity|已啟用快速修正？（[深入瞭解](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|資源類型|
|----|----|----|----|----|
|**應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA**|為具有讀取權限的所有訂用帳戶啟用 Multi-Factor Authentication (MFA)，以避免發生帳戶或資源資料外洩。<br>（相關原則：您應該在訂用帳戶上具有讀取權限的帳戶上啟用 MFA）|高|N|訂用帳戶|
|**必須在訂用帳戶上具有寫入權限的帳戶上啟用 MFA**|為具有寫入權限的所有訂用帳戶啟用 Multi-Factor Authentication (MFA)，以避免發生帳戶或資源資料外洩。<br>（相關原則：您應該在訂用帳戶上具有寫入權限的帳戶上啟用 MFA）|高|N|訂用帳戶|
|**應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA**|為具有擁有者許可權的所有訂用帳戶啟用多重要素驗證（MFA），以避免發生帳戶或資源缺口。<br>（相關原則：必須在訂用帳戶上具有擁有者許可權的帳戶上啟用 MFA）|高|N|訂用帳戶|
|**具有讀取權限的外部帳戶應該從您的訂用帳戶中移除**|從訂用帳戶中移除具有讀取權限的外部帳戶，以避免出現未受監視的存取。<br>（相關原則：應從您的訂用帳戶移除具有讀取權限的外部帳戶）|高|N|訂用帳戶|
|**應從訂用帳戶移除具有寫入權限的外部帳戶**|從您的訂用帳戶中移除具有寫入權限的外部帳戶，以避免未受監視的存取。<br>（相關原則：必須從您的訂用帳戶移除具有寫入權限的外部帳戶）|高|N|訂用帳戶|
|**具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除**|從您的訂用帳戶中移除具有擁有者許可權的外部帳戶，以避免未受監視的存取。<br>（相關原則：必須從您的訂用帳戶移除具有擁有者許可權的外部帳戶）|高|N|訂用帳戶|
|**具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除**|從訂用帳戶中移除具有擁有者權限的已取代帳戶。<br>（相關原則：具有擁有者許可權的已淘汰帳戶應從您的訂用帳戶中移除）|高|N|訂用帳戶|
|**已取代帳戶應該從您的訂用帳戶中移除**|從訂用帳戶中移除不再使用的帳戶，以僅允許目前的使用者存取。<br>（相關原則：已淘汰的帳戶應從您的訂用帳戶移除）|高|N|訂用帳戶|
|**應將一個以上的擁有者指派給您的訂用帳戶**|指定多位訂用帳戶擁有者，以擁有系統管理員存取備援。<br>（相關原則：應將一個以上的擁有者指派給您的訂用帳戶）|高|N|訂用帳戶|
|**應針對您的訂用帳戶指定最多 3 位擁有者**|指定少於三個訂用帳戶擁有者，以降低受到遭入侵之擁有者入侵的可能性。<br>（相關原則：應為您的訂用帳戶指定最多3個擁有者）|高|N|訂用帳戶|
|**應啟用 Key Vault 中的診斷記錄**|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br>（相關原則： Key Vault 中的診斷記錄應該啟用）|低|**Y**|Key Vault|
||||||

## <a name="next-steps"></a>後續步驟
若要深入瞭解建議，請參閱下列各項：

* [Azure 資訊安全中心中的安全性建議](security-center-recommendations.md)
* [保護您的機器和應用程式](security-center-virtual-machine-protection.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)