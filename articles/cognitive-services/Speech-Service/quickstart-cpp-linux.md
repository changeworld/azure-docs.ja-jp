---
title: C++ と Linux 向け Speech SDK のクイック スタート | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Linux と C++ の環境での Cognitive Services の Speech SDK の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: cee70ba585f93dda3249fc5b39f25fb613b57a45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753602"
---
# <a name="quickstart-for-c-and-linux"></a>C++ と Linux 向けのクイック スタート

Cognitive Services Speech SDK の現在のバージョンは `0.4.0` です。

Linux 向け Cognitive Services Speech SDK は、64 ビットおよび 32 ビット アプリケーションの構築に利用できます。 必要なファイルは、https://aka.ms/csspeech/linuxbinary から tar ファイルとしてダウンロードできます。

> [!NOTE]
> C++ と Windows 向けクイック スタートをお探しの場合は、[こちら](quickstart-cpp-windows.md)にお進みください。
> C# と Windows 向けのクイック スタートをお探しの場合は、[こちら](quickstart-csharp-windows.md)にお進みください。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 以降の手順では、PC (x86 または x64) で Ubuntu 16.04 が実行されていることを前提としています。
> 異なる Ubuntu バージョン、または異なる Linux ディストリビューションでは、環境に合わせて必要な手順に調整を加える必要があります。

## <a name="prerequisites"></a>前提条件

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>バイナリ パッケージの入手

[!include[License Notice](includes/license-notice.md)]

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

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>サンプル コード

次のコードは、マイクからの英語の音声を認識します。
このコードを `quickstart-linux.cpp` という名前のファイルに配置します。

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

コード内のサブスクリプション キーを、取得したサブスクリプション キーで置き換えます。

## <a name="building"></a>ビルド

> [!NOTE]
> 次のビルド コマンドをコピーし、"_単一行_" として貼り付けます。

* x64 マシン上で次のコマンドを実行して、アプリケーションをビルドします。

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* x86 マシン上で次のコマンドを実行して、アプリケーションをビルドします。

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>実行中

アプリケーションを実行するには、Speech SDK ライブラリを指すようにローダーのライブラリ パスを構成する必要があります。

* x64 マシン上で、次のコードを実行します。

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* x86 マシン上で、次のコードを実行します。

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

次のようにアプリケーションを実行します。

```sh
./quickstart-linux
```

正常に処理が完了すると、次のような出力が表示されます。

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>サンプルのダウンロード

最新のサンプルのセットについては、[Cognitive Services の Speech SDK のサンプルの GitHub リポジトリ](https://aka.ms/csspeech/samples)を参照してください。

## <a name="next-steps"></a>次の手順

* 追加のサンプルについては、[サンプルのページ](samples.md)を参照してください。
