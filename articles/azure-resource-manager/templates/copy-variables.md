---
title: 定義變數的多個實例
description: 在 Azure Resource Manager 範本中使用複製作業，在建立變數時反復執行多次。
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 9e252a0b9721ffec99535c5d30e609e12e9e67eb
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210810"
---
# <a name="variable-iteration-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中的變數反復專案

本文說明如何在 Azure Resource Manager 範本中為變數建立一個以上的值。 藉由將**copy**元素新增至範本的 variables 區段，您可以在部署期間，動態設定變數的專案數。 您也可以避免重複範本語法。

您也可以使用 [複製] 搭配[資源中](copy-properties.md)的[資源](copy-resources.md)和屬性。

## <a name="variable-iteration"></a>變數反覆項目

Copy 元素具有下列一般格式：

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**Name**屬性是識別迴圈的任何值。 **Count**屬性會指定您想要用於變數的反覆運算次數。

**輸入**屬性會指定您想要重複的屬性。 您會建立從**輸入**屬性中的值所構造的元素陣列。 它可以是單一屬性（例如字串）或具有數個屬性的物件。

下列範例顯示如何建立字串值的陣列：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

上述範本會傳回具有下列值的陣列：

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

下一個範例顯示如何建立具有三個屬性的物件陣列-name、diskSizeGB 和 diskIndex。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

上述範例會傳回具有下列值的陣列：

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> 變數反復專案支援 offset 引數。 位移必須位於反復專案的名稱之後，例如 copyIndex （' diskNames '，1）。 如果您未提供位移值，第一個實例的預設值會是0。
>

您也可以在變數內使用 copy 元素。 下列範例會建立一個物件，其具有陣列做為其中一個值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

上述範例會傳回具有下列值的物件：

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

下一個範例顯示您可以使用 copy 搭配變數的不同方式。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>複製限制

計數不能超過800。

計數不可為負數。 如果您部署具有 Azure PowerShell 2.6 或更新版本的範本、Azure CLI 2.0.74 或更新版本，或 REST API **2019-05-10**版或更新版本，您可以將 count 設定為零。 舊版的 PowerShell、CLI 和 REST API 不支援 count 的零。

## <a name="example-templates"></a>範本的範例

下列範例顯示為變數建立一個以上值的常見案例。

|[範本]  |描述  |
|---------|---------|
|[複製變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |示範逐一查看變數的不同方式。 |
|[多個安全性規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |將數個安全性規則部署至網路安全性群組。 從參數建構安全性規則。 如需參數，請參閱[多個 NSG 參數檔案](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。 |

## <a name="next-steps"></a>後續步驟

* 如須逐步瀏覽教學課程，請參閱[教學課程：使用 Resource Manager 範本建立多個資源執行個體](template-tutorial-create-multiple-instances.md)。
* 如需 copy 元素的其他用法，請參閱[Azure Resource Manager 範本中](copy-properties.md)Azure Resource Manager 範本和屬性反復專案中的[資源反復](copy-resources.md)專案。
* 若要了解範本區段的相關資訊，請參閱[編寫 Azure Resource Manager 範本](template-syntax.md)。
* 若要了解如何部署範本，請參閱 [使用 Azure 資源管理員範本部署應用程式](deploy-powershell.md)。

