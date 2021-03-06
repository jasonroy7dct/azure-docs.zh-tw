---
title: 容器安全性
titleSuffix: Azure Cognitive Services
description: 瞭解如何保護您的容器
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/30/2019
ms.author: dapine
ms.openlocfilehash: 35f5cffdc644370082e229c88d67db33e853c446
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499192"
---
## <a name="azure-cognitive-services-container-security"></a>Azure 認知服務容器安全性

在開發應用程式時，安全性應該是主要焦點。 安全性的重要性是成功的度量。 當您架構包含認知服務容器的軟體解決方案時，請務必瞭解您可用的限制和功能。 如需網路安全性的詳細資訊，請參閱[設定 Azure 認知服務虛擬網路][az-security]。

> [!IMPORTANT]
> 根據預設，認知服務容器 API*沒有安全性*。 這是因為容器最常作為 pod 的一部分來執行，而這是由網路橋接器的外部保護。 不過，您可以啟用驗證，其運作方式與存取[雲端式認知服務][request-authentication]時所使用的驗證相同。

下圖說明預設和**不安全**的方法：

![容器安全性](../media/container-security.svg)

作為另一個*安全*的方法，認知服務容器的取用者可以使用前方元件來增加容器，並將容器端點設為私用。 讓我們來看一個案例，我們會使用[Istio][istio]作為輸入閘道。 Istio 支援 HTTPS/SSL 和用戶端憑證驗證。 在此案例中，Istio 前端會公開容器存取權，呈現預先列入 Istio 的用戶端憑證。

[Nginx][nginx]是相同類別中另一個常用的選擇。 Istio 和 Nginx 都扮演服務網格，並提供額外的功能，包括負載平衡、路由及速率控制等專案。

### <a name="container-networking"></a>容器網路服務

需要認知服務容器，才能提交計量資訊以供計費之用。 唯一的例外是*離線容器*，因為它們遵循不同的計費方法。 若無法允許列出認知服務容器所依賴的各種網路通道，將會導致容器無法運作。

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>允許清單認知服務網域和埠

主機應允許列出**埠 443**和下列網域：

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>停用深度封包檢查

> [深度封包檢查](https://en.wikipedia.org/wiki/Deep_packet_inspection)（DPI）是一種資料處理類型，會詳細檢查透過電腦網路傳送的資料，而且通常會藉由封鎖、重新路由或適當地記錄來採取動作。

停用認知服務容器建立至 Microsoft 伺服器的安全通道上的 DPI。 若未這麼做，將會導致容器無法正常運作。

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
