---
title: Power BI を使用してナレッジ ストア (プレビュー) に接続する
titleSuffix: Azure Cognitive Search
description: 分析と探索を目的に Power BI を使用して Azure Cognitive Search のナレッジ ストア (プレビュー) に接続します。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: d1e836e0f463d1d2ce2b71d689ed590239cfb607
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406591"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Power BI を使用してナレッジ ストアに接続する

> [!IMPORTANT] 
> ナレッジ ストアは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 プレビュー機能は [REST API バージョン 2019-05-06-Preview](search-api-preview.md) で提供しています。 現時点でポータルによるサポートは一部のみにとどまります。また、.NET SDK によるサポートはありません。

この記事では、Power BI Desktop アプリの Power Query を使用してナレッジ ストアに接続し、探索する方法を説明します。 テンプレートを使用してすぐに作業を開始することも、カスタム ダッシュボードを最初から作成することもできます。

+ [Azure portal でのナレッジ ストアの作成](knowledge-store-create-portal.md)に関するページか、「[REST を使用して Azure Cognitive Search のナレッジ ストアを作成する](knowledge-store-create-rest.md)」の手順に従って、このチュートリアルに使用されているサンプル ナレッジ ストアを作成します。 ナレッジ ストアの作成に使用した Azure Storage アカウントの名前とそのアクセス キー (Azure portal から入手) も必要になります。

+ [Power BI Desktop をインストール](https://powerbi.microsoft.com/downloads/)します。

## <a name="sample-power-bi-template---azure-portal-only"></a>サンプル Power BI テンプレート - Azure portal のみ

[Azure portal を使用してナレッジ ストアを作成](knowledge-store-create-portal.md)した場合は、[Azure Cognitive Search Power BI テンプレート サンプル](https://github.com/Azure-Samples/cognitive-search-templates)を使用して Power BI の視覚エフェクトを表示し、試すことができます。 このテンプレートは、**データのインポート** ウィザードの手順に従ってダウンロードすることもできます。

以降この記事の中で説明しているセットアップ手順は、サンプル テンプレートによって自動的に実行されます。 ただし、REST API を使用してナレッジ ストアを作成した場合は、テンプレートは省略し、この記事の残りのセクションを参照して、ナレッジ ストアを Power BI に接続します。 まずは、「[Power BI を使用した接続](#connect-with-power-bi)」をお読みください。

サンプル テンプレートには、WordCloud や Network Navigator など、いくつかの視覚エフェクトが含まれています。 テンプレートに含まれる一部の視覚エフェクト (Locations マップ、Entity-Graph Viewer など) では、[Azure portal でのナレッジ ストアの作成](knowledge-store-create-portal.md)に関するページで作成したサンプル ナレッジ ストアのデータが表示されません。 これは、**データのインポート** ウィザードで利用できる AI エンリッチメントの一部しか使用されていないためです。

![サンプル Azure Cognitive Search Power BI テンプレート](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "サンプル Power BI テンプレート")

## <a name="connect-with-power-bi"></a>Power BI を使用した接続

1. Power BI Desktop を起動して、 **[データの取得]** をクリックします。

1. **[データの取得]** ウィンドウで **[Azure]** を選択し、 **[Azure Table Storage]** を選択します。

1. **[接続]** をクリックします。

1. **[アカウント名または URL]** に、Azure Storage アカウントの名前を入力します (完全な URL が自動的に作成されます)。

1. メッセージが表示されたら、ストレージ アカウント キーを入力します。

1. *[hotelReviewsSsDocument]* 、 *[hotelReviewsSsKeyPhrases]* 、 *[hotelReviewsSsPages]* の各テーブルを選択します。 これらのテーブルは、ホテル レビュー サンプル データの Azure テーブル プロジェクションであり、ナレッジ ストアの作成時に選択された AI エンリッチメントを含んでいます。

1. **[読み込み]** をクリックします。

1. 上部のリボンの **[クエリを編集]** をクリックして **Power Query エディター**を開きます。

   ![Power Query を開く](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power Query を開く")

1. *[hotelReviewsSsDocument]* を選択し、 *[PartitionKey]* 、 *[RowKey]* 、 *[Timestamp]* の各列を削除します。 

   ![テーブルを編集する](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "テーブルを編集する")

1. テーブルの右上にある対立する矢印を含んだアイコンをクリックして、 *[コンテンツ]* を展開します。 列が一覧表示されたら、すべての列を選択し、"metadata" で始まる列の選択を解除します。 **[OK]** をクリックすると、選択した列が表示されます。

   ![テーブルを編集する](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "コンテンツを展開する")

1. 列の左上にある [ABC-123] アイコンをクリックして、次の列のデータ型を変更します。

   + *[content.latitude]* と *[Content.longitude]* には、 **[小数]** を選択します。
   + *[Content.reviews_date]* と *[Content.reviews_dateAdded]* には、 **[日付/時刻]** を選択します。

   ![データ型を変更する](media/knowledge-store-connect-power-bi/powerbi-change-type.png "データ型を変更する")

1. *[hotelReviewsSsPages]* を選択し、手順 9. と手順 10. を繰り返して列を削除し、 *[コンテンツ]* を展開します。
1. *[Content.SentimentScore]* のデータ型を **[小数]** に変更します。
1. *[hotelReviewsSsKeyPhrases]* を選択し、手順 9. と手順 10. を繰り返して列を削除し、 *[コンテンツ]* を展開します。 このテーブルについては、データ型を変更しません。

1. コマンド バーで、 **[閉じて適用する]** をクリックします。

1. 左側のナビゲーション ウィンドウで [モデル] タイルをクリックし、3 つすべてのテーブル間のリレーションシップが Power BI によって表示されていることを確認します。

   ![リレーションシップを検証する](media/knowledge-store-connect-power-bi/powerbi-relationships.png "リレーションシップを検証する")

1. それぞれのリレーションシップをダブルクリックし、 **[クロスフィルターの方向]** が **[両方]** に設定されていることを確認します。  これにより、フィルターを適用したときにビジュアルが更新されます。

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>クリーンアップ

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。

## <a name="next-steps"></a>次の手順

Storage Explorer を使用してこのナレッジ ストアを探索する方法については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Storage Explorer を使用した表示](knowledge-store-view-storage-explorer.md)

REST API と Postman を使用してナレッジ ストアを作成する方法については、次の記事を参照してください。  

> [!div class="nextstepaction"]
> [REST でナレッジ ストアを作成する](knowledge-store-howto.md)
