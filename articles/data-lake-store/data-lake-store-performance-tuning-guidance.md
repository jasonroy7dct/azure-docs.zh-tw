---
title: Azure Data Lake Storage Gen1-效能微調
description: 說明如何微調 Azure Data Lake Storage Gen1 的效能。
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 2521700e0f07691541ee6cbbf085a8be72f08129
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904616"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>微調 Azure Data Lake Storage Gen1 效能

Data Lake Storage Gen1 支援 i/o 密集分析和資料移動的高輸送量。 在 Data Lake Storage Gen1 中，使用所有可用的輸送量 (每秒可以讀取或寫入的資料量) 是取得最佳效能的重要部分。 這可以藉由盡可能平行執行讀取和寫入來達成。

![Data Lake Storage Gen1 效能](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 可以調整以提供所有分析情節的必要輸送量。 根據預設，Data Lake Storage Gen1 帳戶會自動提供足夠產能，以符合廣泛類別使用案例的需求。 客戶遇到預設限制的情況下，可以將 Data Lake Storage Gen1 帳戶設定為連絡 Microsoft 支援服務來提供更多輸送量。

## <a name="data-ingestion"></a>資料擷取

從來源系統內嵌資料到 Data Lake Storage Gen1 時，請務必考慮 Data Lake Storage Gen1 的來源硬體、來源網路硬體和網路連線能力可能是瓶頸。

![Data Lake Storage Gen1 效能](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

請務必確保資料移動不會受到這些因素的影響。

### <a name="source-hardware"></a>來源硬體

無論您使用的是內部部署機器或 Azure 中的 Vm，都應該小心地選取適當的硬體。 對於來源磁碟硬體，偏好使用 SSD 而非 HDD，並且挑選具有更快磁針的磁碟硬體。 對於來源網路硬體，盡可能使用最快的 NIC。 在 Azure 上，我們建議使用具有適當功能之磁片和網路硬體的 Azure D14 Vm。

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 的網路連接

您的來源資料與 Data Lake Storage Gen1 之間的網路連線有時可能是瓶頸。 當您的來源資料是內部部署時，請考慮使用與 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 的專用連結。 如果您的來源資料是在 Azure 中，當資料位於與 Data Lake Storage Gen1 相同的 Azure 區域時，效能最佳。

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>設定最大平行處理的資料擷取工具

在解決來源硬體和網路連線瓶頸之後，您就可以開始設定您的內嵌工具。 下表摘要說明數個熱門擷取工具的關鍵設定，並且提供它們的深入效能微調文章。 若要深入了解哪一個工具適用於您的案例，請參閱這篇[文章](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios)。

| 工具          | 設定 | 其他詳細資訊                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| Powershell       | PerFileThreadCount、ConcurrentFileCount | [連結](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy    | Azure Data Lake Analytics units | [連結](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapper) | [連結](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies | [連結](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size、-m (mapper) | [連結](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>結構化您的資料集

當資料儲存在 Data Lake Storage Gen1 中時，檔案大小、檔案數目和資料夾結構會影響效能。 下一節說明這些區域的最佳做法。

### <a name="file-size"></a>檔案大小

一般而言，例如 HDInsight 和 Azure Data Lake Analytics 的分析引擎都有每個檔案的額外負荷。 如果您將資料儲存為許多小型檔案，會造成效能的負面影響。

一般情況下，將您的資料組織成較大大小的檔案，以提升效能。 根據經驗法則，在 256 MB 或更大的檔案中組織資料集。 在例如映像和二進位資料的某些情況下，不能夠平行處理。 在這些情況下，建議您將個別檔案保留在 2 GB 以下。

有時候，資料管線對具有大量小型檔案的原始資料有有限的控制。 建議進行「cooking」程序，該程序會產生較大的檔案，以用於下游應用程式。

### <a name="organize-time-series-data-in-folders"></a>組織資料夾中的時間序列資料

針對 Hive 和 ADLA 工作負載，時間序列資料的分割區剪除可以協助某些查詢唯讀取資料的子集，進而改善效能。

這些管線會內嵌時間序列資料，通常會將其檔案與檔案和資料夾的結構化命名放在一起。 以下是我們針對以日期結構化的資料所見的常見範例：

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

請注意，日期時間資訊會同時在資料夾和檔案名稱中顯示。

對於日期和時間，以下是常見的模式

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

同樣地，您對於資料夾和檔案組織的選擇，應該針對較大的檔案大小以及每個資料夾中合理的檔案數目進行最佳化。

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>將 HDInsight 上的 Hadoop 和 Spark 工作負載上的 i/o 密集作業優化

作業屬於下列三個類別之一：

* **CPU 密集作業** 這些作業有很長的計算時間和最短的 I/O 時間。 範例包括機器學習和自然語言處理作業。
* **記憶體密集作業** 這些作業會使用大量記憶體。 範例包括 PageRank 和即時分析作業。
* **I/O 密集作業** 這些作業花費大部分時間執行 I/O。 常見的範例是僅執行讀取和寫入作業的複製工作。 其他範例包括讀取大量資料的資料準備工作、執行一些資料轉換，然後將資料寫回存放區。

下列指南僅適用於 I/O 密集作業。

### <a name="general-considerations-for-an-hdinsight-cluster"></a>HDInsight 叢集的一般考量

* **HDInsight 版本** 為了達到最佳效能，請使用最新版本的 HDInsight。
* **區域** 將 Data Lake Storage Gen1 帳戶放置在與 HDInsight 叢集相同的區域中。

HDInsight 叢集是由兩個前端節點和一些背景工作角色節點所組成。 每個背景工作角色節點提供特定數目的核心和記憶體，由 VM 類型決定。 執行作業時，YARN 是資源交涉程式，它會配置可用記憶體和核心來建立容器。 每個容器會執行完成作業所需的工作。 平行執行容器以快速地處理工作。 因此，盡可能平行執行最多容器可提升效能。

HDInsight 叢集內有三個層級可以微調，以增加容器數目並且使用所有可用的輸送量。

* **實體層**
* **YARN 層**
* **工作負載層**

### <a name="physical-layer"></a>實體層

**執行具有更多節點和/或較大大小 VM 的叢集。** 較大的叢集可讓您執行更多 YARN 容器，如下圖所示。

![Data Lake Storage Gen1 效能](./media/data-lake-store-performance-tuning-guidance/VM.png)

**使用具有較大網路頻寬的 VM。** 如果網路頻寬比 Data Lake Storage Gen1 輸送量小，網路頻寬量可能會是瓶頸。 不同 VM 會有不同的網路頻寬大小。 選擇具有最大可能網路頻寬的 VM 類型。

### <a name="yarn-layer"></a>YARN 層

**使用較小的 YARN 容器。** 減少每個 YARN 容器的大小以使用相同的資源量來建立更多容器。

![Data Lake Storage Gen1 效能](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

根據您的工作負載，一定有需要的最小 YARN 容器大小。 如果您挑選的容器太小，您的作業會遇到記憶體不足的問題。 通常 YARN 的容器不應小於 1 GB。 通常會看到 3 GB 的 YARN 容器。 針對某些工作負載，您可能需要較大的 YARN 容器。

**增加每個 YARN 容器的核心。** 增加配置給每個容器的核心數目，以增加在每個容器中執行的平行工作數目。 這適用于 Spark 之類的應用程式，它會針對每個容器執行多個工作。 對於在每個容器中執行單一執行緒的 Hive 之類的應用程式，最好擁有更多容器，而不是每個容器有更多核心。

### <a name="workload-layer"></a>工作負載層

**使用所有可用的容器。** 將工作數目設定為等於或大於可用容器的數目，以使用所有資源。

![Data Lake Storage Gen1 效能](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**失敗的工作成本很高。** 如果每項工作都有大量資料要處理，則工作失敗會造成昂貴的重試成本。 因此，最好是建立更多工具，每個工作都會處理少量的資料。

除了上述的一般方針，每個應用程式都有不同的參數可以針對該特定應用程式進行微調。 下表列出一些參數和連結，以開始進行每個應用程式的效能微調。

| 工作負載               | 設定工作的參數                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [HDInsight 上的 Spark](data-lake-store-performance-tuning-spark.md)  | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
| [Hive on HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce on HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm on HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>背景工作處理序數目</li><li>Spout 執行程式執行個體數目</li><li>Bolt 執行程式執行個體數目 </li><li>Spout 工作數目</li><li>Bolt 工作數目</li></ul>|

## <a name="see-also"></a>另請參閱

* [Azure Data Lake Storage Gen1 概觀](data-lake-store-overview.md)
* [開始使用 Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
