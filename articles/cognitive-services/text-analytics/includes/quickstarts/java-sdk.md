---
title: 快速入門：適用於 Java 的文字分析 v3 用戶端程式庫 | Microsoft Docs
description: 開始使用適用於 Java 的 v3 文字分析用戶端程式庫。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/11/2020
ms.author: aahi
ms.reviewer: tasharm, assafi
ms.openlocfilehash: eeaef4a9970609c43c03cd784436796a6f8af174
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211447"
---
<a name="HOLTop"></a>

[參考文件](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [套件 (Maven)](https://oss.sonatype.org/#nexus-search;quick~com.azure) | [範例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* [Java 開發套件](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) 含第 8 版或更新版本


[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>設定

### <a name="create-a-new-maven-project"></a>建立新的 Maven 專案

將下列文字分析相依性新增至您的專案。 此版本的相依性會使用文字分析 API 的 `3.0-preview` 版。 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
</dependencies>
```

在下列目錄中建立新的 Java 檔案：`\src\main\java`。

開啟 Java 檔案，並新增下列 `import` 陳述式：

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

在 Java 檔案中，新增新的類別，並新增 Azure 資源的金鑰和端點，如下所示。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

將下列 main 方法新增至類別。 稍後，您將會定義此處所呼叫的方法。

```java
public static void main(String[] args) {

    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>物件模型

文字分析用戶端是 `TextAnalyticsClient` 物件，會使用您的金鑰向 Azure 進行驗證，並提供可接受文字作為單一字串或批次的函式。 您可以透過同步或非同步方式將文字傳送至 API。 回應物件會包含每個傳送文件的分析資訊。 

## <a name="code-examples"></a>程式碼範例

* [驗證用戶端](#authenticate-the-client)
* [情感分析](#sentiment-analysis) 
* [語言偵測](#language-detection)
* [具名實體辨識](#named-entity-recognition-ner) 
* [實體連結](#entity-linking)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>驗證用戶端

建立可使用上方所建立 `KEY` 和 `ENDPOINT` 來具現化 `TextAnalyticsClient` 物件的方法。

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

在程式的 `main()` 方法中，呼叫驗證方法來具現化用戶端。

## <a name="sentiment-analysis"></a>情感分析

建立名為 `sentimentAnalysisExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `analyzeSentiment()` 函式。 如果成功，傳回的 `AnalyzeSentimentResult` 物件會包含 `documentSentiment` 和 `sentenceSentiments`，如果未能成功，則會包含 `errorMessage`。 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
        System.out.printf(
            "Recognized document sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
            documentSentiment.getSentiment(),
            documentSentiment.getSentimentScores().getPositive(),
            documentSentiment.getSentimentScores().getNeutral(),
            documentSentiment.getSentimentScores().getNegative());

        for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
            System.out.printf(
                "Recognized sentence sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
                sentenceSentiment.getSentiment(),
                sentenceSentiment.getSentimentScores().getPositive(),
                sentenceSentiment.getSentimentScores().getNeutral(),
                sentenceSentiment.getSentimentScores().getNegative());
        }
}
```

### <a name="output"></a>輸出

```console
Recognized document sentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized sentence sentiment: positive, positive score: 1.00, neutral score: 0.00, negative score: 0.00.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```
## <a name="language-detection"></a>語言偵測

建立名為 `detectLanguageExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `detectLanguage()` 函式。 如果成功，傳回的 `DetectLanguageResult` 物件會包含所偵測到的主要語言和所偵測到其他語言的清單，如果未能成功，則會包含 `errorMessage`。

> [!Tip]
> 在某些情況下，可能難以根據輸入來區分語言。 您可以使用 `countryHint` 參數來指定 2 個字母的國碼。 根據預設，API 會使用 "US" 作為預設 countryHint，若要移除此行為，您可以將此值設定為空字串 `countryHint = ""`，以重設此參數。 若要設定不同的預設值，請設定 `TextAnalyticsClientOptions.DefaultCountryHint` 屬性，並在用戶端的初始化期間傳遞此屬性。

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getScore());
}
```

### <a name="output"></a>輸出

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

> [!NOTE]
> 在 `3.0-preview` 版中：
> * NER 包含各種用於偵測個人資訊的方法。 
> * 實體連結是不同於 NER 的要求。

建立名為 `recognizeEntitiesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `recognizeEntities()` 函式。 如果成功，傳回的 `RecognizeEntitiesResult` 物件會包含 `NamedEntity` 的清單，如果未能成功，則會包含 `errorMessage`。

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>輸出

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, offset: 26, length: 7, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, offset: 34, length: 9, score: 0.80.
```

## <a name="using-ner-to-recognize-personal-information"></a>使用 NER 辨識個人資訊

建立名為 `recognizePIIEntitiesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `recognizePiiEntities()` 函式。 如果成功，傳回的 `RecognizePiiEntitiesResult` 物件會包含 `NamedEntity` 的清單，如果未能成功，則會包含 `errorMessage`。 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>輸出

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), entity sub-category: N/A, offset: 33, length: 11, score: 0.85.
```

## <a name="entity-linking"></a>實體連結

建立名為 `recognizeLinkedEntitiesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `recognizeLinkedEntities()` 函式。 如果成功，傳回的 `RecognizeLinkedEntitiesResult` 物件會包含 `LinkedEntity` 的清單，如果未能成功，則會包含 `errorMessage`。 因為連結的實體已識別為是唯一的，所以相同實體再次出現的話，就會群組到 `LinkedEntity` 物件底下作為 `LinkedEntityMatch` 物件的清單。

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
                linkedEntity.getName(),
                linkedEntity.getId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Offset: %s, Length: %s, Score: %.2f.%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getOffset(),
                    linkedEntityMatch.getLength(),
                    linkedEntityMatch.getScore());
        }
    }
}
```

### <a name="output"></a>輸出

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Offset: 11, Length: 116, Score: 0.78.
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Offset: 10, Length: 25, Score: 0.55.
Text: Gates, Offset: 5, Length: 161, Score: 0.55.
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Offset: 10, Length: 40, Score: 0.53.
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Offset: 9, Length: 0, Score: 0.47.
Text: Microsoft, Offset: 9, Length: 150, Score: 0.47.
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Offset: 7, Length: 54, Score: 0.25.
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Offset: 5, Length: 89, Score: 0.28.
```
## <a name="key-phrase-extraction"></a>關鍵片語擷取

建立名為 `extractKeyPhrasesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `extractKeyPhrases()` 函式。 如果成功，傳回的 `ExtractKeyPhraseResult` 物件會包含關鍵片語的清單，如果未能成功，則會包含 `errorMessage`。

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>輸出

```console
Recognized phrases: 
cat
veterinarian
```
