---
title: 存取 Apache Hadoop YARN 應用程式記錄-Azure HDInsight
description: 了解如何使用命令列和網頁瀏覽器存取以 Linux 為基礎之 HDInsight (Apache Hadoop) 叢集上的 YARN 應用程式記錄。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76764387"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>在以 Linux 為基礎的 HDInsight 上存取 Apache Hadoop YARN 應用程式記錄

了解如何在 Azure HDInsight 中的 [Apache Hadoop](https://hadoop.apache.org/) 叢集上存取 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Yet Another Resource Negotiator) 應用程式記錄。

## <a name="what-is-apache-yarn"></a>什麼是 Apache YARN？

YARN 藉由將資源管理從應用程式排程/監視分離，支援多種程式設計模型 ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) 為其中之一)。 YARN 使用全域 *ResourceManager* (RM)、每一背景工作節點 *ResourceManager* (NM) 及每一應用程式 *ResourceManager* (AM)。 每一應用程式 AM 會與 RM 交涉用來執行您應用程式的資源 (CPU、記憶體、磁碟、網路)。 RM 會與 NM 合作來授與這些資源 (以「 *容器*」的形式授與)。 AM 則是負責追蹤 RM 指派給它之容器的進度。 視應用程式的本質而定，一個應用程式可能會需要許多容器。

每個應用程式都可能包含多個「應用程式嘗試」。 如果應用程式失敗，系統可能會以新的嘗試來重試它。 每個嘗試都在容器中執行。 從某方面來說，容器提供由 YARN 應用程式所執行之作業的基本單位內容。 所有於容器內容之內所執行的作業，都會在容器所配置的單一背景工作節點上執行。 如需進一步參考，請參閱[Hadoop：撰寫 YARN 應用程式](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)或[Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) 。

若要調整您的叢集以支援更高的處理輸送量[幾種不同的語言](hdinsight-autoscale-clusters.md) ，您可以使用[，手動自動調整或擴充您的](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters)叢集。

## <a name="yarn-timeline-server"></a>YARN Timeline Server

[Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) 提供已完成應用程式的相關泛型資訊

YARN Timeline Server 包含下列類型的資料：

* 應用程式識別碼，應用程式的唯一識別碼
* 啟動應用程式的使用者
* 為完成應用程式而進行之嘗試的相關資訊
* 任何指定之應用程式嘗試所使用的容器

## <a name="yarn-applications-and-logs"></a>YARN 應用程式和記錄

YARN 藉由將資源管理從應用程式排程/監視分離，支援多種程式設計模型 ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) 為其中之一)。 YARN 使用全域 *ResourceManager* (RM)、每一背景工作節點 *ResourceManager* (NM) 及每一應用程式 *ResourceManager* (AM)。 每一應用程式 AM 會與 RM 交涉用來執行您應用程式的資源 (CPU、記憶體、磁碟、網路)。 RM 會與 NM 合作來授與這些資源 (以「 *容器*」的形式授與)。 AM 則是負責追蹤 RM 指派給它之容器的進度。 視應用程式的本質而定，一個應用程式可能會需要許多容器。

每個應用程式都可能包含多個「應用程式嘗試」。 如果應用程式失敗，系統可能會以新的嘗試來重試它。 每個嘗試都在容器中執行。 從某方面來說，容器提供由 YARN 應用程式所執行之作業的基本單位內容。 所有於容器內容之內所執行的作業，都會在容器所配置的單一背景工作節點上執行。 如需進一步參考，請參閱[Apache HADOOP YARN 概念](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)。

應用程式記錄 (和關聯的容器記錄) 在對有問題的 Hadoop 應用程式進行偵錯上相當重要。 YARN 提供使用[記錄匯總](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/)功能來收集、匯總及儲存應用程式記錄檔的絕佳架構。 「記錄彙總」功能可使存取應用程式記錄更具確定性。 它能彙總背景工作節點上所有容器的記錄，並將它們依每個背景工作節點儲存成單一彙總記錄。 在應用程式完成之後，記錄檔會儲存在預設檔案系統上。 您的應用程式可能使用數百或數千個容器，但在單一背景工作節點上執行之所有容器的記錄一律彙總成單一檔案。 因此，您的應用程式所使用的每個背景工作節點只會有1個記錄。 根據預設，HDInsight 叢集 3.0 版和更新版本上會啟用記錄彙總。 彙總記錄位於叢集的預設儲存體。 下列路徑是記錄的 HDFS 路徑︰

```
/app-logs/<user>/logs/<applicationId>
```

在路徑中，`user` 是啟動應用程式的使用者名稱。 `applicationId` 是 YARN RM 指派給應用程式的唯一識別碼

匯總記錄檔無法直接讀取，因為它們是以[TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf)，以容器編制索引的[二進位格式](https://issues.apache.org/jira/browse/HADOOP-3315)寫入。 對於您感興趣的應用程式或容器，請使用 YARN ResourceManager 記錄或 CLI 工具來檢視純文字記錄。

## <a name="yarn-logs-in-an-esp-cluster"></a>Yarn ESP 叢集中的記錄

您必須將兩個設定新增至 Ambari 中的自訂 `mapred-site`。

1. 從網頁瀏覽器流覽至 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 從 Ambari UI 中，流覽至**MapReduce2** >  **Advanced**  > **自訂 mapred.max.split.size**  > 。

1. 新增下列*其中一*組屬性：

    **集合1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Set 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. 儲存變更並重新啟動所有受影響的服務。

## <a name="yarn-cli-tools"></a>YARN CLI 工具

1. 使用[ssh 命令](./hdinsight-hadoop-linux-use-ssh-unix.md)連接到您的叢集。 以您叢集的名稱取代 CLUSTERNAME，然後輸入命令，以編輯下面的命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 使用下列命令，列出目前正在執行之 Yarn 應用程式的所有應用程式識別碼：

    ```bash
    yarn top
    ```

    請記下要下載其記錄的 `APPLICATIONID` 資料行中的應用程式識別碼。

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. 您可以執行下列其中一個命令，以檢視這些純文字記錄：

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    執行這些命令時，請指定 &lt;applicationId>、&lt;user-who-started-the-application>、&lt;containerId> 及 &lt;worker-node-address> 資訊。

### <a name="other-sample-commands"></a>其他範例命令

1. 使用下列命令，下載所有應用程式主機的 Yarn 容器記錄。 這會以文字格式建立名為 `amlogs.txt` 的記錄檔。

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. 使用下列命令下載僅有最新應用程式主機的 Yarn 容器記錄：

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. 使用下列命令下載前兩部應用程式主機的 Yarn 容器記錄：

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. 使用下列命令下載所有 Yarn 容器記錄：

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. 使用下列命令下載特定容器的 Yarn 容器記錄：

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI 是在叢集前端節點上執行。 它是透過 Ambari web UI 來存取。 請使用下列步驟來檢視 YARN 記錄：

1. 在您的網頁瀏覽器中，瀏覽至 `https://CLUSTERNAME.azurehdinsight.net`。 將 CLUSTERNAME 取代為 HDInsight 叢集的名稱。

2. 從左邊的服務清單中選取 [YARN]。

    ![已選取 Apache Ambari Yarn 服務](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. 在 [快速連結] 下拉式清單中，選取其中一個叢集前端節點，然後選取 [ResourceManager 記錄]。

    ![Apache Ambari Yarn 快速連結](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    您會看到一份 YARN 記錄的連結清單。
