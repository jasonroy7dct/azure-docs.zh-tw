---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 4bd2af67ed0e570e8d171da678b69d0908627374
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593932"
---
若要刪除密碼，請執行下列命令：

```powershell
(kubectl get secret --all-namespaces -o json | ConvertFrom-Json).items.metadata |% { if ($_.name -match "istio.") { "Deleting {0}.{1}" -f $_.namespace, $_.name; kubectl delete secret -n $_.namespace $_.name } }
```
