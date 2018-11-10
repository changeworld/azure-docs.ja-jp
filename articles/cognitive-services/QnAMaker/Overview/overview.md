---
title: QnA Maker とは
titleSuffix: Azure Cognitive Services
description: QnA Maker は、ユーザーの自然言語の質問にカスタムの機械学習インテリジェンスを適用して最適な回答を提供する、クラウドベースの API サービスです。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: bd859183a13e0f8a21cdd2eabb464b718e949464
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212218"
---
# <a name="what-is-qna-maker"></a>QnA Maker とは

QnA Maker は、データに基づく対話型の Q&A レイヤーを作成できるクラウドベースの API サービスです。 

QnA Maker を使うと、よくある質問 (FAQ) の URL、製品マニュアル、サポート ドキュメント、カスタムの Q&A などの半構造化コンテンツからナレッジベース (KB) を作成できます。 QnA Maker サービスでは、ユーザーが自然言語を使って行った質問に対し、ナレッジ ベースにある Q&A から回答となる可能性が最も高いものが提示されます。

使いやすい [Web ポータル](https://qnamaker.ai)により、開発者エクスペリエンスがなくても、サービスを作成、管理、トレーニング、公開することができます。 サービスがエンドポイントに公開されると、チャット ボットなどのクライアント アプリケーションは、ユーザーとの会話を管理して、質問を受けてその回答を返すことができます。 

![概要](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>QnA Maker の主要なプロセス

QnA Maker には、データ関連の主要サービスが 2 つあります。

* **抽出**: FAQ や製品マニュアルなど、構造化されたり、半構造化されたりしている[データ ソース](../Concepts/data-sources-supported.md)から、構造化された質問と回答のデータが抽出されます。 この抽出は、KB の[作成](https://aka.ms/qnamaker-docs-createkb)の一部として、または後続の編集プロセスの一部として行うことができます。

* **照合**: ナレッジ ベースの[トレーニングとテスト](https://aka.ms/qnamaker-docs-trainkb)が済んだら、それを[公開](https://aka.ms/qnamaker-docs-publishkb)します。 これにより、QnA Maker のナレッジ ベースに対してエンドポイントが有効になり、ボットやクライアント アプリで使用できるようになります。 このエンドポイントは、ユーザーの質問を受け付け、ナレッジ ベース内の最適な回答と、一致の信頼度スコアで応答します。

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>QnA Maker のアーキテクチャ

QnA Maker のアーキテクチャは、次の 2 つのコンポーネントで構成されています。

1. **QnA Maker 管理サービス**: QnA Maker ナレッジ ベースの管理エクスペリエンスであり、初期作成、更新、トレーニング、および公開が含まれています。 これらのアクティビティは、[ポータル](https://qnamaker.ai)または[管理 API](https://aka.ms/qnamaker-v4-apis) を使って行うことができます。 

2. **QnA Maker のデータおよびランタイム**: 指定したリージョンの Azure サブスクリプションにデプロイされます。 KB のコンテンツは [Azure Search](https://azure.microsoft.com/services/search/) に格納され、エンドポイントは[アプリ サービス](https://azure.microsoft.com/services/app-service/)としてデプロイされます。 分析用に [Application Insights](https://azure.microsoft.com/services/application-insights/) リソースをデプロイすることもできます。

![アーキテクチャ](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>サービスの概要

- [FAQ ボットを作成する](https://aka.ms/qnamaker-docs-create-faqbot)ための完全な**非コード** エクスペリエンスです。
- **予測のための帯域幅調整はありません**。 トランザクションの数ではなく、サービスのホストに対して課金されます。 詳しくは、[料金に関するページ](https://aka.ms/qnamaker-docs-pricing)をご覧ください。
- **必要に応じてスケーリング**できます。 実際のシナリオに適した個々のコンポーネントの適切な SKU を選択できます。 QnA Maker サービスの[容量の選択](https://aka.ms/qnamaker-docs-capacity)方法をご覧ください。
- **完全なデータ コンプライアンス**です。 予測サービス コンポーネントは、Azure サブスクリプションとそのコンプライアンス境界内にデプロイされます。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker サービスを作成する](../how-to/set-up-qnamaker-service-azure.md)
