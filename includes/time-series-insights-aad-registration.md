---
title: 包含檔案
description: 包含檔案
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984519"
---
1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，選取 [Azure Active Directory] > [應用程式註冊] > [新增註冊]。

   [![Azure Active Directory 中的新應用程式註冊](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    應用程式在註冊後便會在此列出。

1. 請為應用程式提供名稱，然後選取 [僅此組織目錄中的帳戶] 來指定可存取 API 的 [支援的帳戶類型]。 選擇要在使用者通過驗證後作為其重新導向目的地的有效 URI，然後選取 [註冊]。

   [![在 Azure Active Directory 中建立應用程式](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. 重要的 Azure Active Directory 應用程式資訊會顯示在所列出應用程式的 [概觀] 刀鋒視窗中。 在 [擁有的應用程式] 底下選取您的應用程式，然後選取 [概觀]。

   [![複製應用程式識別碼](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   複製 [應用程式 (用戶端) 識別碼] 以便用於用戶端應用程式。

1. [驗證] 刀鋒視窗會指定重要的驗證組態設定。 

    1. 藉由選取 [ **+ 新增平臺**] 來新增重新**導向 Uri**和設定**存取權杖**。

    1. 藉由選取 **[是] 或 [** **否**]，判斷應用程式是否為**公用用戶端**。

    1. 確認支援的帳戶和租使用者。

    [![設定隱含授與](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. 選取適當的平臺之後，請在使用者介面右邊的側邊面板中設定重新**導向 uri**和**存取權杖**。

    1. [重新導向 URI] 必須符合驗證要求所提供的位址：

        * 對於裝載在本機開發環境中的應用程式，請選取 [公用用戶端 (行動和傳統型)]。 請務必將 [**公用用戶端**] 設定為 **[是]** 。
        * 針對裝載于 Azure App Service 上的單一頁面應用程式，選取 [ **Web**]。

    1. 判斷**登出 URL**是否合適。

    1. 藉由檢查**存取權杖**或**識別碼權杖**來啟用隱含授與流程。

    [![建立重新導向 Uri](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    依序按一下 [**設定**] 和 [**儲存**]。

1. 選取 [**憑證 & 秘密**] [**新的用戶端密碼**] 來建立應用程式密碼，讓您的用戶端應用程式可用來證明其身分識別。

   [![建立新的用戶端密碼](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   接著將會顯示您的用戶端密碼。 將金鑰複製到您慣用的文字編輯器。

   > [!NOTE]
   > 您可以改用匯入憑證的功能。 為了增強安全性，建議使用憑證。 若要使用憑證，請選取 [上傳憑證]。

1. 讓 Azure Active Directory 應用程式與 Azure 時間序列深入解析建立關聯。 選取 [API 權限] > [新增權限] > [組織使用的 API]。 

    [![讓 API 與 Azure Active Directory 應用程式建立關聯](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   在搜尋列中輸入 `Azure Time Series Insights`，然後選取 `Azure Time Series Insights`。

1. 接下來，請指定應用程式所需的 API 權限類型。 根據預設，系統會醒目提示 [委派的權限]。 選擇某個權限類型，然後選取 [新增權限]。

    [![指定應用程式所需的 API 權限類型](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
