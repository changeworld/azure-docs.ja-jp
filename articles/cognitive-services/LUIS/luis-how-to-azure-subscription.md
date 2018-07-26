---
title: Azure エンドポイント サブスクリプションの管理 | Microsoft Docs
description: この記事では、ご自身の LUIS アカウントに対して従量制課金エンドポイント キーを作成し、次の支払いプランに従って、お使いのエンドポイントに無制限トラフィックを提供します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: diberry
ms.openlocfilehash: 0b735499ae589e44c2ce5076fce38ec47ddd69c7
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223281"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Azure エンドポイント サブスクリプション キーの管理

テストおよびプロトタイプについてのみ、Free (F0) レベルを使用します。 運用システムについては、[有料](https://aka.ms/luis-price-tier)レベルを使用してください。 

> [!NOTE]
> 運用環境でのエンドポイントのクエリには、[作成者キー](luis-concept-keys.md#authoring-key)を使用しないでください。

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>LUIS エンドポイント キーの作成

1. **[Microsoft Azure](https://ms.portal.azure.com/)** にサインインします 
2. 左側のパネルの緑色の **+** 記号をクリックし、マーケットプレースで "LUIS" を検索して、**[Language Understanding]** をクリックします。次に、**[経験の作成]** に従って LUIS サブスクリプション アカウントを作成します。 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

3. アカウント名、価格レベルなどの設定によってサブスクリプションを構成します。 

    ![Azure API の選択](./media/luis-azure-subscription/azure-api-choice.png) 

4. LUIS サービスを作成したら、**[リソース管理] -> [キー]** で生成されたアクセス キーを表示できます。  

    ![Azure キー](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > * リージョンの [LUIS](luis-reference-regions.md) Web サイトにログインし、[新しい LUIS エンドポイント キーを追加](luis-how-to-manage-keys.md#assign-endpoint-key)します。 
    > * 作成した Azure サービスの名前は、リージョンの [LUIS](luis-reference-regions.md) 発行ページで選択できるように覚えておく必要があります。  

## <a name="change-luis-pricing-tier"></a>LUIS 価格レベルの変更

1.  [Azure](https://portal.azure.com) で、ご自身の LUIS サブスクリプションを検索します。 LUIS サブスクリプション キーをクリックします。
    ![LUIS サブスクリプション の検索](./media/luis-usage-tiers/find.png)
2.  使用可能な価格レベルを表示するには、**[価格レベル]** をクリックします。 
    ![価格レベルの表示](./media/luis-usage-tiers/subscription.png)
3.  価格レベルをクリックし、**[選択]** をクリックして、ご自身の変更内容を保存します。 
    ![ご自身の LUIS 支払いレベルの変更](./media/luis-usage-tiers/plans.png)
4.  価格の変更が完了したら、ポップアップ ウィンドウで新しい価格レベルを確認します。 
    ![ご自身の LUIS 支払いレベルの確認](./media/luis-usage-tiers/updated.png)
5. 必ず **[発行]** ページで[このエンドポイント キーを割り当て](luis-how-to-manage-keys.md#assign-endpoint-key)て、すべてのエンドポイントのクエリで使用します。 

## <a name="exceed-pricing-tier-usage"></a>価格レベルの使用量の超過
価格レベルごとに、LUIS アカウントに対して許可されるエンドポイント要求の割合が決まっています。 要求の割合が、従量制課金アカウントで許可されている 1 分または 1 か月あたりの割合を超えると、要求は "429: 要求が多すぎます" という HTTP エラーを受け取ります。

価格レベルごとに、1 か月に許可される累積要求数が決まっています。 要求の合計数が、許可されている割合を超えると、要求は "403: 許可されていません" という HTTP エラーを受け取ります。  

## <a name="viewing-summary-usage"></a>使用状況の表示
Azure で LUIS の使用に関する情報を表示できます。 **[概要]** ページには、呼び出し、エラーなど、最近の概要情報が表示されます。 LUIS エンドポイント要求を行ったらすぐに、**[概要] ページ**を確認します。使用状況が表示されるまでに最大で 5 分かかります。

![使用状況の表示](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>使用状況グラフのカスタマイズ
メトリックより、データの詳細ビューを確認できます。

![Default metrics](./media/luis-usage-tiers/metrics-default.png)

期間およびメトリックの種類のメトリック グラフを構成できます。 

![カスタム メトリック](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>トランザクション合計しきい値のアラート
トランザクションしきい値に達したこと、たとえば、トランザクション数が 10,000 になったことを知るには、アラートを作成します。 

![既定のアラート](./media/luis-usage-tiers/alert-default.png)

特定の期間に対して、**呼び出し合計**メトリックのメトリック アラートを追加します。 アラートを受け取るすべてのユーザーのメール アドレスを追加します。 アラートを受け取るすべてのシステムの Webhook を追加します。 アラートがトリガーされたときに、ロジック アプリを実行することもできます。 

## <a name="next-steps"></a>次の手順

[バージョン](luis-how-to-manage-versions.md)を使用してご自身の LUIS アプリへの変更を管理する方法を確認します。