---
title: Content Moderator の概要
titlesuffix: Azure Cognitive Services
description: Content Moderator を使い始める方法。
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: c2ac0ccd89b5f1436a151e3d69c5d7423090f244
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225296"
---
# <a name="get-started-with-content-moderator"></a>Content Moderator の概要

Content Moderator は、次の方法で使い始めます。

- [レビュー ツールを使用して開始](#start-with-the-review-tool)し、API キーを取得してレビュー チームを作成します。 追加の手順なしに、API キーを使用して、コンテンツをスキャンするためのモデレーション用 API と、レビューを生成するためのレビュー用 API を呼び出すことができるのが利点です。
- Azure portal で [Content Moderator にサブスクライブ](#start-with-the-apis)して API キーを取得します。 [API リファレンス](api-reference.md)と [SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net) を確認してください。 レビュー チームを作成するには、さらにオンラインで サインアップする必要があります。
- [フロー コネクタとテンプレートを使用して](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/)、使いやすいデザイナーとの幅広い統合を確認します。

選択するオプションに関係なく、[資格情報の管理](review-tool-user-guide/credentials.md)の記事を確認して、お使いの API 資格情報を見つけてください。

## <a name="start-with-the-review-tool"></a>レビュー ツールを開始する
Content Moderator レビュー ツール Web サイトで[サインアップ](http://contentmoderator.cognitive.microsoft.com/)します。

![Content Moderator ホーム ページ](images/homepage.PNG)

### <a name="create-a-review-team"></a>レビュー チームを作成する
チームに名前を指定してください。 仕事仲間を招待する場合は、その電子メール アドレスを入力することで可能になります。

![チーム メンバーを招待する](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>画像をアップロードするかテキストを入力する
**[Try]\(試す) > [画像]** または **[Try]\(試す) > [テキスト]** をクリックします。 最大 5 つのサンプル画像をアップロードするか、モデレーション用のサンプル テキストを入力します。

![画像またはテキスト モデレーションを試す](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>自動モデレーション用に送信する
自動モデレーション用にコンテンツを送信します。 レビュー ツールは内部で、モデレーション API を呼び出し、コンテンツをスキャンします。 スキャンが完了したら、レビューを待機している結果について通知するメッセージが表示されます。

![ファイルをモデレートする](images/submitted.png)

### <a name="review-and-confirm-results"></a>結果をレビューおよび確認する
自動モデレートされたタグを確認し、必要に応じて変更して、**[次へ]** ボタンを使用して送信します。 ビジネス アプリケーションが Moderator API を呼び出すと、タグ付きコンテンツは、キュー追加を開始し、人間のレビュー チームにレビューされる用意が整えられます。 このアプローチを使用して、大量のコンテンツをすばやくレビューします。

![結果の確認](images/reviewresults.png)

[レビュー ツールの機能](Review-Tool-User-Guide/human-in-the-loop.md)のすべてを使用する方法を学習するか、次のセクションに進んで API について学習します。 [資格情報の管理](review-tool-user-guide/credentials.md)の記事に示すように、レビュー ツールで API キーを自分用にプロビジョニングしているので、サインアップ手順をスキップします。

### <a name="use-the-apis"></a>API の使用

Content Moderator をビジネス アプリケーションと統合する方法について説明します。 [API リファレンス](api-reference.md)と [SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net) を確認してください。

## <a name="subscribe-in-the-azure-portal"></a>Azure portal でサブスクライブする

Azure portal で、[Content Moderator にサブスクライブ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)します。 次の API のいずれかから開始します。

### <a name="image-moderation"></a>画像のモデレート

[API コンソール](try-image-api.md)から開始するか、または [.NET クイックスタート](image-moderation-quickstart-dotnet.md)を使用して、画像をスキャンして、タグ、信頼度スコア、および他の抽出された情報を使用することによって、成人向けや猥褻な可能性のあるコンテンツを検出します。

### <a name="text-moderation"></a>テキストのモデレート

[API コンソール](try-text-api.md)を開始するか、[.NET クイックスタート](text-moderation-quickstart-dotnet.md)を使用して、可能性のある罰当たりな表現、機械支援の不要なテキスト分類 (プレビュー)、個人的に識別可能な情報 (PII) に関してテキスト コンテンツをスキャンします。 


### <a name="video-moderation"></a>ビデオのモデレート

[.NET クイックスタート](video-moderation-api.md)を開始してビデオをスキャンし、成人向けや猥褻なコンテンツの可能性があるものを検出します。 


### <a name="review-apis"></a>API の確認

ジョブ、レビュー、およびワークフロー API から選択することによりここから開始します。

- [ジョブ API](try-review-api-job.md) は、モデレーション API を使用することによりコンテンツをスキャンし、レビュー ツールでレビューを生成します。 
- [レビュー API](try-review-api-review.md) は、最初にコンテンツをスキャンすることなく、人間の モデレーター用の画像、テキスト、またはビデオ レビューを直接作成します。 
- [ワークフロー API](try-review-api-workflow.md) は、自分のチームが作成するカスタム ワークフローに関する詳細を作成、更新、取得します。

## <a name="next-steps"></a>次の手順

[API リファレンス](api-reference.md)と [SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net) を確認してください。 [.NET SDK のサンプル](sdk-and-samples.md#net-sdk-samples)、[C# での REST API のサンプル](https://github.com/sanjeev3/azure-docs-pr/blob/master/articles/cognitive-services/Content-Moderator/sdk-and-samples.md#rest-api-samples-in-c)、および[チュートリアル](sdk-and-samples.md#tutorials)を使用して、統合をすぐに始めます。
