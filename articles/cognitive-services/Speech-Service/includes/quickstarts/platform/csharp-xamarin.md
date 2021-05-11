---
title: 'クイックスタート: Speech SDK for C# (Xamarin) プラットフォームの設定 - Speech サービス'
titleSuffix: Azure Cognitive Services
description: Speech サービス SDK を使用して C# Xamarin のプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ee95f272b57ac4ed1219c682ce0eb97cc965ea3
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108157512"
---
このガイドでは、[Xamarin](/xamarin/get-started/what-is-xamarin) 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。これは、.NET を使用する iOS、Android、および Windows 用の最新の高パフォーマンス アプリケーションを構築するためのオープン ソース プラットフォームです。 このパッケージ名の使用を自分で開始する場合は、NuGet コンソールで `Install-Package Microsoft.CognitiveServices.Speech` を実行します。

> [!NOTE]
> Xamarin 用 Speech SDK では、Windows Desktop (x86 および x64) またはユニバーサル Windows プラットフォーム (x86、x64、ARM、ARM64)、Android (x86、ARM32 または 64)、iOS (x64 シミュレーターおよび ARM64) がサポートされています。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)が必要です。 これを初めてインストールする場合、再起動が必要になる場合があります。
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Visual Studio プロジェクトを作成して Speech SDK をインストールする

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

これで Speech SDK がインストールされました。 前の手順で作成した "helloworld" プロジェクトを削除または再利用できるようになりました。

これで、下記の「[次の手順](#next-steps)」に進むことができます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [windows](../quickstart-list.md)]
