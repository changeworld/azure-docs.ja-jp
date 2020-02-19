---
title: クイック スタート:マイクから音声を認識する、C++ (macOS) - Speech サービス
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 85f2d671e53592fbe2a543fade43c0bc3e6f47e1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77155961"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=macos)
> * オーディオ キャプチャ用のマイクにアクセスできることを確認する

## <a name="add-sample-code"></a>サンプル コードを追加する

1. `helloworld.cpp` という名前で C++ ソース ファイルを作成し、その中に次のコードを貼り付けます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

1. この新しいファイルで、文字列 `YourSubscriptionKey` を、音声サービスのサブスクリプション キーで置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](https://aka.ms/speech/sdkregion)の "Speech SDK パラメーター" (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

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

1. コンソール ウィンドウに、何か発言するよう求めるプロンプトが表示されます。 英語の語句または文を読み上げます。 音声が Speech サービスに送信されてテキストに変換され、同じウィンドウに表示されます。

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]