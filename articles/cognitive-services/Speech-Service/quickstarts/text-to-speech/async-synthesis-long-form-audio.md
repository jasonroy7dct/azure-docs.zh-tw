---
title: 快速入門：適用于長格式音訊的非同步合成（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 使用長音訊 API 以非同步方式將文字轉換成語音，並從服務所提供的 URI 中取出音訊輸出。 此 REST API 適用于需要將大於10000個字元或50個段落的文字檔轉換成合成語音的內容提供者。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: eef9a99e4c94fa45e21abfc9d19fcef1230ffe76
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75944684"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>快速入門：在 Python 中針對長格式音訊進行非同步合成（預覽）

在本快速入門中，您將使用長音訊 API 以非同步方式將文字轉換成語音，並從服務所提供的 URI 中取出音訊輸出。 此 REST API 適用于需要從超過5000個字元的文字合成音訊的內容提供者（或長度超過10分鐘）。 如需詳細資訊，請參閱[長音訊 API](../../long-audio-api.md)。

> [!NOTE]
> 適用于長格式音訊的非同步合成僅能與[自訂類神經語音](../../how-to-custom-voice.md#custom-neural-voices)搭配使用。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* Python 2.7. x 或3.x。
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)]、[ [Visual Studio Code](https://code.visualstudio.com/download) 或您慣用的文字編輯器。
* Azure 訂用帳戶和語音服務訂用帳戶金鑰。 [建立 Azure 帳戶](../../get-started.md#try-the-speech-service-using-a-new-azure-account)，並[建立語音資源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)以取得金鑰。 建立語音資源時，請確定您的定價層已設定為**S0**，且 location 已設定為支援的[區域](../../regions.md#standard-and-neural-voices)。

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

在您慣用的 IDE 或編輯器建立新的 Python 專案。 然後將此程式碼片段複製到名為 `voice_synthesis_client.py`的檔案中。

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> 如果您尚未使用這些模組，您必須先安裝它們，再執行程式。 若要安裝這些套件，請執行：`pip install requests urllib3`。

這些模組是用來剖析引數、建立 HTTP 要求，以及呼叫文字轉換語音的長音訊 REST API。

## <a name="get-a-list-of-supported-voices"></a>取得支援的語音清單

這段程式碼會取得可用來將文字轉換成語音的語音清單。 將程式碼新增至 `voice_synthesis_client.py`：

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>測試您的程式碼

讓我們來測試到目前為止所做的工作。 您必須更新下列要求中的一些事項：

* 以您的語音服務訂用帳戶金鑰取代 `<your_key>`。 您可以在[Azure 入口網站](https://aka.ms/azureportal)資源的 [**總覽**] 索引標籤中取得這項資訊。
* 將 `<region>` 取代為您的語音資源建立所在的區域（例如： `eastus` 或 `westus`）。 您可以在[Azure 入口網站](https://aka.ms/azureportal)資源的 [**總覽**] 索引標籤中取得這項資訊。

請執行這個命令：

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

您會看到如下所示的輸出：

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>準備輸入檔

準備輸入文字檔。 它可以是純文字或 SSML 文字。 如需輸入檔的需求，請參閱如何[準備內容以進行合成](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis)。

## <a name="convert-text-to-speech"></a>將文字轉換成語音

準備輸入文字檔之後，請將下列語音合成程式碼加入 `voice_synthesis_client.py`：

> [!NOTE]
> ' concatenateResult ' 是選擇性參數。 如果未設定此參數，則會針對每個段落產生音訊輸出。 您也可以藉由設定參數，將音訊串連成1個輸出。 根據預設，音訊輸出會設定為 riff-riff-16khz-16bit-mono-pcm-dxil 16 位-mono-pcm。 如需有關支援的音訊輸出的詳細資訊，請參閱[音訊輸出格式](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)。

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>測試您的程式碼

讓我們使用您的輸入檔做為來源，來提出合成文字的要求。 您必須更新下列要求中的一些事項：

* 以您的語音服務訂用帳戶金鑰取代 `<your_key>`。 您可以在[Azure 入口網站](https://aka.ms/azureportal)資源的 [**總覽**] 索引標籤中取得這項資訊。
* 將 `<region>` 取代為您的語音資源建立所在的區域（例如： `eastus` 或 `westus`）。 您可以在[Azure 入口網站](https://aka.ms/azureportal)資源的 [**總覽**] 索引標籤中取得這項資訊。
* 將 `<input>` 取代為您為文字轉換語音而準備的文字檔路徑。
* 以所需的輸出地區設定取代 `<locale>`。 如需詳細資訊，請參閱[語言支援](../../language-support.md#neural-voices)。
* 以所需的輸出語音取代 `<voice_guid>`。 使用其中一個所傳回的語音，[取得支援的語音清單](#get-a-list-of-supported-voices)。

使用下列命令將文字轉換為語音：

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> 如果您有1個以上的輸入檔案，就必須提交多個要求。 有一些需要注意的限制。 
> * 針對每個 Azure 訂用帳戶，允許用戶端每秒提交最多**5**個對伺服器的要求。 如果超過限制，用戶端會收到429錯誤碼（太多要求）。 請減少每秒的要求數量
> * 允許伺服器執行，並將每個 Azure 訂用帳戶的**120**要求排入佇列。 如果超過限制，伺服器會傳回429錯誤碼（太多要求）。 請等候並避免提交新的要求，直到部分要求完成

您會看到如下所示的輸出：

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

結果包含由服務產生的輸入文字和音訊輸出檔案。 您可以用 zip 格式下載這些檔案。

## <a name="remove-previous-requests"></a>移除先前的要求

伺服器會針對每個 Azure 訂用帳戶保留最多**20000**個要求。 如果您的要求數量超出此限制，請先移除先前的要求，再建立新的要求。 如果您未移除現有的要求，您會收到錯誤通知。

將程式碼新增至 `voice_synthesis_client.py`：

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>測試您的程式碼

現在，讓我們檢查以查看您先前提交的要求。 在繼續之前，您必須先更新此要求中的一些事項：

* 以您的語音服務訂用帳戶金鑰取代 `<your_key>`。 您可以在[Azure 入口網站](https://aka.ms/azureportal)資源的 [**總覽**] 索引標籤中取得這項資訊。
* 將 `<region>` 取代為您的語音資源建立所在的區域（例如： `eastus` 或 `westus`）。 您可以在[Azure 入口網站](https://aka.ms/azureportal)資源的 [**總覽**] 索引標籤中取得這項資訊。

請執行這個命令：

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

這會傳回您所做的合成要求清單。 您會看到如下所示的輸出：

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

現在，讓我們移除先前提交的要求。 您必須更新下列程式碼中的幾個專案：

* 以您的語音服務訂用帳戶金鑰取代 `<your_key>`。 您可以在[Azure 入口網站](https://aka.ms/azureportal)資源的 [**總覽**] 索引標籤中取得這項資訊。
* 將 `<region>` 取代為您的語音資源建立所在的區域（例如： `eastus` 或 `westus`）。 您可以在[Azure 入口網站](https://aka.ms/azureportal)資源的 [**總覽**] 索引標籤中取得這項資訊。
* 以前一個要求中傳回的值取代 `<synthesis_id>`。

> [!NOTE]
> 狀態為「執行中」/「正在等候」的要求無法移除或刪除。

請執行這個命令：

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

您會看到如下所示的輸出：

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>取得完整的用戶端

您可以從[GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)下載完整的 `voice_synthesis_client.py`。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入瞭解較長的音訊 API](../../long-audio-api.md)
