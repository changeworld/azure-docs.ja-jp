---
title: コード サンプル - Content Moderator (.NET)
titleSuffix: Azure Cognitive Services
description: SDK を通じて .NET アプリケーション内で Azure Cognitive Services Content Moderator を使用する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: 14750f4bfcab2ffcbb9763d9242ed09273fdc6cd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450421"
---
# <a name="content-moderator-net-sdk-samples"></a>Content Moderator .NET SDK サンプル

次のリストは、Azure Content Moderator for .NET を使用してビルドされたコード サンプルへのリンクです。

## <a name="moderation"></a>モデレート

- **画像のモデレート**: [成人向けのきわどいコンテンツ、テキスト、および顔の画像を評価します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs)。 [.NET SDK のクイックスタート](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)のページを参照してください。
- **カスタム画像**: [カスタム画像のリストを使用してモデレートします](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs)。 [.NET SDK のクイックスタート](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)のページを参照してください。

> [!NOTE]
> **画像リスト数は 5 個**、各リストの **画像数は 10,000 個** という上限があります。
>

- **テキストのモデレート**: [不適切な表現や個人データが含まれていないかテキストをスクリーニングします](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs)。 [.NET SDK のクイックスタート](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)のページを参照してください。
- **カスタム用語**: [カスタム用語のリストを使用してモデレートします](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs)。 [.NET SDK のクイックスタート](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)のページを参照してください。

> [!NOTE]
> 上限は **用語の一覧が 5 つ** で、各一覧では **用語が 10,000 個を超えてはいけません**。
>

- **ビデオのモデレート**: [成人向けのきわどいコンテンツのビデオをスキャンし、結果を取得します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs)。 [クイック スタート](video-moderation-api.md)のページを参照してください。

## <a name="review"></a>確認

- **画像ジョブ**: [スキャンしてレビューを作成するモデレート ジョブを開始します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs)。 [クイック スタート](moderation-jobs-quickstart-dotnet.md)のページを参照してください。
- **画像のレビュー**: [人がモデレート作業を行うためのレビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs)。 [クイック スタート](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)のページを参照してください。
- **ビデオ レビュー**: [人がモデレート作業を行うためのビデオ レビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs)。 [クイック スタート](video-reviews-quickstart-dotnet.md)のページを参照してください
- **ビデオ トランスクリプト レビュー**: [人がモデレート作業を行うためのビデオ トランスクリプト レビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs)。[クイック スタート](video-reviews-quickstart-dotnet.md)のページを参照してください

すべての .NET サンプルは、[GitHub にある Content Moderator の .NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)に関するページでご覧ください。