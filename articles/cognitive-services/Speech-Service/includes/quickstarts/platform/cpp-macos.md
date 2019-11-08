---
title: クイック スタート:Speech SDK C++ (macOS) プラットフォームのセットアップ - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Services SDK を使用して、macOS 上の C++ のプラットフォームをセットアップするには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 2c6a186eef631372f08b876083dacede31cf1077
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504469"
---
このガイドでは、macOS 10.13 以降に C++ 用の [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>システム要件

macOS 10.13 以降

## <a name="install-speech-sdk"></a>Speech SDK をインストールする

1. Speech SDK のファイル抽出先にする必要があるディレクトリを選択し、そのディレクトリを指すように `SPEECHSDK_ROOT` 環境変数を設定します。 この変数によって、後のコマンドでこのディレクトリを参照することが容易になります。 たとえば、ホーム ディレクトリで `speechsdk` ディレクトリを使用する場合、次のようなコマンドを使用します。

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. このディレクトリがまだ存在しない場合は作成します。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Speech SDK フレームワークを含む `.zip` アーカイブをダウンロードして抽出します。

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. 展開されたパッケージの最上位ディレクトリの内容を検証します。

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   ディレクトリの一覧には、サード パーティの通知やライセンスのファイルのほか、`MicrosoftCognitiveServicesSpeech.framework` ディレクトリが含まれているはずです。

これで、下記の「[次の手順](#next-steps)」に進むことができます。

## <a name="next-steps"></a>次の手順

[!INCLUDE [windows](../quickstart-list.md)]
