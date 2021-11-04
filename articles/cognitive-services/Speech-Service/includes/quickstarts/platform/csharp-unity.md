---
title: クイック スタート:Speech SDK for C# Unity プラットフォームの設定 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech サービス SDK を使用して C# Unity のプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: eur
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: 5e2a9f88914e83300207895d73c6c0c9d2ddd6cb
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502460"
---
このガイドでは、[Unity](https://unity3d.com/) 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。

> [!NOTE]
> Unity 用 Speech SDK では、Windows Desktop (x86 および x64) またはユニバーサル Windows プラットフォーム (x86、x64、ARM または ARM64)、Android (x86、ARM32 または 64)、iOS (x64 シミュレーターおよび ARM64)、Mac (x64) がサポートされています。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

- Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)が必要です。 これを初めてインストールする場合、再起動が必要になる場合があります。
- [Unity 2018.3 以降](https://store.unity.com/)および [UWP ARM64 のサポートを追加する Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)。
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 Visual Studio 2017 バージョン 15.9 以降も許容されます。
- Windows ARM64 をサポートするため、[ARM64 用のオプションのビルド ツール、および ARM64 用の Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) をインストールします。
- Android では、開発に対応しており、マイクが動作する ARM ベースの Android デバイス (API 23: Android 6.0 Marshmallow 以上) が必要です。
- iOS では、開発に対応しており、マイクが動作する iOS デバイス (ARM64) が必要です。
- macOS では、Mac デバイス (x64) と、Unity Player 設定でマイクへのアクセスを統合サポートするために Unity 2019 (またはそれ以上) の最新の LTS バージョンが必要です。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

Unity 用 Speech SDK をインストールするには、次の手順を実行します。

1. Unity アセット パッケージ (.unitypackage) としてパッケージ化され、既に Unity に関連付けられている [Unity 用 Speech SDK](https://aka.ms/csspeech/unitypackage) をダウンロードして開きます。 アセット パッケージを開くと、**[Import Unity Package]\(Unity パッケージのインポート\)** ダイアログ ボックスが表示されます。 この手順を実行するには、必要に応じて空のプロジェクトを作成して開く必要があります。

   [![Unity エディターの [Import Unity Package]\(Unity パッケージのインポート\) ダイアログ ボックス](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. すべてのファイルが選択されていることを確認したら、**[Import]\(インポート\)** を選択します。 しばらくすると、Unity アセット パッケージがプロジェクトにインポートされます。

Unity にアセット パッケージをインポートする方法の詳細については、[Unity のドキュメント](https://docs.unity3d.com/Manual/AssetPackages.html)を参照してください。

## <a name="next-steps"></a>次の手順

[!INCLUDE [windows](../quickstart-list.md)]
