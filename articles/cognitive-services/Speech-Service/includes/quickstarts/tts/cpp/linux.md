---
title: クイック スタート:音声を合成する、C++ (Linux) - Speech サービス
titleSuffix: Azure Cognitive Services
description: Linux で C++ と Speech SDK を使用して音声を合成する方法について説明します
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: fc5d0e0a7f693df87e754383a123dc1cebfdd94c
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818378"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=linux)

## <a name="add-sample-code"></a>サンプル コードを追加する

1. `helloworld.cpp` という名前で C++ ソース ファイルを作成し、その中に次のコードを貼り付けます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/text-to-speech/helloworld.cpp#code)]

1. この新しいファイルで、文字列 `YourSubscriptionKey` を、音声サービスのサブスクリプション キーで置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](~/articles/cognitive-services/Speech-Service/regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

## <a name="build-the-app"></a>アプリのビルド

> [!NOTE]
> 次のコマンドを必ず _1 つのコマンド ライン_ として入力してください。 各コマンドの横にある **[Copy]\(コピー)** ボタンを使用してコマンドをコピーし、シェル プロンプトでそれを貼り付けるのが、それを行う最も簡単な方法です。

* **x64** (64 ビット) システムでは、次のコマンドを実行してアプリケーションをビルドします。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* **x86** (32 ビット) システムでは、次のコマンドを実行してアプリケーションをビルドします。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* **ARM64** (64 ビット) システムでは、次のコマンドを実行してアプリケーションをビルドします。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>アプリの実行

1. Speech SDK ライブラリを指すようにローダーのライブラリ パスを構成します。

   * **x64** (64 ビット) システムでは、次のコマンドを入力します。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * **x86** (32 ビット) システムでは、このコマンドを入力します。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * **ARM64** (64 ビット) システムでは、次のコマンドを入力します。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. アプリケーションを実行します。

   ```sh
   ./helloworld
   ```

1. コンソール ウィンドウにプロンプトが表示されて、何かテキストを入力するよう求められます。 いくつかの単語または文を入力してください。 入力したテキストが Speech サービスに転送されて音声に合成され、スピーカーで再生されます。

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>次の手順

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>関連項目

- [カスタム音声を作成する](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [カスタム音声サンプルを録音する](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
