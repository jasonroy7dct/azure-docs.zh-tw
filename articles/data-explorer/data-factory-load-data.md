---
title: 將資料從 Azure Data Factory 複製到 Azure 資料總管
description: 在本文中，您將瞭解如何使用 Azure Data Factory 複製工具，將資料內嵌（載入）至 Azure 資料總管。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300591"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure 資料總管 

Azure 資料總管是一種快速、完全受控的資料分析服務。 它會針對從許多來源（例如應用程式、網站和 IoT 裝置）串流的大量資料提供即時分析。 有了 Azure 資料總管，您就可以反復探索資料，並找出模式和異常，以改善產品、增強客戶體驗、監視裝置，以及提升營運效率。 它可協助您探索新的問題，並在幾分鐘內獲得解答。 

Azure Data Factory 是完全受控且以雲端為基礎的資料整合服務。 您可以使用它，以現有系統的資料填入您的 Azure 資料總管資料庫。 當您建立分析解決方案時，它可協助您節省時間。

當您將資料載入 Azure 資料總管時，Data Factory 提供下列優點：

* **輕鬆設定**：取得直覺、五個步驟的 wizard，而不需要撰寫腳本。
* **豐富的資料存放區支援**：取得一組豐富內部部署和雲端式資料存放區的內建支援。 如需詳細清單，請參閱[支援的資料存放區](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)的資料表。
* **安全且符合規範**：資料會透過 HTTPS 或 Azure ExpressRoute 傳輸。 具有全域服務，可確保資料絕不會離開地理界限。
* **高效**能：資料載入速度最多每秒 1 gb （GBps）至 Azure 資料總管。 如需詳細資訊，請參閱[複製活動效能](/azure/data-factory/copy-activity-performance)。

在本文中，您會使用 Data Factory 資料複製工具，將資料從 Amazon Simple Storage Service （S3）載入至 Azure 資料總管。 您可以遵循類似的程式，從其他資料存放區複製資料，例如：
* [Azure Blob 儲存體](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL 資料倉儲](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [檔案系統](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。
* 資料的來源。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 登入 [Azure 入口網站](https://ms.portal.azure.com)。

1. 在左窗格中，選取 [**建立資源** > **分析** > ] [**Data Factory**]。

   ![在 Azure 入口網站中建立資料處理站](media/data-factory-load-data/create-adf.png)

1. 在 [**新增 data factory** ] 窗格中，提供下表中的欄位值：

   ![[新增資料處理站] 窗格](media/data-factory-load-data/my-new-data-factory.png)  

   | 設定  | 要輸入的值  |
   |---|---|
   | **名稱** | 在方塊中，輸入資料處理站的全域唯一名稱。 如果您收到錯誤，表示*data factory 名稱\"LoadADXDemo\"無法使用*，請為資料處理站輸入不同的名稱。 如需命名 Data Factory 成品的相關規則，請參閱[Data Factory 命名規則](/azure/data-factory/naming-rules)。|
   | **訂用帳戶** | 在下拉式清單中，選取要在其中建立資料處理站的 Azure 訂用帳戶。 |
   | **資源群組** | 選取 **[新建]** ，然後輸入新資源群組的名稱。 如果您已經有資源群組，請選取 [**使用現有**的]。 |
   | **版本** | 在下拉式清單中，選取 [ **V2**]。 |  
   | **位置** | 在下拉式清單中，選取 data factory 的 [位置]。 清單中只會顯示支援的位置。 Data factory 所使用的資料存放區可存在於其他位置或區域中。 |

1. 選取 [建立]。

1. 若要監視建立程式，請選取工具列上的 [**通知**]。 建立 data factory 之後，請選取它。
   
   [ **Data Factory** ] 窗格隨即開啟。

   ![[Data Factory] 窗格](media/data-factory-load-data/data-factory-home-page.png)

1. 若要在另一個窗格中開啟應用程式，請選取 [**作者 & 監視器**] 磚。

## <a name="load-data-into-azure-data-explorer"></a>將資料載入 Azure 資料總管

您可以從許多類型的資料存放[區](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)將資料載入 Azure 資料總管。 本文討論如何從 Amazon S3 載入資料。

您可以透過下列其中一種方式來載入資料：

* 在 [Azure Data Factory] 使用者介面的左窗格中，選取 [**作者**] 圖示，如[使用 Azure Data Factory UI 建立資料](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)處理站中的「建立資料處理站」一節所示。
* 在 Azure Data Factory 資料複製工具中，如[使用資料複製工具複製資料](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)所示。

### <a name="copy-data-from-amazon-s3-source"></a>從 Amazon S3 （來源）複製資料

1. 在 [**現在就開始**吧] 窗格中，選取 [**資料複製**] 來開啟 [資料複製] 工具。

   ![[資料複製工具] 按鈕](media/data-factory-load-data/copy-data-tool-tile.png)

1. 在 [**屬性**] 窗格的 [工作**名稱**] 方塊中，輸入名稱，然後選取 **[下一步]** 。

    ![資料複製屬性 窗格](media/data-factory-load-data/copy-from-source.png)

1. 在 [**來源資料存放區**] 窗格中，選取 [**建立新連接**]。

    ![資料複製的 [來源資料存放區] 窗格](media/data-factory-load-data/source-create-connection.png)

1. 選取 [ **Amazon S3**]，然後選取 [**繼續**]。

    ![新增連結服務窗格](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. 在 [**新增連結服務（Amazon S3）** ] 窗格中，執行下列動作：

    ![指定 Amazon S3 連結服務](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. 在 [**名稱**] 方塊中，輸入新連結服務的名稱。

    b. 在 [透過**整合運行**時間連線] 下拉式清單中，選取 [值]。

    c. 在 [**存取金鑰識別碼**] 方塊中，輸入值。
    
    > [!NOTE]
    > 在 Amazon S3 中，若要尋找您的存取金鑰，請在導覽列上選取您的 Amazon 使用者名稱，然後選取 [**我的安全性認證**]。
    
    d. 在 [**秘密存取金鑰**] 方塊中，輸入值。

    e. 若要測試您所建立的連結服務連線，請選取 [**測試連接**]。

    f. 選取 [完成]。
    
      [**來源資料存放區**] 窗格會顯示您的新 AmazonS31 連接。 

1. 選取 [下一步]。

   ![來源資料存放區已建立連接](media/data-factory-load-data/source-data-store-created-connection.png)

1. 在 [**選擇輸入檔案或資料夾**] 窗格中，執行下列動作：

    a. 流覽至您要複製的檔案或資料夾，然後選取它。

    b. 選取您想要的複製行為。 請確定已清除 [**二進位複製**] 核取方塊。

    c. 選取 [下一步]。

    ![選擇輸入檔案或資料夾](media/data-factory-load-data/source-choose-input-file.png)

1. 在 [**檔案格式設定**] 窗格中，選取檔案的相關設定。 然後選取 **[下一步]** 。

   ![[檔案格式設定] 窗格](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>將資料複製到 Azure 資料總管（目的地）

建立新的 Azure 資料總管連結服務，以將資料複製到本節所指定的 Azure 資料總管目的地資料表（接收）中。

#### <a name="create-the-azure-data-explorer-linked-service"></a>建立 Azure 資料總管連結服務

若要建立 Azure 資料總管連結服務，請執行下列動作：

1. 若要使用現有的資料存放區連線，或指定新的資料存放區，請在 [**目的地資料存放區**] 窗格中，選取 [**建立新連接**]。

    ![目的地資料存放區窗格](media/data-factory-load-data/destination-create-connection.png)

1. 在 [**新增連結服務**] 窗格中，選取 [ **Azure 資料總管**]，然後選取 [**繼續**]。

    ![新增連結服務窗格](media/data-factory-load-data/adx-select-new-linked-service.png)

1. 在 [**新增連結服務（Azure 資料總管）** ] 窗格中，執行下列動作：

    ![Azure 資料總管新增連結服務窗格](media/data-factory-load-data/adx-new-linked-service.png)

   a. 在 [**名稱**] 方塊中，輸入 Azure 資料總管連結服務的名稱。

   b. 在 [**帳戶選取方法**] 底下，執行下列其中一項動作： 

    * 選取 [**從 Azure 訂用**帳戶]，然後在下拉式清單中**選取您的** **Azure 訂**用帳戶和叢集。 

        > [!NOTE]
        > [叢集] 下拉式清單控制項只會列出與您的訂用帳戶相關**聯的叢集**。

    * 選取 [**手動輸入**]，然後輸入您的**端點**。

   c. 在 [**租**使用者] 方塊中，輸入租使用者名稱。

   d. 在 [**服務主體識別碼**] 方塊中，輸入服務主體識別碼。

   e. 選取 [**服務主體金鑰**]，然後在 [**服務主體金鑰**] 方塊中，輸入金鑰的值。

   f. 在 [**資料庫**] 下拉式清單中，選取您的資料庫名稱。 或者，選取 [**編輯**] 核取方塊，然後輸入資料庫名稱。

   g. 若要測試您所建立的連結服務連線，請選取 [**測試連接**]。 如果您可以連接到已連結的服務，窗格會顯示綠色核取記號和連線**成功**訊息。

   h. 選取 **[完成]** 以完成已連結的服務建立。

    > [!NOTE]
    > Azure Data Factory 使用服務主體來存取 Azure 資料總管服務。 若要建立服務主體，請移至[建立 Azure Active Directory （Azure AD）服務主體](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal)。 請勿使用 Azure Key Vault 方法。

#### <a name="configure-the-azure-data-explorer-data-connection"></a>設定 Azure 資料總管資料連線

建立連結服務連線之後，[**目的地資料存放區**] 窗格隨即開啟，而您建立的連線可供使用。 若要設定連接，請執行下列動作：

1. 選取 [下一步]。

    ![Azure 資料總管的 [目的地資料存放區] 窗格](media/data-factory-load-data/destination-data-store.png)

1. 在 [**資料表對應**] 窗格中，設定目的地資料表名稱，然後選取 **[下一步]** 。

    ![目的地資料集的 [資料表對應] 窗格](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. 在 [資料**行對應**] 窗格中，會進行下列對應：

    a. 第一個對應是 Azure Data Factory 根據[Azure Data Factory 架構對應](/azure/data-factory/copy-activity-schema-and-type-mapping)來執行。 請執行下列動作：

    * 設定 Azure Data Factory 目的地資料表的資料**行**對應。 預設的對應會從來源顯示到 Azure Data Factory 目的地資料表。

    * 取消選取您不需要定義資料行對應的資料行。

    b. 當此表格式資料內嵌至 Azure 資料總管時，就會發生第二個對應。 對應是根據[CSV 對應規則](/azure/kusto/management/mappings#csv-mapping)來執行。 即使來源資料不是 CSV 格式，Azure Data Factory 會將資料轉換成表格式格式。 因此，CSV 對應是此階段中唯一的相關對應。 請執行下列動作：

    * 選擇性在 **[Azure 資料總管（Kusto）接收屬性**] 下，新增相關的內嵌**對應名稱**，以便使用資料行對應。

    * 如果未指定內嵌**對應名稱**，則會使用 [資料**行**對應] 區段中所定義的*依名稱*對應順序。 如果*依名稱*對應失敗，Azure 資料總管會嘗試以逐*欄的位置*順序內嵌資料（亦即，它會依位置對應為預設值）。

    * 選取 [下一步]。

    ![目的地資料集的 [資料行對應] 窗格](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. 在 [**設定**] 窗格中，執行下列動作：

    a. 在 [**容錯設定**] 下，輸入相關的設定。

    b. 在 [**效能設定**] 底下，[**啟用暫存**] 不適用，而 [**高級設定**] 則包含成本考慮。 如果您沒有特定的需求，請保留這些設定。

    c. 選取 [下一步]。

    ![複製資料的 [設定] 窗格](media/data-factory-load-data/copy-data-settings.png)

1. 在 [**摘要**] 窗格中，檢查設定，然後選取 **[下一步]** 。

    ![[複製資料] [摘要] 窗格](media/data-factory-load-data/copy-data-summary.png)

1. 在 [**部署完成**] 窗格中，執行下列動作：

    a. 若要切換至 [**監視**] 索引標籤並查看管線的狀態（也就是進度、錯誤和資料流程），請選取 [**監視**]。

    b. 若要編輯連結的服務、資料集和管線，請選取 [**編輯管線**]。

    c. 選取 **[完成]** 以完成「複製資料」工作。

    ![[部署完成] 窗格](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>後續步驟

* 瞭解 Azure Data Factory 中的[Azure 資料總管連接器](/azure/data-factory/connector-azure-data-explorer)。
* 深入瞭解如何在[DATA FACTORY UI](/azure/data-factory/quickstart-create-data-factory-portal)中編輯連結的服務、資料集和管線。
* 瞭解用於資料查詢的[Azure 資料總管查詢](/azure/data-explorer/web-query-data)。
