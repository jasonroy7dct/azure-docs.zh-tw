---
title: 針對 Azure Kubernetes Service 自訂 CoreDNS （AKS）
description: 瞭解如何使用 Azure Kubernetes Service （AKS）自訂 CoreDNS 以新增子域或擴充自訂 DNS 端點
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595819"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>使用 Azure Kubernetes Service 自訂 CoreDNS

Azure Kubernetes Service （AKS）會使用[CoreDNS][coredns]專案來進行叢集 DNS 管理，並使用所有的*1.12. x*和更高的叢集進行解析。 先前已使用 kube dns 專案。 此 kube 的 dns 專案現在已被取代。 如需 CoreDNS 自訂和 Kubernetes 的詳細資訊，請參閱[官方上游檔][corednsk8s]。

由於 AKS 是受控服務，因此您無法修改 CoreDNS （ *CoreFile*）的主要設定。 相反地，您會使用 Kubernetes *ConfigMap*來覆寫預設設定。 若要查看預設的 AKS CoreDNS ConfigMaps，請使用 `kubectl get configmaps --namespace=kube-system coredns -o yaml` 命令。

本文說明如何將 ConfigMaps 用於 AKS 中 CoreDNS 的基本自訂選項。 這種方式不同于在其他內容中設定 CoreDNS，例如使用 CoreFile。 請確認您正在執行的 CoreDNS 版本，因為設定值可能會在版本之間變更。

> [!NOTE]
> `kube-dns` 透過 Kubernetes config map 提供不同的[自訂選項][kubednsblog]。 CoreDNS 與 kube 的回溯**不**相容。 您先前使用的任何自訂都必須更新，才能與 CoreDNS 搭配使用。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

## <a name="what-is-supportedunsupported"></a>支援/不支援的內容

支援所有內建的 CoreDNS 外掛程式。 不支援附加元件/協力廠商外掛程式。

## <a name="rewrite-dns"></a>重寫 DNS

其中一個案例是執行即時 DNS 名稱重寫。 在下列範例中，將 `<domain to be written>` 取代為您自己的完整功能變數名稱。 建立名為 `corednsms.yaml` 的檔案，並貼上下列範例設定：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

使用[kubectl apply ConfigMap][kubectl-apply]命令來建立 ConfigMap，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f corednsms.yaml
```

若要確認已套用自訂，請使用[kubectl get configmaps][kubectl-get]並指定您的*coredns 自訂*ConfigMap：

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

現在強制 CoreDNS 重載 ConfigMap。 [Kubectl delete pod][kubectl delete]命令不具破壞性，也不會造成停機時間。 `kube-dns` pod 會遭到刪除，然後 Kubernetes 排程器會重新建立它們。 這些新的 pod 包含 TTL 值的變更。

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> 上述命令是正確的。 當我們變更 `coredns`時，部署會在 [ **kube-dns**名稱] 底下。

## <a name="custom-forward-server"></a>自訂轉寄伺服器

如果您需要為您的網路流量指定轉寄伺服器，您可以建立 ConfigMap 來自訂 DNS。 在下列範例中，使用您自己環境的值來更新 `forward` 名稱和位址。 建立名為 `corednsms.yaml` 的檔案，並貼上下列範例設定：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

如先前範例所示，使用[kubectl apply ConfigMap][kubectl-apply]命令來建立 ConfigMap，並指定 YAML 資訊清單的名稱。 然後，強制 CoreDNS 使用[kubectl delete pod][kubectl delete]重載 ConfigMap，讓 Kubernetes 排程器重新建立它們：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>使用自訂網域

您可能想要設定只能在內部解析的自訂網域。 例如，您可能會想要解析自訂網域*puglife*，這不是有效的最上層網域。 如果沒有自訂網域 ConfigMap，AKS 叢集就無法解析位址。

在下列範例中，請使用您自己環境的值，將自訂網域和 IP 位址更新為將流量導向至。 建立名為 `corednsms.yaml` 的檔案，並貼上下列範例設定：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

如先前範例所示，使用[kubectl apply ConfigMap][kubectl-apply]命令來建立 ConfigMap，並指定 YAML 資訊清單的名稱。 然後，強制 CoreDNS 使用[kubectl delete pod][kubectl delete]重載 ConfigMap，讓 Kubernetes 排程器重新建立它們：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>存根網域

CoreDNS 也可以用來設定存根網域。 在下列範例中，請使用您自己環境的值來更新自訂網域和 IP 位址。 建立名為 `corednsms.yaml` 的檔案，並貼上下列範例設定：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

如先前範例所示，使用[kubectl apply ConfigMap][kubectl-apply]命令來建立 ConfigMap，並指定 YAML 資訊清單的名稱。 然後，強制 CoreDNS 使用[kubectl delete pod][kubectl delete]重載 ConfigMap，讓 Kubernetes 排程器重新建立它們：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Hosts 外掛程式

所有的內建外掛程式都受到支援，這表示 CoreDNS [Hosts][coredns hosts]外掛程式也可以自訂：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>啟用記錄以進行 DNS 查詢的偵錯工具 

若要啟用 DNS 查詢記錄，請在您的 coredns 中套用下列設定-自訂 ConfigMap：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>後續步驟

本文說明了一些 CoreDNS 自訂的範例案例。 如需 CoreDNS 專案的詳細資訊，請參閱[CoreDNS 上游專案頁面][coredns]。

若要深入瞭解核心網路概念，請參閱[AKS 中應用程式的網路概念][concepts-network]。

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
