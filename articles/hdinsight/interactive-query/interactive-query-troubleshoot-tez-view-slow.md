---
title: Apache Ambari Tez View 在 Azure HDInsight 中載入緩慢
description: Apache Ambari Tez View 可能會載入緩慢或根本不會載入 Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f4b1ffbc1e5b8147279d1e0320bd5f55aec90ea2
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895100"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>案例： Apache Ambari Tez View 在 Azure HDInsight 中載入緩慢

本文說明在 Azure HDInsight 叢集中使用互動式查詢元件時，針對問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache Ambari Tez View 可能會載入緩慢或根本不會載入。 載入 Ambari Tez View 時，您可能會看到前端節點上的處理常式變得沒有回應。

## <a name="cause"></a>原因

當叢集上有大量的 Hive 作業執行時，存取 Yarn ATS Api 有時可能會在2017年10月之前建立的叢集上產生效能不佳的情況。

## <a name="resolution"></a>解析度

這是2017年10月已修正的問題。 重新建立叢集之後，就不會再次遇到此問題。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源，來改善客戶體驗：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
