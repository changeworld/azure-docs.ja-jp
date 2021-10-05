---
title: Power BI を使用してナレッジ ストアに接続する
titleSuffix: Azure Cognitive Search
description: 分析と探索を目的に Power BI を使用して Azure Cognitive Search のナレッジ ストアに接続します。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 97a0025437b7f055f9fcf2e7860e926e9693cde6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766951"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Power BI を使用してナレッジ ストアに接続する

この記事では、Power BI Desktop アプリの Power Query を使用してナレッジ ストアに接続し、探索する方法を説明します。 テンプレートを使用してすぐに作業を開始することも、カスタム ダッシュボードを最初から作成することもできます。

Azure Storage のテーブルで構成されるナレッジ ストアは、Power BI で最適に機能します。 テーブルに同じスキルセットとプロジェクション グループのプロジェクションが含まれている場合は、それらを簡単に "結合" して、関連テーブルのフィールドを含むテーブルの視覚化を作成できます。

サンプル データと [Azure portal で作成された](knowledge-store-create-portal.md)ナレッジ ストアを使用するか、または [Postman と REST API](knowledge-store-create-rest.md) を使用して、この記事の手順に従います。 

## <a name="connect-to-azure-storage"></a>Azure Storage への接続

1. [Power BI Desktop](https://powerbi.microsoft.com/downloads/) を起動し、 **[データの取得]** を選択します。 

1. **[データの取得]** ウィンドウで **[Azure]** を選択し、 **[Azure Table Storage]** を選択します。

1. **[接続]** を選択します。

1. **[アカウント名または URL]** に、Azure Storage アカウントの名前を入力します (完全な URL が自動的に作成されます)。

1. メッセージが表示されたら、ストレージ アカウント キーを入力します。

## <a name="set-up-tables"></a>テーブルを設定する

1. 同じスキルセットから作成されたすべてのテーブルの横にあるチェック ボックスをオンにし、 **[読み込む]** を選択します。

   ![テーブルを読み込む](media/knowledge-store-connect-power-bi/power-bi-load-tables.png "テーブルを読み込む")

1. 上部のリボンの **[データの変換]** を選択して **Power Query エディター** を開きます。

   ![Power Query を開く](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power Query を開く")

1. *[hotelReviewsSsDocument]* を選択し、 *[PartitionKey]* 、 *[RowKey]* 、 *[Timestamp]* の各列を削除します。 

   ![テーブルを編集する](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "テーブルを編集する")

1. テーブルの右上にある対立する矢印を含んだアイコンをクリックして、 *[コンテンツ]* を展開します。 列が一覧表示されたら、すべての列を選択し、"metadata" で始まる列の選択を解除します。 **[OK]** をクリックすると、選択した列が表示されます。

   ![コンテンツを展開する](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "コンテンツを展開する")

1. 列の左上にある [ABC-123] アイコンをクリックして、次の列のデータ型を変更します。

   + *[content.latitude]* と *[Content.longitude]* には、 **[小数]** を選択します。
   + *[Content.reviews_date]* と *[Content.reviews_dateAdded]* には、 **[日付/時刻]** を選択します。

   ![データ型を変更する](media/knowledge-store-connect-power-bi/powerbi-change-type.png "データ型を変更する")

1. *[hotelReviewsSsPages]* を選択し、手順 9. と手順 10. を繰り返して列を削除し、 *[コンテンツ]* を展開します。

1. *[hotelReviewsSsKeyPhrases]* を選択し、手順 9. と手順 10. を繰り返して列を削除し、 *[コンテンツ]* を展開します。 このテーブルについては、データ型を変更しません。

1. コマンド バーで、 **[閉じて適用する]** をクリックします。

## <a name="check-table-relationships"></a>テーブルのリレーションシップを確認する

1. 左側のナビゲーション ウィンドウで [モデル] タイルをクリックし、3 つすべてのテーブル間のリレーションシップが Power BI によって表示されていることを確認します。

   ![リレーションシップを検証する](media/knowledge-store-connect-power-bi/powerbi-relationships.png "リレーションシップを検証する")

1. それぞれのリレーションシップをダブルクリックし、 **[クロスフィルターの方向]** が **[両方]** に設定されていることを確認します。  これにより、フィルターを適用したときにビジュアルが更新されます。

## <a name="build-a-report"></a>レポートを作成する

1. 左側のナビゲーション ペインで [レポート] タイルをクリックし、視覚エフェクトを使用してデータを探索します。 テキスト フィールドの場合、テーブルとカードは便利な視覚エフェクトです。

1. 3 つのテーブルそれぞれでフィールドを選択して、テーブルまたはカードに入力します。

   ![テーブル レポートを作成する](media/knowledge-store-connect-power-bi/power-bi-table-report.png "テーブル レポートを作成する")

## <a name="sample-power-bi-template---azure-portal-only"></a>サンプル Power BI テンプレート - Azure portal のみ

[Azure portal を使用してナレッジ ストア](knowledge-store-create-portal.md)を作成するときは、**データのインポート** ウィザードの 2 ページ目で、[Power BI テンプレート](https://github.com/Azure-Samples/cognitive-search-templates)をダウンロードすることもできます。 このテンプレートでは、WordCloud や Network Navigator など、テキスト ベースのコンテンツ用の視覚エフェクトがいくつか提供されます。 

**[認知技術を追加します]** ページの **[Power BI テンプレートを取得する]** をクリックして、そのパブリックな GitHub の場所からテンプレートを取得してダウンロードします。 ウィザードでは、ウィザードで指定されたナレッジ ストアの予測でキャプチャされたデータの構造に合わせてテンプレートが変更されます。 このため、データ入力とスキルの選択が異なるとすると、ダウンロードしたテンプレートは、ウィザードを実行するたびに変化します。

![サンプル Azure Cognitive Search Power BI テンプレート](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "サンプル Power BI テンプレート")

> [!NOTE]
> このテンプレートは、ウィザードの実行途中でダウンロードされます。 ナレッジ ストアが Azure Table Storage に実際に作成されるまで待ってから、使用する必要があります。

## <a name="video-introduction"></a>ビデオの紹介

ナレッジ ストアで Power BI を使用する方法のデモについては、次のビデオをご覧ください。

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Power BI のレポートとダッシュボードのテーブル](/power-bi/visuals/power-bi-visualization-tables)