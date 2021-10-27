---
title: Power BI を使用してナレッジ ストアに接続する
titleSuffix: Azure Cognitive Search
description: 分析と探索を目的に Power BI を使用して Azure Cognitive Search のナレッジ ストアに接続します。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 14a6cd5ec07d9c4c5ee584312e9ba20b2251d0ef
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178305"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Power BI を使用してナレッジ ストアに接続する

この記事では、Power BI Desktop アプリの Power Query を使用してナレッジ ストアに接続し、照会する方法を説明します。 テンプレートを使用してすぐに作業を開始することも、カスタム ダッシュボードを最初から作成することもできます。

Azure Storage のテーブルで構成されるナレッジ ストアは、Power BI で最適に機能します。 テーブルに同じスキルセットとプロジェクション グループのプロジェクションが含まれている場合は、それらを簡単に "結合" して、関連テーブルのフィールドを含むテーブルの視覚化を作成できます。

サンプル データと[このポータル クイックスタートで作成された](knowledge-store-create-portal.md)ナレッジ ストアを使用して、または [Postman と REST API](knowledge-store-create-rest.md) を使用して、この記事の手順に従います。 

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

1. *hotelReviewsDocument* を開き、その *PartitionKey*、*RowKey*、*Timestamp* の各列を削除します。 それらの列は、Azure Table Storage でのテーブルのリレーションシップに使用されます。 Power BI にはそれらは必要はありません。 それぞれの "*レコード*" を示す "Content" という名前の 1 つの列が残っている必要があります。 

   ![テーブルを編集する](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "テーブルを編集する")

1. テーブルの右上にある反対向きの矢印のアイコンをクリックして、 *[コンテンツ]* を展開します。 列の一覧が表示されたら、すべての列を選択します。 "metadata" で始まる列の選択を解除します。 **[OK]** をクリックすると、選択した列が含められます。

   ![コンテンツを展開する](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "コンテンツを展開する")

1. 列の左上にある [ABC-123] アイコンをクリックして、次の列のデータ型を変更します。

   + *[content.latitude]* と *[Content.longitude]* には、 **[小数]** を選択します。
   + *[Content.reviews_date]* と *[Content.reviews_dateAdded]* には、 **[日付/時刻]** を選択します。

   ![データ型を変更する](media/knowledge-store-connect-power-bi/powerbi-change-type.png "データ型を変更する")

1. *hotelReviewsSsPages* を開き、列の削除手順を繰り返し、 *[コンテンツ]* を展開してレコードから列を選択します。 このテーブルについては、データ型を変更しません。

1. *hotelReviewsSsKeyPhrases* を開き、列の削除手順を繰り返し、 *[コンテンツ]* を展開してレコードから列を選択します。 このテーブルについては、データ型を変更しません。

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