---
title: 在 Azure 容器實例中執行臉部容器
titleSuffix: Azure Cognitive Services
description: 將臉部容器部署至 Azure 容器實例，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 4f8a08697443ec6920b3fa73de19a950e54e7f40
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716254"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>將臉部容器部署至 Azure 容器實例

瞭解如何將認知服務[臉部](../face-how-to-install-containers.md)容器部署至 Azure[容器實例](https://docs.microsoft.com/azure/container-instances/)。 此程式示範如何建立 Azure 臉部資源。 然後，我們會討論如何提取相關聯的容器映射。 最後，我們強調了從瀏覽器執行這兩個協調流程的能力。 使用容器可以將開發人員的注意力轉移到管理基礎結構之外，改為專注于應用程式開發。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]