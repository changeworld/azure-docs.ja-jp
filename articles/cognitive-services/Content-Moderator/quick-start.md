---
title: Azure Content Moderator の概要 | Microsoft Docs
description: Azure Content Moderator を開始する方法。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: ae4333047ebd95733c7baaed0323a0c2c477d323
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374317"
---
# <a name="get-started-with-content-moderator"></a>Content Moderator の概要

Content Moderator API とレビュー ツールを次の方法で開始します。

- [レビュー ツールを開始](#start-with-the-review-tool)して、API キーおよびレビュー チームの両方を作成します。 レビュー ツールを探索し、Content Moderator API を使用して統合する方法について説明します。
- Azure portal で、[Content Moderator にサブスクライブ](#start-with-the-apis)します。 レビュー チームを作成するには、さらにオンラインで サインアップする必要があります。
- [フロー コネクタとテンプレートを使用して](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/)、使いやすいデザイナーとの幅広い統合を確認します。

選択するオプションに関係なく、[資格情報の管理](review-tool-user-guide/credentials.md)の記事を確認して、お使いの API 資格情報を見つけてください。

## <a name="start-with-the-review-tool"></a>レビュー ツールを開始する
Content Moderator レビュー ツール Web サイトで[サインアップ](http://contentmoderator.cognitive.microsoft.com/)します。

![Content Moderator ホーム ページ](images/homepage.PNG)

### <a name="create-a-review-team"></a>レビュー チームを作成する
チームに名前を指定してください。 仕事仲間を招待する場合は、その電子メール アドレスを入力することで可能になります。

![チーム メンバーを招待する](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>画像をアップロードするかテキストを入力する
**[Try](試す) > [画像]** または **[Try](試す) > [テキスト]** をクリックします。 最大 5 つのサンプル画像をアップロードするか、モデレーション用のサンプル テキストを入力します。

![画像またはテキスト モデレーションを試す](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>自動モデレーション用に送信する
自動モデレーション用にコンテンツを送信します。 レビュー ツールは内部で、モデレーション API を呼び出し、コンテンツをスキャンします。 スキャンが完了したら、レビューを待機している結果について通知するメッセージが表示されます。

![ファイルをモデレートする](images/submitted.png)

### <a name="review-and-confirm-results"></a>結果をレビューおよび確認する
自動モデレートされたタグを確認し、必要に応じて変更して、**[次へ]** ボタンを使用して送信します。 ビジネス アプリケーションが Moderator API を呼び出すと、タグ付きコンテンツは、キュー追加を開始し、人間のレビュー チームにレビューされる用意が整えられます。 このアプローチを使用して、大量のコンテンツをすばやくレビューします。

![結果の確認](images/reviewresults.png)

[レビュー ツールの機能](Review-Tool-User-Guide/human-in-the-loop.md)のすべてを使用する方法を学習するか、次のセクションに進んで API について学習します。 [資格情報の管理](review-tool-user-guide/credentials.md)の記事に示すように、レビュー ツールで API キーを自分用にプロビジョニングしているので、サインアップ手順をスキップします。

### <a name="use-the-apis"></a>API の使用

コンテンツ モデレーションとレビュー ツールの操作を確認したので、Content Moderator を自身のビジネス アプリケーションと統合する方法について説明します。 次のセクションを使用して、SDK とサンプルの理解を深め、促進します。

## <a name="start-with-the-apis"></a>API を開始する

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

[画像モデレート API](image-moderation-api.md) から始まるコンテンツ モデレートについて詳しく説明します。
