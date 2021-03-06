---
title: 儲存 Helm 圖表
description: 瞭解如何在 Azure Container Registry 中使用存放庫來儲存 Kubernetes 應用程式的 Helm 圖表
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 26588bb4dc3cf50656103b50d5d0559908a1ccb7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524626"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>將 Helm 圖表推送並提取到 Azure container registry

若要快速管理及部署 Kubernetes 的應用程式，您可以使用[開放原始碼 Helm 套件管理員][helm]。 使用 Helm 時，應用程式套件會定義為[圖表](https://helm.sh/docs/topics/charts/)，並會收集並儲存在[Helm 圖表存放庫](https://helm.sh/docs/topics/chart_repository/)中。

本文說明如何使用 Helm 3 或 Helm 2 安裝，在 Azure container registry 中裝載存放庫中的 Helm 圖表。 在此範例中，您會從公用 Helm*穩定*存放庫儲存現有的 Helm 圖表。 在許多情況下，您會針對您所開發的應用程式建立並上傳您自己的圖表。 如需如何建立您自己的 Helm 圖表的詳細資訊，請參閱[圖表範本開發人員指南][develop-helm-charts]。

> [!IMPORTANT]
> Azure Container Registry 中的 Helm 圖表支援目前為預覽狀態。 您必須同意補充[使用][terms-of-use]規定，才能使用預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="helm-3-or-helm-2"></a>Helm 3 或 Helm 2？

若要儲存、管理和安裝 Helm 圖，您可以使用 Helm 用戶端和 Helm CLI。 Helm 用戶端的主要版本包括 Helm 3 和 Helm 2。 Helm 3 支援新的圖表格式，而且不再安裝 Tiller 伺服器端元件。 如需版本差異的詳細資訊，請參閱[版本常見問題](https://helm.sh/docs/faq/)。 如果您先前已部署 Helm 2 圖表，請參閱[將 Helm V2 遷移至 v3](https://helm.sh/docs/topics/v2_v3_migration/)。

您可以使用 Helm 3 或 Helm 2 來裝載 Azure Container Registry 中的 Helm 圖表，其中包含每個版本特定的工作流程：

* [Helm 3 用戶端](#use-the-helm-3-client)-使用 `helm chart` 命令，以[OCI 構件](container-registry-image-formats.md#oci-artifacts)的形式管理登錄中的圖表
* [Helm 2 客戶](#use-the-helm-2-client)端-使用 Azure CLI 中的[az acr Helm][az-acr-helm]命令，以 Helm 圖表存放庫來新增及管理您的容器登錄

### <a name="additional-information"></a>其他資訊

* 我們建議使用 Helm 3 工作流程搭配原生 `helm chart` 命令，將圖表當做 OCI 成品來管理。
* 您可以使用舊版[az acr helm][az-acr-helm] Azure CLI 命令和工作流程搭配 helm 3 用戶端和圖表。 不過，某些命令（例如 `az acr helm list`）與 Helm 3 圖表並不相容。
* 從 Helm 3 起， [az acr Helm][az-acr-helm]命令的支援主要是為了與 Helm 2 用戶端和圖表格式相容。 目前尚未規劃這些命令的未來開發。

## <a name="use-the-helm-3-client"></a>使用 Helm 3 用戶端

### <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶中**的 azure container registry** 。 如有需要，請使用[Azure 入口網站](container-registry-get-started-portal.md)或[Azure CLI](container-registry-get-started-azure-cli.md)來建立登錄。
- **Helm 用戶端版本3.0.0 或更新**版本-執行 `helm version` 以尋找您目前的版本。 如需有關如何安裝和升級 Helm 的詳細資訊，請參閱[安裝 Helm][helm-install]。
- **Kubernetes**叢集，您將在其中安裝 Helm 圖表。 如有需要，請建立[Azure Kubernetes Service][aks-quickstart]叢集。 
- **Azure CLI 版本2.0.71 或更新版本**-執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

### <a name="high-level-workflow"></a>高階工作流程

有了**Helm 3** ，您可以：

* 可以在 Azure container registry 中建立一或多個 Helm 存放庫
* 在登錄中將 Helm 3 圖表儲存為[OCI](container-registry-image-formats.md#oci-artifacts)成品。 目前，OCI 的 Helm 3 支援會視為*實驗*性。
* 直接從 Helm CLI 使用 `helm chart` 命令來推送、提取和管理登錄中的 Helm 圖表
* 透過 Azure CLI 驗證您的登錄，然後使用登錄 URI 和認證自動更新您的 Helm 用戶端。 您不需要手動指定此登錄資訊，因此不會在命令歷程記錄中公開認證。
* 使用 `helm install` 從本機存放庫快取將圖表安裝到 Kubernetes 叢集。

如需範例，請參閱下列各節。

### <a name="enable-oci-support"></a>啟用 OCI 支援

設定下列環境變數，以在 Helm 3 用戶端中啟用 OCI 支援。 目前，這種支援是實驗性。 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="pull-an-existing-helm-package"></a>提取現有的 Helm 套件

如果您尚未新增 `stable` Helm 圖表存放庫，請執行 `helm repo add` 命令：

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```

從本機 `stable` 存放庫提取圖表封裝。 例如，建立一個本機目錄（例如 *~/acr-helm*），然後下載現有的*穩定/wordpress*圖表套件。 （此範例和本文中的其他命令會針對 Bash shell 進行格式化）。

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm pull stable/wordpress --untar
```

`helm pull stable/wordpress` 命令未指定特定版本，因此會在 `wordpress` 子目錄中提取並解壓縮*最新*版本。

### <a name="save-chart-to-local-registry-cache"></a>將圖表儲存到本機登錄快取

將目錄變更為 `wordpress` 子目錄，其中包含 Helm 的圖表檔案。 然後，執行 `helm chart save` 將圖表的複本儲存在本機，同時使用登錄的完整名稱和目標存放庫和標籤來建立別名。 

在下列範例中，登錄名稱是*mycontainerregistry*，目標存放庫是*wordpress*，而靶心圖表表標記是*最新*的，但您的環境值會是：

```console
cd wordpress
helm chart save . wordpress:latest
helm chart save . mycontainerregistry.azurecr.io/helm/wordpress:latest
```

執行 `helm chart list` 以確認您已將圖表儲存在本機登錄快取中。 輸出會類似：

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
wordpress:latest                                         wordpress       8.1.0   5899db0 29.1 KiB        1 day 
mycontainerregistry.azurecr.io/helm/wordpress:latest     wordpress       8.1.0   5899db0 29.1 KiB        1 day 
```

### <a name="push-chart-to-azure-container-registry"></a>將圖表推送至 Azure Container Registry

在 Helm 3 CLI 中執行 `helm chart push` 命令，將 Helm 圖表推送至 Azure container registry 中的存放庫。 如果不存在，則會建立存放庫。

首先，使用 Azure CLI 命令[az acr login][az-acr-login]來驗證您的登錄：

```azurecli
az acr login --name mycontainerregistry
```

將圖表推送至完整的目標存放庫：

```console
helm chart push mycontainerregistry.azurecr.io/helm/wordpress:latest
```

成功推送之後，輸出類似于：

```console
The push refers to repository [mycontainerregistry.azurecr.io/helm/wordpress]
ref:     mycontainerregistry.azurecr.io/helm/wordpress:latest
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    29.1 KiB
name:    wordpress
version: 8.1.0
```

### <a name="list-charts-in-the-repository"></a>列出存放庫中的圖表

如同儲存在 Azure container registry 中的映射，您可以使用[az acr repository][az-acr-repository]命令來顯示裝載圖表的存放庫，以及圖表標記和資訊清單。 

例如，執行[az acr repository show][az-acr-repository-show]以查看您在上一個步驟中建立的存放庫屬性：

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/wordpress
```

輸出會類似：

```console
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-01-29T16:54:30.1514833Z",
  "imageName": "helm/wordpress",
  "lastUpdateTime": "2020-01-29T16:54:30.4992247Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

執行[az acr repository show-資訊清單][az-acr-repository-show-manifests]命令，以查看儲存在存放庫中的圖表詳細資料。 例如，

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/wordpress --detail
```

輸出（在此範例中為縮寫）顯示 `application/vnd.cncf.helm.config.v1+json`的 `configMediaType`：

```console
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-01-29T16:54:30.2382436Z",
    "digest": "sha256:xxxxxxxx51bc0807bfa97cb647e493ac381b96c1f18749b7388c24bbxxxxxxxxx",
    "imageSize": 29995,
    "lastUpdateTime": "2020-01-29T16:54:30.3492436Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "latest"
    ]
  }
]
```

### <a name="pull-chart-to-local-cache"></a>將圖表提取至本機快取

若要將 Helm 圖表安裝到 Kubernetes，此圖表必須位於本機快取中。 在此範例中，先執行 `helm chart remove` 以移除名為 `mycontainerregistry.azurecr.io/helm/wordpress:latest`的現有本機圖表：

```console
helm chart remove mycontainerregistry.azurecr.io/helm/wordpress:latest
```

執行 `helm chart pull`，以從 Azure container registry 將圖表下載到本機快取：

```console
helm chart pull mycontainerregistry.azurecr.io/helm/wordpress:latest
```

### <a name="export-helm-chart"></a>匯出 Helm 圖表

若要使用圖表進一步工作，請使用 `helm chart export`將其匯出至本機目錄。 例如，將您提取的圖表匯出至 `install` 目錄：

```console
helm chart export mycontainerregistry.azurecr.io/helm/wordpress:latest --destination ./install
```

若要在存放庫中查看匯出之圖表的資訊，請在您匯出圖表的目錄中執行 `helm inspect chart` 命令。

```console
cd install
helm inspect chart wordpress
```

未提供任何版本號碼時，會使用「最新」版本。 Helm 會傳回有關您圖表的詳細資訊，如下列壓縮輸出所示：

```
apiVersion: v1
appVersion: 5.3.2
dependencies:
- condition: mariadb.enabled
  name: mariadb
  repository: https://kubernetes-charts.storage.googleapis.com/
  tags:
  - wordpress-database
  version: 7.x.x
description: Web publishing platform for building blogs and websites.
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
```

### <a name="install-helm-chart"></a>安裝 Helm 圖表

執行 `helm install` 以安裝提取至本機快取並匯出的 Helm 圖表。 請指定發行名稱，或傳遞 `--generate-name` 參數。 例如，

```console
helm install wordpress --generate-name
```

當安裝繼續進行時，請遵循命令輸出中的指示，以查看 WorPress 的 Url 和認證。 您也可以執行 `kubectl get pods` 命令，以查看透過 Helm 圖表部署的 Kubernetes 資源：

```console
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>從存放庫中刪除 Helm 圖表

若要從存放庫中刪除圖表，請使用[az acr repository delete][az-acr-repository-delete]命令。 執行下列命令，並在出現提示時確認操作：

```azurecli
az acr repository delete --name mycontainerregistry --image helm/wordpress:latest
```

## <a name="use-the-helm-2-client"></a>使用 Helm 2 用戶端

### <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶中**的 azure container registry** 。 如有需要，請使用[Azure 入口網站](container-registry-get-started-portal.md)或[Azure CLI](container-registry-get-started-azure-cli.md)來建立登錄。
- **Helm 用戶端 2.11.0 版 (不是 RC 版本) 或更新版本** - 執行 `helm version` 以找出您目前的版本。 此外，您還需要一部在 Kubernetes 叢集內初始化的 Helm 伺服器 (Tiller)。 如有需要，請建立[Azure Kubernetes Service][aks-quickstart]叢集。 如需有關如何安裝和升級 Helm 的詳細資訊，請參閱[安裝 Helm][helm-install-v2]。
- **Azure CLI 2.0.46 版或更新版本** - 請執行 `az --version` 來找出版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

### <a name="high-level-workflow"></a>高階工作流程

使用**Helm 2** ，您可以：

* 將您的 Azure container registry 設定為*單一*Helm 圖存放庫。 當您在存放庫中新增和移除圖表時，Azure Container Registry 管理索引定義。
* 使用 Azure CLI 中的[az acr helm][az-acr-helm]命令，將您的 Azure container registry 新增為 helm 圖表存放庫，以及推送和管理圖表。 這些 Azure CLI 命令會包裝 Helm 2 用戶端命令。
* 將 Azure container registry 中的圖表儲存機制新增至您的本機 Helm 存放庫索引，以支援圖表搜尋
* 透過 Azure CLI 向您的 Azure container registry 進行驗證，然後使用登錄 URI 和認證自動更新您的 Helm 用戶端。 您不需要手動指定此登錄資訊，因此不會在命令歷程記錄中公開認證。
* 使用 `helm install` 從本機存放庫快取將圖表安裝到 Kubernetes 叢集。

如需範例，請參閱下列各節。

### <a name="add-repository-to-helm-client"></a>將存放庫新增至 Helm 用戶端

使用[az acr Helm repository add][az-acr-helm-repo-add]命令，將您的 Azure Container Registry Helm 圖表存放庫新增至 Helm 用戶端。 此命令會取得 Helm 用戶端所使用 Azure Container Registry 的驗證權杖。 驗證權杖的有效時間為3小時。 與 `docker login` 類似，您可以在未來的 CLI 工作階段中執行此命令，以向 Azure Container Registry Helm 圖表存放庫驗證 Helm 用戶端：

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-chart-to-the-repository"></a>將圖表新增至存放庫

首先，在 *~/acr-helm*建立本機目錄，然後下載現有的*穩定/wordpress*圖表：

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

輸入 `ls` 以列出已下載的圖表，並記下檔案名中包含的 Wordpress 版本。 `helm fetch stable/wordpress` 命令並未指定特定的版本，因此擷取的是「最新」版本。 在下列範例輸出中，Wordpress 圖表是版本*8.1.0*：

```
wordpress-8.1.0.tgz
```

使用 Azure CLI 中的[az acr Helm push][az-acr-helm-push]命令，將圖表推送至 Azure Container Registry 中的 Helm 圖表存放庫。 指定您在上一個步驟中下載的 Helm 圖表名稱，例如*wordpress-8.1.0. tgz*：

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

幾分鐘後，Azure CLI 會報告您的圖表已儲存，如下列範例輸出所示：

```
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>列出存放庫中的圖表

若要使用在上一個步驟中上傳的圖表，必須更新本機 Helm 存放庫索引。 您可以為 Helm 用戶端中的存放庫重新編製索引，或使用 Azure CLI 來更新存放庫索引。 每次您將圖表新增至存放庫時，都必須完成此步驟：

```azurecli
az acr helm repo add --name mycontainerregistry
```

透過將圖表儲存在您的存放庫中，並在本機提供已更新的索引，您便可以使用一般 Helm 用戶端命令來進行搜尋或安裝。 若要查看存放庫中的所有圖表，請使用 `helm search` 命令，並提供您自己的 Azure Container Registry 名稱：

```console
helm search mycontainerregistry
```

這會列出在上一個步驟中推送的 Wordpress 圖表，如以下範例輸出所示：

```
NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  8.1.0           5.3.2       Web publishing platform for building blogs and websites.
```

您也可以使用[az acr helm list][az-acr-helm-list]，列出具有 Azure CLI 的圖表：

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>顯示 Helm 圖表的資訊

若要在存放庫中查看特定圖表的資訊，您可以使用 `helm inspect` 命令。

```console
helm inspect mycontainerregistry/wordpress
```

未提供任何版本號碼時，會使用「最新」版本。 Helm 會傳回您圖表的相關詳細資訊，如以下扼要的範例輸出所示：

```
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

您也可以使用 Azure CLI [az acr helm show][az-acr-helm-show]命令來顯示圖表的資訊。 同樣地，預設會傳回圖表的「最新」版本。 您可以附加 `--version` 來列出特定版本的圖表，例如*8.1.0*：

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>從存放庫安裝 Helm 圖表

您存放庫中的 Helm 圖表會藉由指定存放庫名稱和圖表名稱來安裝。 使用 Helm 用戶端來安裝 Wordpress 圖表：

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> 如果您推送至 Azure Container Registry Helm 圖表存放庫並稍後回到新的 CLI 工作階段，您的本機 Helm 用戶端就會需要已更新的驗證權杖。 若要取得新的驗證權杖，請使用[az acr helm][az-acr-helm-repo-add]存放庫 add 命令。

下列步驟會在安裝程序期間完成：

- Helm 用戶端會搜尋本機存放庫索引。
- 對應的圖表會下載自 Azure Container Registry 存放庫。
- 此圖表是使用您 Kubernetes 叢集中的 Tiller 來部署的。

當安裝繼續進行時，請遵循命令輸出中的指示，以查看 WorPress 的 Url 和認證。 您也可以執行 `kubectl get pods` 命令，以查看透過 Helm 圖表部署的 Kubernetes 資源：

```
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>從存放庫中刪除 Helm 圖表

若要從存放庫中刪除圖表，請使用[az acr helm delete][az-acr-helm-delete]命令。 指定圖表的名稱，例如*wordpress*，以及要刪除的版本，例如*8.1.0*。

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

如果您想要刪除所指定圖表的所有版本，請省略 `--version` 參數。

當您執行 `helm search`時，會繼續傳回圖表。 同樣地，Helm 用戶端並不會自動更新存放庫中的可用圖表清單。 若要更新 Helm 用戶端存放庫索引，請再次使用[az acr Helm][az-acr-helm-repo-add]存放庫 add 命令：

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>後續步驟

此文章使用了來自公用 *stable* 存放庫的現有 Helm 圖表。 如需如何建立和部署 Helm 圖表的詳細資訊，請參閱[開發 Helm 圖表][develop-helm-charts]。

Helm 圖表可以用來作為容器建置程序的一部分。 如需詳細資訊，請參閱[使用 Azure Container Registry 工作][acr-tasks]。

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
