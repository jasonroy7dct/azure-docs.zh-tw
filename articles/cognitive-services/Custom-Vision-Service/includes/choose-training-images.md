---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70130068"
---
建議在初始定型集的每個標記使用至少 30 個影像。 您也會想要收集一些額外的影像，來測試已定型的模型。

若要有效地定型您的模型，可使用有不同視覺效果的影像。 選取有下列各種變化的影像：
* 攝影機角度
* 光源
* 背景資訊
* 視覺效果樣式
* 單一/群組對象
* size
* type

此外，請確定所有的訓練映像符合下列準則：
* .jpg、.png、.bmp 或 .gif 格式
* 不大於 6MB (預測影像為 4MB)
* 最短邊緣不小於 256 像素；任何超過此上限的影像會由自訂視覺服務自動相應增加
