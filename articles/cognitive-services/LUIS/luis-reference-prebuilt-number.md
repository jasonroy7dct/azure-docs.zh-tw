---
title: 編號預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 number 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6a59cf83b3912e31b8aae67319902ce516519af8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491290"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的數字預先建置實體
數值用來量化、表達及描述各項資訊的方式眾多。 本文只涵蓋部分可能的範例。 LUIS 可解譯使用者語句的變化，並傳回一致的數值。 因為此實體已經定型，所以您不需要將包含數字的範例語句加入應用程式意圖。 

## <a name="types-of-number"></a>數字類型
數字可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) GitHub 存放庫管理

## <a name="examples-of-number-resolution"></a>數字解析範例

| 語句        | 實體   | 解決方案 |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS 會在所傳回 JSON 回應的  **欄位中，包含 `builtin.number`** `resolution` 實體的辨識值。

## <a name="resolution-for-prebuilt-number"></a>解析預先建置的數字

系統會針對查詢傳回下列實體物件：

`order two dozen eggs`

#### <a name="v3-responsetabv3"></a>[V3 回應](#tab/V3)

下列 JSON 會將 `verbose` 參數設定為 `false`：

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 詳細回應](#tab/V3-verbose)

下列 JSON 會將 `verbose` 參數設定為 `true`：

```json
"entities": {
    "number": [
        24
    ],
    "$instance": {
        "number": [
            {
                "type": "builtin.number",
                "text": "two dozen",
                "startIndex": 6,
                "length": 9,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetabv2"></a>[V2 回應](#tab/V2)

下列範例顯示來自 LUIS 的 JSON 回應，其中包含「兩打」語句的值 24。

```json
"entities": [
  {
    "entity": "two dozen",
    "type": "builtin.number",
    "startIndex": 6,
    "endIndex": 14,
    "resolution": {
      "subtype": "integer",
      "value": "24"
    }
  }
]
```
* * * 

## <a name="next-steps"></a>後續步驟

深入瞭解[V3 預測端點](luis-migration-api-v3.md)。

了解 [currency](luis-reference-prebuilt-currency.md)、[ordinal](luis-reference-prebuilt-ordinal.md) 及 [percentage](luis-reference-prebuilt-percentage.md) 相關資訊。 
