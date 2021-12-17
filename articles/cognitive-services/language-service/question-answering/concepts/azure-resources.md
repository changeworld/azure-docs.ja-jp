---
title: Azure リソース - 質問応答
description: 質問応答では、それぞれ異なる目的で複数の Azure ソースを使用します。 それらが個別にどのように使用されるかを理解すると、正しい価格レベルを計画して選択し、価格レベルを変更する時期を把握できるようになります。 これらを組み合わせて使用する方法を理解すると、発生した問題を見つけて修正できるようになります。
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 10/10/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: ddd5ed0c51c412342b6e8093a69cdef900346be2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476682"
---
# <a name="azure-resources-for-question-answering"></a>質問応答のための Azure リソース

質問応答では、それぞれ異なる目的で複数の Azure ソースを使用します。 それらが個別にどのように使用されるかを理解すると、正しい価格レベルを計画して選択し、価格レベルを変更する時期を把握できるようになります。 リソースを "組み合わせて" 使用する方法を理解すると、発生した問題を見つけて修正できるようになります。

## <a name="resource-planning"></a>リソースの計画

初めてナレッジ ベースを開発する場合、プロトタイプ フェーズでは、テストと運用の両方に 1 つのリソースを使用することが一般的です。

> [!TIP]
> "ナレッジ ベース" と "プロジェクト" は、質問応答において同等の用語であり、同じ意味で使用できます。

プロジェクトの開発フェーズに移行するときは、次のことを考慮する必要があります。

* ナレッジ ベース システムで保持される言語の数
* ナレッジ ベースを使用できるようにする必要があるリージョンの数
* システムによって各ドメイン内に保持されるドキュメントの数

## <a name="pricing-tier-considerations"></a>価格レベルに関する考慮事項

通常、次の 3 つのパラメーターを考慮する必要があります。

* **必要なスループット**:
    * 質問応答は無料の機能であり、現在のスループットは、Management API と Prediction API の両方について 10 トランザクション/秒に制限されています。
    * この点は、Azure **Cognitive Search** の SKU の選択にも影響します。詳細については、[こちら](../../../../search/search-sku-tier.md)を参照してください。 さらに、レプリカで Cognitive Search の[容量](../../../../search/search-capacity-planning.md)を調整することが必要になる場合があります。

* **ナレッジ ベースのサイズと数**: 実際のシナリオに合った適切な [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/) を選択してください。 通常は、さまざまな主題の領域の数に基づいて必要なナレッジ ベースの数を決定します。 (1 つの言語の) 1 つの主題の領域を 1 つのナレッジ ベースに含める必要があります。

    カスタム質問応答では、言語リソースを 1 つの言語または複数の言語のどちらで設定するかを選べます。 この選択は、[Language Studio](https://language.azure.com/) で最初のプロジェクトを作成するときに行うことができます。

    > [!IMPORTANT]
    > 特定のレベルで発行できるナレッジ ベースの数は、単一言語の場合は N-1 件、複数言語の場合は N/2 件です。N は、そのレベルで許容される最大インデックスです。 レベルごとに許容されるドキュメントの最大サイズと最大数もチェックしてください。

    たとえば、レベルで 15 個のインデックスが許可されている場合、同じ言語で 14 個のナレッジ ベースを発行できます (発行されたナレッジ ベースあたり 1 インデックス)。 15 番目のインデックは、作成およびテスト用にすべてのナレッジ ベースで使用されます。 異なる言語でナレッジ ベースを作成することを選んだ場合は、発行できるナレッジ ベースの数は 7 個だけです。

* **ソースとしてのドキュメントの数**: 質問応答は無料の機能であり、ソースとして追加できるドキュメントの数に制限はありません。 

次の表は、いくつかの基本的なガイドラインを示したものです。

|                            |Azure Cognitive Search | 制限事項                      |
| -------------------------- |------------ | -------------------------------- |
| **実験**        |Free レベル    | 発行できる KB は 2 つまで (最大サイズ 50 MB)  |
| **Dev/Test 環境**   |Basic        | 発行できる KB は 14 個まで (最大サイズ 2 GB)    |
| **運用環境** |Standard     | 発行できる KB は 49 個まで (最大サイズ 25 GB) |

## <a name="recommended-settings"></a>推奨設定

カスタム質問応答は無料の機能であり、現在のスループットは、Management API と Prediction API の両方について 10 トランザクション/秒に制限されています。 サービスの目標を 10 トランザクション/秒にするには、Azure Cognitive Search の S1 (1 つのインスタンス) SKU をお勧めします。

## <a name="keys-in-question-answering"></a>質問応答のキー

カスタム質問応答機能により、顧客のサブスクリプションのサービスにアクセスするために、**オーサリング キー** と **Azure Cognitive Search キー** の 2 種類のキーが処理されます。

これらのキーは、API を介してサービスに要求を行うときに使用します。

|名前|場所|目的|
|--|--|--|
|オーサリングまたはサブスクリプション キー|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|これらのキーは、Language Service API にアクセスするために使用されます。 これらの API を使用して、ナレッジ ベース内の質問と回答を編集したり、ナレッジ ベースを公開したりできます。 これらのキーは、新しいリソースの作成時に作成されます。<br><br>これらのキーは、 **[キーとエンドポイント]** ページの **Cognitive Services** リソースで確認できます。|
|Azure Cognitive Search の管理者キー|[Azure Portal](../../../../search/search-security-api-keys.md)|これらのキーは、ユーザーの Azure サブスクリプションにデプロイされている Azure Cognitive Search サービスとの通信に使用されます。 Azure Cognitive Search リソースをカスタム質問応答機能に関連付けると、管理者キーが質問応答に自動的に渡されます。 <br><br>これらのキーは、**Azure Cognitive Search** リソースの **[キー]** ページで確認できます。|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Azure portal でオーサリング キーを確認する

オーサリング キーの表示とリセットは、言語リソースのカスタム質問応答機能を追加した Azure portal から行うことができます。

1. Azure portal で言語リソースに移動し、種類が *Cognitive Services* であるリソースを選択します。

    > [!div class="mx-imgBorder"]
    > ![質問応答リソース一覧のスクリーンショット。](../../../qnamaker/media/qnamaker-how-to-setup-service/resources-created-question-answering.png)

2. **[キーとエンドポイント]** に移動します。

    > [!div class="mx-imgBorder"]
    > ![サブスクリプション キーのスクリーンショット。](../../../qnamaker/media/qnamaker-how-to-key-management/custom-qna-keys-and-endpoint.png)

### <a name="management-service-region"></a>管理サービスのリージョン

カスタム質問応答では、管理と予測の両方のサービスが同じリージョンに併置されます。

## <a name="resource-purposes"></a>リソースの目的

カスタム質問応答機能を使用して作成された各 Azure リソースには、特定の目的があります。

* 言語リソース (リソースを評価するコンテキストに応じて、Text Analytics とも呼ばれます)。
* Cognitive Search リソース

### <a name="language-resource"></a>言語リソース

カスタム質問応答機能を備えた言語リソースにより、Authoring および Publishing API へのアクセスが提供され、ランク付けランタイムがホストされ、テレメトリが提供されます。

### <a name="azure-cognitive-search-resource"></a>Azure Cognitive Search リソース

[Cognitive Search](../../../../search/index.yml) リソースは次の目的で使用されます。

* 質問応答ペアを格納する
* 実行時に質問応答ペアの初期順位 (ランカー #1) を指定する

#### <a name="index-usage"></a>インデックスの使用量

特定のレベルで発行できるナレッジ ベースの数は、単一言語の場合は N-1 件、複数言語の場合は N/2 件です。N は、Azure Cognitive Search レベルで許容されるインデックスの最大数です。 レベルごとに許容されるドキュメントの最大サイズと最大数もチェックしてください。

たとえば、レベルで 15 個のインデックスが許可されている場合、同じ言語で 14 個のナレッジ ベースを発行できます (発行されたナレッジ ベースあたり 1 インデックス)。 15 番目のインデックは、作成およびテスト用にすべてのナレッジ ベースで使用されます。 異なる言語でナレッジ ベースを作成することを選んだ場合は、発行できるナレッジ ベースの数は 7 個だけです。

#### <a name="language-usage"></a>言語の使用方法

カスタム質問応答では、ナレッジ ベース用のサービスを 1 つの言語または複数の言語のどちらで設定するかを選べます。 この選択は、言語リソースで最初のナレッジ ベースを作成するときに行います。

## <a name="next-steps"></a>次のステップ

* 質問応答[ナレッジ ベース](../How-To/manage-knowledge-base.md)について学習する
