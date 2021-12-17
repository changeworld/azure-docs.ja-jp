---
title: カスタム コマンド アプリをテストする
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンド アプリケーションをテストするためのさまざまな方法について説明します。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: fa9f9ec8d7a8f60d6c72cb6c4f669ef511cc0068
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112456879"
---
# <a name="test-your-custom-commands-application"></a>カスタム コマンド アプリケーションをテストする

この記事では、カスタム コマンド アプリケーションをテストするためのさまざまな方法について説明します。

## <a name="test-in-the-portal"></a>ポータルでのテスト

ポータルでのテストは、カスタム コマンド アプリケーションが期待どおりに動作するかどうかを確認する最もシンプルかつ迅速な方法です。 アプリが正常にトレーニングされたら、[`Test`] ボタンをクリックしてテストを開始します。

> [!div class="mx-imgBorder"]
> ![ポータルでのテスト](media/custom-commands/create-basic-test-chat-no-mic.png)

## <a name="test-with-windows-voice-assistant-client"></a>Windows 音声アシスタント クライアントを使用してテストする

Windows 音声アシスタント クライアントとは、C# で記述された Windows Presentation Foundation (WPF) アプリケーションです。これを使用すれば、カスタム クライアント アプリケーションを作成する前に、ご利用のボットとのやりとりを簡単にテストできます。

このツールでは、カスタム コマンド アプリケーション ID を受け入れることができます。 これにより、カスタム コマンド サービスでホストされているタスクの完了または指示と制御のシナリオをテストすることができます。

クライアントを設定するには、[Windows 音声アシスタント クライアント](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf)をチェックアウトします。

> [!div class="mx-imgBorder"]
> ![WVAC でのプロファイルの作成](media/custom-commands/conversation.png)

## <a name="test-programatically-with-the-cognitive-services-voice-assistant-test-tool"></a>Cognitive Services 音声アシスタント テスト ツールでプログラムによるテストを行う

音声アシスタント テスト ツールは、Microsoft Voice Assistant 向けの構成可能な .NET Core C# コンソール アプリケーションで、エンドツーエンドの機能回帰テストを行えます。 

このツールは、ボットでの回帰を防ぐことを目的として、コンソール コマンドとして手動で実行するか、Azure DevOps CI/CD パイプラインの一部として自動で実行することができます。

ツールを設定する方法については、「[音声アシスタント テスト ツール](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/clients/csharp-dotnet-core/voice-assistant-test)」を参照してください。

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Speech SDK 対応のクライアント アプリケーションを使用してテストする

Speech ソフトウェア開発キット (SDK) では Speech サービスのさまざまな機能が公開されています。これにより、音声認識対応アプリケーションを開発できます。 これは、ほとんどのプラットフォーム、多数のプログラミング言語で使用できます。

Speech SDK を使用してユニバーサル Windows プラットフォーム (UWP) クライアント アプリケーションを設定し、それをカスタム コマンド アプリケーションと統合するには、次のようにします。  
- [方法: Speech SDK を使用してクライアント アプリケーションと統合する](./how-to-custom-commands-setup-speech-sdk.md)
- [方法: クライアント アプリケーションにアクティビティを送信する](./how-to-custom-commands-send-activity-to-client.md)
- [方法: Web エンドポイントを設定する](./how-to-custom-commands-setup-web-endpoints.md)

その他のプログラミング言語およびプラットフォームの場合:
- [Speech SDK のプログラミング言語、プラットフォーム、シナリオの容量](./speech-sdk.md)
- [音声アシスタントのサンプル コード](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub のサンプルを参照](https://aka.ms/speech/cc-samples)
