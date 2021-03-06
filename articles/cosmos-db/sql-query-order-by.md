---
title: Azure Cosmos DB 中的 ORDER BY 子句
description: 瞭解 Azure Cosmos DB 的 SQL ORDER BY 子句。 使用 SQL 做為 Azure Cosmos DB JSON 查詢語言。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188740"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 ORDER BY 子句

選擇性的 ORDER BY 子句會指定查詢所傳回之結果的排序次序。

## <a name="syntax"></a>語法
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>引數
  
- `<sort_specification>`  
  
   指定要排列查詢結果集的屬性或運算式。 排序資料行可以指定為名稱或屬性別名。  
  
   可以指定多個屬性。 屬性名稱必須是唯一的。 ORDER BY 子句中排序屬性的順序會定義排序結果集的組織。 也就是說，結果集是依第一個屬性排序，而該排序清單會依次要屬性進行排序，以此類推。  
  
   ORDER BY 子句中所參考的屬性名稱，必須對應至 select 清單中的屬性，或在 FROM 子句中指定之集合中所定義的屬性，而不會有任何多義性。  
  
- `<sort_expression>`  
  
   指定要對其排序查詢結果集的一或多個屬性或運算式。  
  
- `<scalar_expression>`  
  
   請參閱[純量運算式](sql-query-scalar-expressions.md)一節以取得詳細資料。  
  
- `ASC | DESC`  
  
   指定指定之資料行的值應該以遞增或遞減順序排序。 ASC 從最低值到最高值進行排序。 DESC 從最高值到最低值進行排序。 ASC 是預設排序次序。 Null 值會當做最低的可能值來處理。  
  
## <a name="remarks"></a>備註  
  
   `ORDER BY` 子句需要索引編制原則包含要排序之欄位的索引。 Azure Cosmos DB 查詢執行時間支援針對屬性名稱進行排序，而不是針對計算的屬性。 Azure Cosmos DB 支援多個 `ORDER BY` 屬性。 若要以多個 ORDER BY 屬性執行查詢，您應該在要排序的欄位上定義[複合索引](index-policy.md#composite-indexes)。

> [!Note]
> 如果要排序的屬性可能未針對某些檔定義，而您想要在「排序依據」查詢中加以取出，您必須在索引中明確包含此路徑。 預設的索引編制原則不允許抓取未定義 sort 屬性的檔。 [在含有一些遺漏欄位的檔上，檢查範例查詢](#documents-with-missing-fields)。

## <a name="examples"></a>範例

例如，以下是以居住城市名稱的遞增順序來抓取家族的查詢：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

結果如下：

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

下列查詢會依其專案建立日期的順序，來抓取家族 `id`s。 專案 `creationDate` 是代表*epoch 時間*的數位，或1970（以秒為單位）之後經過的時間。

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

結果如下：

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

此外，您可以依多個屬性來排序。 依多個屬性排序的查詢需要[複合索引](index-policy.md#composite-indexes)。 請考慮以下查詢：

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

此查詢會依城市名稱的遞增順序來抓取家族 `id`。 如果多個專案具有相同的城市名稱，則查詢會依 `creationDate` 的遞減順序排序。

## <a name="documents-with-missing-fields"></a>含有遺漏欄位的檔

針對具有預設索引編制原則之容器執行 `ORDER BY` 的查詢，將不會傳回未定義 sort 屬性的檔。 如果您想要包含未定義 sort 屬性的檔，您應該在索引編制原則中明確包含這個屬性。

例如，以下是具有索引編制原則的容器，其中未明確包含除了 `"/*"`以外的任何路徑：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

如果您在 `Order By` 子句中執行包含 `lastName` 的查詢，則結果只會包含已定義 `lastName` 屬性的檔。 我們尚未定義 `lastName` 的明確包含路徑，因此沒有任何 `lastName` 的任何檔都不會出現在查詢結果中。

以下查詢會依 `lastName` 在兩個檔上排序，其中一個不會定義 `lastName`：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

結果只會包含已定義 `lastName`的檔：

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

如果我們更新容器的編制索引原則，明確包含 `lastName`的路徑，我們將會在查詢結果中包含未定義排序屬性的檔。 您必須明確定義路徑，使其成為此純量值（而不是超過它）。 您應該在索引編制原則的路徑定義中使用 `?` 字元，以確保明確地為屬性編制索引 `lastName` 而且不會有任何其他的嵌套路徑。

以下是索引編制原則的範例，可讓您讓具有未定義 `lastName` 的檔出現在查詢結果中：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

如果您再次執行相同的查詢，`lastName` 遺漏的檔會先出現在查詢結果中：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

結果如下：

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

如果您將排序次序修改為 `DESC`，遺漏的檔 `lastName` 會出現在查詢結果的最後：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

結果如下：

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>後續步驟

- [快速入門](sql-query-getting-started.md)
- [Azure Cosmos DB 中的索引編製原則](index-policy.md)
- [OFFSET LIMIT 子句](sql-query-offset-limit.md)
