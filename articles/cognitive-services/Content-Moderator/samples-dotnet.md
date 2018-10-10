---
title: コード サンプル - Content Moderator (.NET)
description: アプリケーションで Content Moderator を使用する
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: c5465a2de0924037eb93b9de652e93e85f166737
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223153"
---
# <a name="net-sdk-samples"></a>.NET SDK のサンプル

次のリストは、Azure Content Moderator for .NET を使用してビルドされたコード サンプルへのリンクです。

- **ヘルパー ライブラリ**: [他のサンプルで使用する Content Moderator クライアントを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs)。 [クイック スタート](content-moderator-helper-quickstart-dotnet.md)のページを参照してください。

## <a name="moderation"></a>モデレート

- **画像のモデレート**: [成人向けのきわどいコンテンツ、テキスト、および顔の画像を評価します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs)。 [クイック スタート](image-moderation-quickstart-dotnet.md)のページを参照してください。
- **カスタム画像**: [カスタム画像のリストを使用してモデレートします](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs)。 [クイック スタート](image-lists-quickstart-dotnet.md)のページを参照してください。

> [!NOTE]
> **画像リスト数は 5 個**、各リストの**画像数は 10,000 個**という上限があります。
>

- **テキストのモデレート**: [不適切な表現と個人を特定できる情報 (PII) のテキストを検査します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs)。 [クイック スタート](text-moderation-quickstart-dotnet.md)のページを参照してください。
- **カスタム用語**: [カスタム用語のリストを使用してモデレートします](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs)。 [クイック スタート](term-lists-quickstart-dotnet.md)のページを参照してください。

> [!NOTE]
> 上限は**用語のリストが 5 つ**で、各リストでは**用語が 10,000 個を超えてはいけません**。
>

- **ビデオのモデレート**: [成人向けのきわどいコンテンツのビデオをスキャンし、結果を取得します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs)。 [クイック スタート](video-moderation-api.md)のページを参照してください。

## <a name="review"></a>レビュー

- **画像ジョブ**: [スキャンしてレビューを作成するモデレート ジョブを開始します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs)。 [クイック スタート](moderation-jobs-quickstart-dotnet.md)のページを参照してください。
- **画像のレビュー**: [人がモデレート作業を行うためのレビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs)。 [クイック スタート](moderation-reviews-quickstart-dotnet.md)のページを参照してください。
- **ビデオ レビュー**: [人がモデレート作業を行うためのビデオ レビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs)。 [クイック スタート](video-reviews-quickstart-dotnet.md)のページを参照してください
- **ビデオ トランスクリプト レビュー**: [人がモデレート作業を行うためのビデオ トランスクリプト レビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs)。[クイック スタート](video-reviews-quickstart-dotnet.md)のページを参照してください

すべての .NET サンプルは、[GitHub にある Content Moderator の .NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)に関するページでご覧ください。
