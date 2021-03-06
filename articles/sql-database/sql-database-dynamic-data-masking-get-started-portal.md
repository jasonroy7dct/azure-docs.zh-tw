---
title: Azure 入口網站：動態資料遮罩
description: 如何開始在 Azure 入口網站中使用 SQL Database 動態資料遮罩
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: a8098b31c6b389b640fc03e756da44c70d9f3a70
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722113"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>透過 Azure 入口網站開始使用 SQL Database 動態資料遮罩

本文說明如何使用 Azure 入口網站來實作[動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)。 您也可以使用 [Azure SQL Database Cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 來實作動態資料遮罩。

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>使用 Azure 入口網站為您的資料庫設定動態資料遮罩

1. 在 [https://portal.azure.com](https://portal.azure.com) 上啟動 Azure 入口網站。
2. 導覽至您要遮罩處理的敏感性資料所在資料庫的設定頁面。
3. 按一下 [動態資料遮罩] 圖格，以啟動 [動態資料遮罩] 組態頁面。

   * 或者，您可以向下捲動至 [作業] 區段，然後按一下 [動態資料遮罩]。

     ![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. 在 [動態資料遮罩] 設定頁面中，您可能會看到建議引擎已標示要進行遮罩處理的某些資料庫資料行。 若要接受建議，只要對一或多個資料行按一下 [新增遮罩] ，就會根據此資料行的預設類型建立遮罩。 您可按一下遮罩規則並編輯遮罩欄位格式，使其成為您選擇的不同格式，即可變更遮罩函數。 務必按一下 [儲存] 以儲存您的設定。

    ![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. 若要為資料庫中的任何資料行新增遮罩，請在 [動態資料遮罩] 設定頁面的頂端，按一下 [新增遮罩] 以開啟 [新增遮罩規則] 設定頁面。

    ![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. 請選取 [結構描述]、[資料表] 和 [資料行]，以定義要用於遮罩處理的指定欄位。
7. 從敏感性資料遮罩類別清單中，選擇 [遮罩欄位格式] 。

    ![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. 按一下資料遮罩規則頁面中的 [儲存]，以更新動態遮罩原則中的遮罩資料規則集。
9. 輸入應從遮罩處理中排除，並可存取未經遮罩處理之敏感性資料的 SQL 使用者或 AAD 身分識別。 這應該是以分號分隔的使用者清單。 具有系統管理員權限的使用者永遠都有未經遮罩處理之原始資料的存取權。

    ![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > 若要讓應用程式層級可以對具有特殊權限的應用程式使用者顯示敏感性資料，請新增應用程式用於查詢資料庫的 SQL 使用者或 ADD 身分識別。 強烈建議此清單應包含最少的特殊權限使用者數目，以儘可能減少公開的敏感性資料。

10. 按一下資料遮罩組態頁面中的 [儲存]，以儲存新的或更新的遮罩原則。

## <a name="next-steps"></a>後續步驟

* 如需動態資料遮罩的概觀，請參閱[動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)。
* 您也可以使用 [Azure SQL Database Cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 來實作動態資料遮罩。
