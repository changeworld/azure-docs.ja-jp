---
title: クイック スタート:Speech SDK C++ (Linux) プラットフォームの設定 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech サービス SDK を使用して、Linux 上で C++ のプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 03aafb133f1d4a18ffe2b46ae5ec36225865a239
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819039"
---
このガイドでは、Linux 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>システム要件

Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9)

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、次のものが必要です。

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* サポートされている Linux プラットフォームでは、特定のライブラリがインストールされている必要があります (Secure Sockets Layer サポート用に `libssl`、サウンド サポート用に `libasound2`)。 これらのライブラリの正しいバージョンをインストールするために必要なコマンドについては、下記のディストリビューションを参照してください。

   * Ubuntu の場合:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Debian 9 の場合:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

## <a name="install-speech-sdk"></a>Speech SDK をインストールする

Linux 用 Speech SDK は、64 ビット アプリケーションと 32 ビット アプリケーションのどちらの構築にも使用できます。 必要なライブラリとヘッダー ファイルは、 https://aka.ms/csspeech/linuxbinary から tar ファイルとしてダウンロードできます。

SDK を次のようにダウンロードしてインストールします。

1. Speech SDK のファイル抽出先にする必要があるディレクトリを選択し、そのディレクトリを指すように `SPEECHSDK_ROOT` 環境変数を設定します。 この変数によって、後のコマンドでこのディレクトリを参照することが容易になります。 たとえば、ホーム ディレクトリで `speechsdk` ディレクトリを使用する場合、次のようなコマンドを使用します。

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. このディレクトリがまだ存在しない場合は作成します。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Speech SDK バイナリを含む `.tar.gz` アーカイブをダウンロードして抽出します。

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. 展開されたパッケージの最上位ディレクトリの内容を検証します。

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   ディレクトリの一覧には、サード パーティの通知やライセンスのファイルと、ヘッダー (`.h`) ファイルを格納している `include` ディレクトリおよびライブラリを格納している `lib` ディレクトリが含まれているはずです。

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

これで[次の手順](#next-steps)に進むことができます。

## <a name="next-steps"></a>次の手順

[!INCLUDE [windows](../quickstart-list.md)]
