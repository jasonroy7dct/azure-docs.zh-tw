---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 59cf0b81363c724d709d85105525bece13743f26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474873"
---
#### <a name="applicationstabapplications"></a>[應用程式](#tab/applications/)

1. 選取**應用程式**。
1. 選取 *webapi1* 應用程式以開啟其**屬性**頁面。
1. 選取 [發佈的範圍]  。 發佈的範圍可以用來為用戶端應用程式授與對 Web API 的部份權限。
1. 針對 [範圍]  請輸入 `demo.read`，並輸入 `Read access to the web API` 作為 [描述]  。
1. 針對 [範圍]  請輸入 `demo.write`，並輸入 `Write access to the web API` 作為 [描述]  。
1. 選取 [儲存]  。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 選取 [應用程式註冊 (預覽)]  。
1. 選取 *webapi1* 應用程式以開啟其 [概觀]  頁面。
1. 在 [管理]  底下，選取 [公開 API]  。
1. 選取 [應用程式識別碼 URI]  旁邊的 [設定]  連結。
1. 以 `api` 取代預設值 (GUID)，然後選取 [儲存]  。 完整的 URI 隨即顯示，其格式應為 `https://your-tenant-name.onmicrosoft.com/api`。 當您的 Web 應用程式要求 API 的存取權杖時，應該將此 URI 新增為您為 API 定義之每個範圍的前置詞。
1. 在 [由此 API 定義的範圍]  底下，選取 [新增範圍]  。
1. 輸入下列值來建立範圍，以定義 API 的讀取權限，然後選取 [新增範圍]  ：
    1. **範圍名稱**：`demo.read`
    1. **管理員同意顯示名稱**：`Read access to demo API`
    1. **管理員同意描述**：`Allows read access to the demo API`
1. 選取 [新增範圍]  ，輸入下列值來新增範圍來定義 API 的寫入權限，然後選取 [新增範圍]  ：
    1. **範圍名稱**：`demo.write`
    1. **管理員同意顯示名稱**：`Write access to demo API`
    1. **管理員同意描述**：`Allows write access to the demo API`