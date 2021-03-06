---
title: 使用和顯示需求 - 專案答案搜尋
titlesuffix: Azure Cognitive Services
description: 專案答案搜尋端點的使用及顯示需求。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 2b42d61fd887f166a08b78510d5eaacb8a7cdcb8
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706711"
---
# <a name="project-answer-search-use-and-display-requirements"></a>專案答案搜尋使用和顯示需求

使用和顯示需求適用於您可透過呼叫 Bing 知識搜尋、Bing 自訂搜尋、實體搜尋、影像搜尋、新聞搜尋、視訊搜尋、視覺效果搜尋、Web 搜尋、拼字檢查和自動建議 API 所提供的任何內容實作和關聯資訊 (例如，關聯性、中繼資料和其他訊號)。 特定功能和結果的文件中可以找到與這些需求相關的實作詳細資料。

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1.Bing 拼字檢查和 Bing 自動建議 API。

請勿：

- 複製、儲存或快取您從 Bing 拼字檢查或 Bing 自動建議 API 收到的任何資料
- 使用來自 Bing 拼字檢查或 Bing 自動建議 API 的資料作為任何機器學習或類似演算法活動的一部分，可訓練、評估或改進您或第三方可能會提供之新的或現有服務。

## <a name="2-definitions"></a>2.定義

- 「解答」是指的回應中傳回的結果類別。 例如，來自 Bing Web 搜尋 API 的回應可能包含網頁結果、映像、影片和新聞類別中的解答；
- 「回應」表示對搜尋服務 API 的單一呼叫回應中所收到的所有及任何解答和相關聯的資料；
- 「結果」是指解答中資訊的項目。 例如，與單一新聞文章連線的資料集是新聞回應中的結果。
- 「搜尋 API」統稱 Bing 自訂搜尋、實體搜尋、影像搜尋、新聞搜尋、影片搜尋、圖像式搜尋及網路搜尋 API。 


## <a name="3-search-apis"></a>3.搜尋 API

這個第 3 節中的需求會套用至搜尋 API。

**A.網際網路搜尋體驗。** 回應中傳回的所有資料可能僅用於網際網路搜尋體驗。 網際網路搜尋體驗表示所顯示的內容 (如適用)︰ 
- 相關且可回應終端使用者的直接查詢或其他使用者的搜尋興趣與意圖指示 (例如，使用者指示的搜尋查詢)； 
- 協助使用者尋找並瀏覽到資料的來源 (例如，提供的 URL 是實作為超連結，因此內容或屬性是可點選的連結，而且其中顯示資料)；或者，如果是 Bing 實體搜尋 API，則連結到回應中提供的 bing.com URL，這可讓使用者對於 bing.com 上的相關查詢瀏覽到搜尋結果；
- 包含多個結果可供使用者從中選取 (例如，會顯示來自新聞回應的多個解答，或如果所傳回的少於多個，則會顯示所有的結果)； 
- 僅限於適用於提供搜尋用途的數量 (例如，影像縮圖會將縮圖大小調整為使用者的顯示比例)； 
- 包含終端使用者的可見指示，內容為網際網路搜尋結果 (例如，內容是「來自網路」的陳述)，以及
- 包含任何其他的度量組合，這些度量可確保您在使用從搜尋 API 收到的資料時，不致違反任何相關法律或第三方權利 (例如，如果使用 Creative Commons 授權，則遵守適用的授權條款)。 請諮詢法律顧問以決定可能適合的度量。
網際網路搜尋體驗需求的唯一例外狀況是針對 URL 探索，如下面第 3E 節所述 (非顯示 URL 探索)。 

**B.限制。** 請勿：

- 從回應複製、儲存或快取任何資料 (惟保留下面＜服務持續性＞一節所允許之範圍內除外)； 
- 使用來自搜尋 API 的資料作為任何機器學習或類似演算法活動的一部分，可訓練、評估或改進您或第三方可能會提供之新的或現有服務。
- 除非法律要求或者 Microsoft 同意，而修改結果的內容 (只能以不違反任何其他需求的方式將其重新格式化)； 
- 略過與結果內容相關聯的屬性和 URL；
- 除非法律規定或 Microsoft 同意，否則提供訂單或順位時會重新排列 (包括藉由省略) 答案中顯示的結果 (對於 Bing 自訂搜尋 API，此規則不適用於透過 customsearch.ai portal 進行的重新排列)；
- 以可能導致使用者相信其他內容包含在回應中的方式顯示回應任何部分內的其他內容； 
- 在顯示任何回應部分的任何頁面上顯示並非由 Microsoft 提供的廣告；(i) 來自 Bing 影像、新聞或影片搜尋 API；或 (ii) 主要 (或完全) 篩選或限制於影像、新聞和/或影片結果。

**C.注意事項和商標。** 

- 在提供使用者輸入搜尋查詢能力的各個使用者體驗 (UX) 點附近，以醒目方式包含 Microsoft 隱私權聲明 (如 https://go.microsoft.com/fwlink/?LinkId=521839 所示) 的功能性超連結。 標示超連結「Microsoft 隱私權聲明」。
- 在提供使用者輸入搜尋查詢能力的各個 UX 點附近，以醒目方式顯示 Bing 商標，與 https://go.microsoft.com/fwlink/?linkid=833278 提供的指導方針一致。  該商標必須清楚向使用者表示是由 Microsoft 提供網際網路搜尋體驗。
- 除非 Microsoft 另行書面指定使用方式，否則您可將從 Bing Web、影像、新聞及視訊 API 顯示的每個回應內容 (或回應內容的一部分) 視為 Microsoft 提供的回應，如 https://go.microsoft.com/fwlink/?linkid=833278 所述。 
- 除非 Microsoft 另行書面指定特定使用方式，否則請勿將從 Bing 自訂搜尋 API 顯示的回應 (或回應的一部分) 視為 Microsoft 提供的回應。


**D.傳輸回應。** 如果您讓使用者將回應從搜尋 API 傳輸給另一位使用者，例如透過傳訊應用程式或社交媒體貼文，則適用下列步驟︰ 
- 傳輸的回應必須︰
  - 從對傳輸之使用者顯示的回應內容未經修改的內容所組成 (允許格式變更)；
  - 在中繼資料表單中未包含任何資料；
  - 對於 Bing Web、影像、新聞及視訊 API 的回應，指出回應是透過由 Bing 所提供之網際網路搜尋體驗取得的顯示語言 (例如，「由 Bing 所提供」、「在 Bing 上進一步了解此影像」或使用 Bing 標誌)；
  - 針對來自 Bing 自訂搜尋 API 的回應顯示語言，指出回應是透過網際網路搜尋體驗取得 (例如，「深入瞭解此搜尋結果」)；
  - 以醒目方式顯示用來產生回應的完整查詢；和
  - 在回應的基礎來源包括醒目連結或類似的屬性，可能是直接或透過搜尋引擎 (bing.com、m.bing.com，或者是適用的自訂搜尋服務)。
- 您不可以自動化回應的傳輸。 傳輸必須由使用者動作啟動，清楚地證明傳輸回應之意圖。
- 您只可以讓使用者傳輸為了回應傳輸使用者查詢所顯示的回應。

**E.服務的持續性。** 請勿複製、儲存或快取搜尋 API 回應中的任何資料。 不過，若要啟用服務存取持續性和資料轉譯，您僅在下列情況下才可以保留結果︰

**裝置。** 您可讓使用者在裝置上保留結果，惟必須低於 (i) 查詢時間起的 24 小時，或 (ii) 直到使用者提交更新結果的另一個查詢，前提是保留的結果只可用於︰

- 讓終端使用者存取先前在該裝置上傳回給該使用者的結果 (例如，發生服務中斷)；或
- 根據該終端使用者的訊號，在預期到終端使用者需求而個人化的情況下，儲存主動式查詢所傳回的結果 (例如，發生預期的服務中斷時)。

**伺服器。** 您僅在下列情況才可以在您控制的伺服器上安全地保留單一使用者特定的結果和顯示保留的結果︰

- 讓使用者存取先前在您的解決方案中傳回給該使用者的結果歷程記錄報告，前提是結果可能不會 (i) 從使用者的初始查詢時間起保留超過 21 天，(ii) 顯示以回應使用者的新的或重複查詢；或
- 根據該使用者的訊號，儲存預期使用者的需求所個人化之主動式查詢傳回的結果，惟必須低於 (i) 自查詢時間起的 24 小時或 (ii) 直到使用者提交更新結果的另一個查詢。

只要保留，特定使用者的結果便不能與另一位使用者合併，也就是必須保留並分別傳送每個使用者的結果。

**一般。** 對於所有保留結果的呈現︰

- 包含傳送查詢時間格式的清晰可見通知，
- 將按鈕或類似的方法呈現給使用者，以重新查詢並取得更新的結果， 
- 在結果的呈現方式中保留 Bing 商標，以及
- 刪除 (和視需要使用新的查詢重新整理) 指定時段內儲存的結果。

**F.非顯示 URL 探索。** 您只能就探索來自您的使用者或客戶查詢之回應資訊來源 URL 的目的，在非網際網路搜尋體驗中使用搜尋回應。 您可以在您所提供的報告或類似回應中複製這類 URL (i) 僅針對該使用者或客戶回應的查詢，以及 (ii) 其中包括大幅與查詢相關的其他寶貴的內容。 第 3A 節到第 3E 節中這些使用的需求和顯示需求不適用於此非顯示用途，除非︰ 

- 除上面所述受限制的 URL 複製外，不可快取、複製或儲存來自或衍生自搜尋回應的任何資料或內容；
- 請確保您所使用從搜尋 API 收到的資料 (包含 URL) 不違反任何相關法律或第三方權利；以及
- 您不得使用來自搜尋 API 的資料 (包含 URL) 作為任何搜尋索引或機器學習或類似演算法活動的一部分，可訓練、評估或改進您或第三方可能會提供的服務。

## <a name="next-steps"></a>後續步驟
[答案搜尋概觀](overview.md)
