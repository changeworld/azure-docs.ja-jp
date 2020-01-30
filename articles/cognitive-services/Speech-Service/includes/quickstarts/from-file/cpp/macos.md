---
title: クイック スタート:オーディオ ファイルから音声を認識する、C++ (macOS) - Speech サービス
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: erhopf
ms.openlocfilehash: 92586667675823343eb1f44f054e939c59719379
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748881"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=macos)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. `helloworld.cpp` という名前で C++ ソース ファイルを作成し、その中に次のコードを貼り付けます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. この新しいファイルで、文字列 `YourSubscriptionKey` を、音声サービスのサブスクリプション キーで置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](~/articles/cognitive-services/Speech-Service/regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. 文字列 `whatstheweatherlike.wav` を実際のファイル名に置き換えます。

> [!NOTE]
> Speech SDK では、既定で認識される言語が en-us です。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../../how-to-specify-source-language.md)」を参照してください。

## <a name="build-the-app"></a>アプリのビルド

> [!NOTE]
> 次のコマンドを必ず _1 つのコマンド ライン_ として入力してください。 各コマンドの横にある **[Copy]\(コピー)** ボタンを使用してコマンドをコピーし、シェル プロンプトでそれを貼り付けるのが、それを行う最も簡単な方法です。

* 次のコマンドを実行して、アプリケーションをビルドします。

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>アプリを実行する

1. Speech SDK ライブラリを指すようにローダーのライブラリ パスを構成します。

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. アプリケーションを実行します。

   ```sh
   ./helloworld
   ```

1. オーディオ ファイルが Speech サービスに送信され、ファイルの最初の発話がテキストに文字起こしされて、同じウィンドウに表示されます。

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]
