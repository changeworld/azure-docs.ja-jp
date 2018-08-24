---
title: 'クイック スタート: Cognitive Services Speech SDK を使用して Linux 上で C++ で音声を認識する'
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services Speech SDK を使用して Linux 上で C++ で音声を認識する方法について説明します。
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: b8bc24123fe75e876f607b07e37423ae68d92ee4
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "41929943"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>クイック スタート: Speech SDK を使用して Linux 上で C++ で音声を認識する

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Cognitive Services Speech SDK を使用して Linux (Ubuntu 16.04) 上で C++ コンソール アプリケーションを作成することによって音声をテキストに変換する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Speech サービスのサブスクリプション キー。 [Speech サービスを無料で試す](get-started.md)ための記事を参照してください。
* 機能するマイクを備えた Ubuntu 16.04 PC。
* このサンプルの構築および実行に必要なパッケージをインストールするには、次を実行します。

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>Speech SDK を取得する

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK の現在のバージョンは `0.6.0` です。

Linux 向け Cognitive Services Speech SDK は、64 ビットおよび 32 ビット アプリケーションの構築に利用できます。
必要なファイルは、 https://aka.ms/csspeech/linuxbinary から tar ファイルとしてダウンロードできます。
SDK を次のようにダウンロードしてインストールします。

1. Speech SDK のバイナリとヘッダーを配置するディレクトリ (絶対パス) を選択します。
   たとえば、ホーム ディレクトリの下のパス `speechsdk` を選択します。

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. ディレクトリが存在しない場合はこれを作成します。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Speech SDK バイナリが含まれた `.tar.gz` アーカイブをダウンロードして展開します。

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. 展開されたパッケージの最上位ディレクトリの内容を検証します。

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   サードパーティの通知ファイル、ライセンス ファイル、ヘッダー用の `include` ディレクトリ、およびライブラリ用の `lib` ディレクトリが表示されます。

   [!include[Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. `helloworld.cpp` という名前のファイルに次のコードを追加します。

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

## <a name="building"></a>ビルド

> [!NOTE]
> 次のビルド コマンドをコピーし、"_単一行_" として貼り付けます。

* **x64** コンピューター上では、次のコマンドを実行してアプリケーションをビルドします。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* **x86** コンピューター上では、次のコマンドを実行してアプリケーションをビルドします。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>サンプルを実行する

1. Speech SDK ライブラリを指すようにローダーのライブラリ パスを構成します。

   * **x64** コンピューター上では、次を実行します。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * **x86** コンピューター上では、次を実行します。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. 次のようにアプリケーションを実行します。

   ```sh
   ./helloworld
   ```

1. 次のような出力が表示されます。

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/cpp-linux` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

* [サンプルを入手する](speech-sdk.md#get-the-samples)
