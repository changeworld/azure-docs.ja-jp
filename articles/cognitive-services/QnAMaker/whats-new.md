---
title: QnA Maker サービスの新機能
titleSuffix: Azure Cognitive Services
description: この記事には、QnA Maker に関するニュースが含まれています。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 07/16/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 959a6d5c5ed4b606c5a5850264422b6e460eb8f5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020447"
---
# <a name="whats-new-in-qna-maker"></a>QnA Maker の新機能

サービス内の新機能について説明します。 以下の項目には、リリース ノート、ビデオ、ブログの投稿、およびその他の種類の情報が含まれる可能性があります。 このページをブックマークして、常にサービスの最新情報を確認してください。

[!INCLUDE [Custom question answering](./includes/new-version.md)]

## <a name="release-notes"></a>リリース ノート

QnA Maker の新機能について説明します。

### <a name="may-2021"></a>2021 年 5 月

* QnA Maker マネージドが、[Text Analytics リソース](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)のカスタム質問と回答機能として再導入されました。
* カスタム質問と回答では、非構造化ドキュメントがサポートされます。
* API を介して渡されるドキュメント テキストからユーザー クエリの回答を生成するために、[事前構築済み API](how-to/using-prebuilt-api.md) が導入されました。

### <a name="november-2020"></a>2020 年 11 月

* 無料パブリック プレビューでの提供が始まった新しいバージョンの QnA Maker。 詳細については、[こちら](https://techcommunity.microsoft.com/t5/azure-ai/introducing-qna-maker-managed-now-in-public-preview/ba-p/1845575)をご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-QnA-managed-Now-in-Public-Preview/player]
* 簡素化されたリソースの作成
* エンド ツー エンドのリージョン サポート
* ディープ ラーニングされたランキング モデル
* 正確な回答のためのコンピューター読み取りの理解
  
### <a name="july-2020"></a>2020 年 7 月

* [メタデータ: `OR` による複数のメタデータ ペアの論理の組み合わせ](how-to/query-knowledge-base-with-metadata.md#logical-or-using-strictfilterscompoundoperationtype-property)
* Cognitive Search エンドポイントをプライベートにしたうえで引き続き QnA Maker にアクセスできるように構成する[手順](how-to/network-isolation.md)。
* Free レベルの Cognitive Search リソースは、[90 日間非アクティブな期間](how-to/set-up-qnamaker-service-azure.md#inactivity-policy-for-free-search-resources)が経過すると削除されます。

### <a name="june-2020"></a>2020 年 6 月

* すばやく簡単に手順に取り組めるよう、[Power Virtual Agent](tutorials/integrate-with-power-virtual-assistant-fallback-topic.md) のチュートリアルを更新

### <a name="may-2020"></a>2020 年 5 月

* [Azure ロールベースのアクセス制御 (Azure RBAC)](concepts/role-based-access-control.md)
* 回答の[リッチテキスト編集](how-to/edit-knowledge-base.md#rich-text-editing-for-answer)

### <a name="march-2020"></a>2020 年 3 月

* TLS 1.2 は現在、このサービスへのすべての HTTP 要求に適用されるようになりました。 詳細については、[Azure Cognitive Services のセキュリティ](../cognitive-services-security.md)に関するページを参照してください。

### <a name="february-2020"></a>2020 年 2 月

* GenerateAnswer API を使用した [NPM パッケージ](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)

### <a name="november-2019"></a>2019 年 11 月

* QnA Maker に対する[政府クラウドのサポート](../../azure-government/compare-azure-government-global-azure.md#guidance-for-developers)
* [複数ターン](./how-to/multiturn-conversation.md)機能が一般提供になりました
* レベル 1 の言語で[おしゃべりのサポート](./how-to/chit-chat-knowledge-base.md#language-support)が利用可能になりました

### <a name="october-2019"></a>2019 年 10 月

* QnA Maker サービスにおけるすべてのナレッジ ベースの言語を明示的に設定

### <a name="september-2019"></a>2019 年 9 月

* XLS ファイル形式を使用したインポートとエクスポート。

### <a name="june-2019"></a>2019 年 6 月

* フランス語、イタリア語、ドイツ語、スペイン語、ポルトガル語向けの[ランカー モデル](concepts/query-knowledge-base.md#ranker-process)の改善

### <a name="april-2019"></a>2019 年 4 月

* Web サイト コンテンツ抽出のサポート
* 認証済みのアクセスからの [SharePoint ドキュメント](how-to/add-sharepoint-datasources.md)のサポート

### <a name="march-2019"></a>2019 年 3 月

* [アクティブ ラーニング](how-to/improve-knowledge-base.md)による、実際のユーザーの質問に基づく新しい質問の候補の提供
* 英語向けの自然言語処理 (NLP) [ランカー](concepts/query-knowledge-base.md#ranker-process) モデルの改善

> [!div class="nextstepaction"]
> [QnA Maker サービスを作成する](how-to/set-up-qnamaker-service-azure.md)

## <a name="cognitive-service-updates"></a>Cognitive Services の更新プログラム

[Cognitive Services に対する Azure 更新プログラムのお知らせ](https://azure.microsoft.com/updates/?product=cognitive-services)
