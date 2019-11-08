---
title: クイック スタート:音声を合成する、C# (.NET Core) - Speech Service
titleSuffix: Azure Cognitive Services
description: Windows の .NET Core で C# と Speech SDK を使用して音声を合成する方法について説明します
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 670d32a93df9e2a0363163079b50c7306f81975e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504885"
---
> [!NOTE]
> .NET Core は、[.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 仕様を実装した、オープン ソース、クロスプラットフォームの .NET プラットフォームです。

## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [使用する開発環境をセットアップする](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=dotnetcore)
## <a name="add-sample-code"></a>サンプル コードを追加する

1. `Program.cs` を開き、その中のすべてのコードを次の内容に置き換えます。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](~/articles/cognitive-services/Speech-Service/regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、 **[ビルド]**  >  **[ソリューションのビルド]** を選択します。 コードはエラーなくコンパイルされるはずです。

    ![[ソリューションのビルド] オプションが強調表示された、Visual Studio アプリケーションのスクリーン ショット](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "成功したビルド")

1. アプリケーションを起動します。 メニュー バーから、 **[デバッグ]**  >  **[デバッグの開始]** を選択するか、**F5** キーを押します。

    ![[デバッグの開始] オプションが強調表示された、Visual Studio アプリケーションのスクリーン ショット](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "アプリを起動してデバッグする")

1. コンソール ウィンドウが表示され、何かテキストを入力するように求められます。 いくつかの単語または文を入力してください。 入力したテキストが Speech Services に転送されて音声に合成され、スピーカーで再生されます。

    ![合成が成功した後のコンソール出力のスクリーンショット](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "合成が成功した後のコンソール出力")

## <a name="next-steps"></a>次の手順

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>関連項目

- [カスタム音声を作成する](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [カスタム音声サンプルを録音する](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
