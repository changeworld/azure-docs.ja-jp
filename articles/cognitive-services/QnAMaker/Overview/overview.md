---
title: QnA Maker について - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker を使用すると、FAQ ドキュメント、URL、製品マニュアルなどの半構造化コンテンツからの質問と回答サービスをいっそう強力にできます。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: overview
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: f34cec047c18a6db10b5adda82800c51d44c1155
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "36295147"
---
# <a name="what-is-qna-maker"></a>QnA Maker とは

[QnA Maker](https://qnamaker.ai) を使用すると、FAQ (よくあるご質問) ドキュメント、URL、製品マニュアルなどの半構造化コンテンツからの質問と回答サービスをいっそう強力にできます。 ユーザーのクエリに柔軟に対応する質問と回答のモデルを作成し、自然な会話で使用するようボットをトレーニングする応答を提供することができます。

使いやすいグラフィカル ユーザー インターフェイスにより、開発の経験がなくても、サービスを作成、管理、トレーニングし、稼働させることができます。

![概要](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>ハイライト

- [FAQ ボットを作成する](https://aka.ms/qnamaker-docs-create-faqbot)ための完全な**非コード** エクスペリエンスです。
- **ネットワークの調整はもう必要ありません**。 トランザクションの数ではなく、サービスのホストに対して課金されます。 詳しくは、[料金に関するページ](https://aka.ms/qnamaker-docs-pricing)をご覧ください。
- **ニーズに応じてスケーリングします**。 実際のシナリオに適した個々のコンポーネントの適切な SKU を選択できます。 QnA Maker サービスの[容量の選択](https://aka.ms/qnamaker-docs-capacity)方法をご覧ください。
- **完全なデータ コンプライアンス**です。 データとランタイム コンポーネントは、ユーザーの Azure サブスクリプションとそのコンプライアンス境界内に展開されます。 詳細については、以下を参照してください。

## <a name="key-qna-maker-processes"></a>QnA Maker の主要なプロセス

QnA Maker では、データに対して 2 つの主要なサービスが提供されます。

* **抽出**: FAQ や製品マニュアルなどの半構造化されたデータ ソースから、構造化された質問と回答のデータが抽出されます。 この抽出は、ナレッジ ベースを作成するときに行われます。 独自のナレッジ ベースを[こちら](https://aka.ms/qnamaker-docs-createkb)で作成してください。

* **照合**: ナレッジ ベースの[トレーニングとテスト](https://aka.ms/qnamaker-docs-trainkb)が済んだら、それを[公開](https://aka.ms/qnamaker-docs-publishkb)します。 これにより、QnA Maker のナレッジ ベースに対してエンドポイントが有効になり、ボットやアプリで使用できるようになります。 このエンドポイントは、ユーザーの質問を受け付け、ナレッジ ベースで最もよく一致した質問/回答と、一致の信頼スコアで応答します。

## <a name="qna-maker-architecture"></a>QnA Maker のアーキテクチャ

QnA Maker のスタックは、次の部分で構成されます。

1. **QnA Maker 管理サービス (制御プレーン)**: QnA Maker ナレッジ ベースの管理エクスペリエンスであり、初期作成、更新、トレーニング、および公開が含まれています。 これらのアクティビティは、[ポータル](https://qnamaker.ai)または[管理 API](https://aka.ms/qnamaker-v4-apis) を使って行うことができます。 管理サービスは、次のランタイム コンポーネントと通信します。

2. **QnA Maker ランタイム (データ プレーン)**: データとランタイムは、ユーザーが選択したリージョンのユーザーの Azure サブスクリプション内に展開されます。 顧客の質問/回答の内容は [Azure Search](https://azure.microsoft.com/services/search/) に格納され、ランタイムは [App Service](https://azure.microsoft.com/services/app-service/) として展開されます。 必要に応じて、分析用に [Application Insights](https://azure.microsoft.com/services/application-insights/) リソースを展開することもできます。

![アーキテクチャ](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker サービスを作成する](../how-to/set-up-qnamaker-service-azure.md)
