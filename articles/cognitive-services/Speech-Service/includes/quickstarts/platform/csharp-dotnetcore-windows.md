---
title: クイック スタート:Speech SDK for C# .NET Core プラットフォームの設定 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech サービス SDK を使用して Windows または macOS 上の .NET Core で C# のプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: lajanuar
ms.openlocfilehash: 7c05c58d1994b32703e2900070405aea30e39479
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070107"
---
このガイドでは、C# .NET Core 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。 このパッケージ名の使用を自分で開始する場合は、NuGet コンソールで `Install-Package Microsoft.CognitiveServices.Speech` を実行します。

> [!NOTE]
> .NET Core は、[.NET Standard](/dotnet/standard/net-standard) 仕様を実装した、オープン ソース、クロスプラットフォームの .NET プラットフォームです。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)が必要です。 これを初めてインストールする場合、再起動が必要になる場合があります。
* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 以降

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Visual Studio プロジェクトを作成して Speech SDK をインストールする

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="next-steps"></a>次の手順

[!INCLUDE [windows](../quickstart-list.md)]