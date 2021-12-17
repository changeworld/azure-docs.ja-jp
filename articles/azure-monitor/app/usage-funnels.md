---
title: Application Insights のファネル
description: ファネルを使って、顧客によるアプリケーションの操作状況を把握する方法を説明します。
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 9733cc11c346263ad0801f97bd876e5b8c7d4fd9
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131678"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Application Insights のファネルを使って、顧客がどのようにアプリケーションを利用しているか把握する

カスタマー エクスペリエンスの理解は、ビジネスにとって極めて重要です。 アプリケーションが複数のステージに渡る場合は、ほとんどの顧客がプロセス全体を進めているか、またはどこかの時点でプロセスを終了しているかを把握する必要があります。 Web アプリケーション内の一連の手順の進行は、*ファネル* と呼ばれています。 Application Insights のファネルを使用すると、ユーザーの分析情報を取得し、段階を追ってコンバージョン率を監視することができます。 

## <a name="create-your-funnel"></a>ファネルを作成する
ファネルを作成する前に、答えを知りたいテーマを決めておきます。 たとえば、何人のユーザーがホーム ページを閲覧しているか、顧客プロファイルを閲覧しているか、チケットを作成しているか知りたい場合があります。 

ファネルを作成するには、以下を実行します。

1. **[ファネル]** タブで **[編集]** を選択します。
1. "*最上位のステップ*" を選択します。

     :::image type="content" source="./media/usage-funnels/funnel.png" alt-text="[ファネル] タブのスクリーンショット。[編集] タブでステップを選択しています。" lightbox="./media/usage-funnels/funnel.png":::

1. ステップにフィルターを適用するには、 **[フィルターの追加]** を選択します。これは、最上位のステップの項目を選択した後に表示されます。
1. 次に、"*2 番目のステップ*" を選択します。以降、同様に続けます。
1. **[表示]** タブを選択して、ファネルの結果を表示します

      :::image type="content" source="./media/usage-funnels/funnel-2.png" alt-text="[表示] タブに最上位のステップと 2 番目のステップの結果を表示している [ファネル] タブのスクリーンショット。" lightbox="./media/usage-funnels/funnel-2.png":::

1. ファネルを保存して後で表示するには、上部の **[保存]** を選択します。 保存したファネルを開くには、 **[開く]** を使用します。

### <a name="funnels-features"></a>ファネルの機能

- アプリがサンプリングされる場合は、サンプリング バナーが表示されます。 バナーを選択すると、サンプリングをオフにする方法を説明するコンテキスト ペインが開きます。 
- 手順を選択すると、右側に詳細が表示されます。 
- コンバージョン履歴のグラフによって、過去 90 日間のコンバージョン レートが表示されます。 
- ユーザー ツールにアクセスすることで、ユーザーの理解を深めます。 各ステップでフィルターを使用できます。 

## <a name="next-steps"></a>次のステップ
  * [利用状況の概要](usage-overview.md)
  * [ユーザー、セッション、およびイベント](usage-segmentation.md)
  * [保持](usage-retention.md)
  * [ブック](../visualize/workbooks-overview.md)
  * [ユーザー コンテキストの追加](./usage-overview.md)
  * [Power BI へのエクスポート](./export-power-bi.md)