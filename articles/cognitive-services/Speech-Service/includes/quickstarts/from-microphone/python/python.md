---
title: 快速入門：從麥克風辨識語音，Python - 語音服務
titleSuffix: Azure Cognitive Services
description: 您可以使用本指南來建立語音轉換文字的主控台應用程式 (其會使用適用於 Python 的語音 SDK)。 完成之後，您可以使用電腦的麥克風將語音即時轉譯為文字。
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 086a77a9d7096870b8d381d301bfc0f31ab390f9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468125"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定開發環境](../../../../quickstarts/setup-platform.md)
> * [建立空的範例專案](../../../../quickstarts/create-project.md)
> * 確定您可以存取麥克風以擷取音訊

## <a name="support-and-updates"></a>支援及更新

語音 SDK Python 套件的更新會透過 PyPI 散發，並在[版本資訊](~/articles/cognitive-services/Speech-Service/releasenotes.md)上宣佈。
如果有新版本可用，您可以使用 `pip install --upgrade azure-cognitiveservices-speech` 命令來更新至該版本。
請檢查 `azure.cognitiveservices.speech.__version__` 變數來確認目前所安裝的版本。

如果您有問題或缺少功能，請參閱[支援和協助選項](~/articles/cognitive-services/Speech-Service/support.md)。

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>建立使用語音 SDK 的 Python 應用程式

### <a name="run-the-sample"></a>執行範例

您可以將本快速入門中的[程式碼範例](#sample-code)複製到原始程式檔 `quickstart.py`，並在您的 IDE 或主控台中執行它：

```sh
python quickstart.py
```

或者，也可以從[語音 SDK 範例存放庫](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) \(英文\) 將本快速入門教學課程下載為 [Jupyter](https://jupyter.org) \(英文\) 筆記本，並以筆記本的形式執行它。

### <a name="sample-code"></a>範例程式碼

> [!NOTE]
> 語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>透過 Visual Studio Code 安裝及使用語音 SDK

1. 在您的電腦上下載並安裝 64 位元版本的 [Python](https://www.python.org/downloads/) \(英文\) (3.5 或更新版本)。
1. 下載並安裝 [Visual Studio Code](https://code.visualstudio.com/Download)。
1. 開啟 Visual Studio Code，然後安裝 Python 擴充功能。 從功能表選取 [檔案]   > [喜好設定]   > [擴充功能]  。 搜尋 **Python**。

   ![安裝 Python 擴充功能](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. 建立資料夾來儲存專案。 例如，使用 Windows 檔案總管。
1. 在 Visual Studio Code 中選取 [檔案]  圖示。 然後開啟您所建立的資料夾。

   ![開啟資料夾](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. 選取 [新增檔案] 圖示，以建立新的 Python 來源檔案 `speechsdk.py`。

   ![建立檔案](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. 將 [Python 程式碼](#sample-code)複製並貼上到新建立的檔案中，然後儲存它。
1. 插入您的語音服務訂用帳戶資訊。
1. 如果已經選取，Python 直譯器會顯示在視窗底部狀態列的左側。
   否則，會顯示可用 Python 直譯器的清單。 開啟命令選擇區 (Ctrl+Shift+P)，然後輸入 **Python:Select Interpreter**。 選擇適當的解譯器。
1. 您可以從 Visual Studio Code 內安裝語音 SDK Python 套件。 如果您選取的 Python 直譯器尚未安裝該套件，請按照以下步驟安裝。
   若要安裝語音 SDK 套件，請開啟終端機。 再次開啟命令選擇區 (Ctrl+Shift+P)，然後輸入 **Terminal:Create New Integrated Terminal** 來開啟終端機。
   在開啟的終端機中，輸入命令 `python -m pip install azure-cognitiveservices-speech` 或系統所適用的命令。
1. 若要執行範例程式碼，請在編輯器內任意地方按一下滑鼠右鍵。 選取 [在終端機中執行 Python 檔案]  。
   出現提示時，請說出幾個字。 不久之後便會顯示轉譯的文字。

   ![執行範例](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

如果您在遵循這些指示時遇到問題，請參閱更加詳盡的 [Visual Studio Code Python 教學課程](https://code.visualstudio.com/docs/python/python-tutorial) \(英文\)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
