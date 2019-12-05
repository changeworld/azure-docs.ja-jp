---
title: Azure portal でナレッジ ストア (プレビュー) を作成する
titleSuffix: Azure Cognitive Search
description: データのインポート ウィザードを使用して、エンリッチされたコンテンツを保持するために使用されるナレッジ ストアを作成します。 他のアプリからの分析のためにナレッジ ストアに接続するか、エンリッチされたコンテンツを下流プロセスに送信します。 現在、この機能はパブリック プレビュー段階にあります。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: a8cc368b2949d9a65034ee4f989b8603dfa01027
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533958"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>クイック スタート:Azure portal で Azure Cognitive Search のナレッジ ストアを作成する

> [!IMPORTANT] 
> ナレッジ ストアは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 

ナレッジ ストアは Azure Cognitive Search の機能の 1 つであり、後続の分析や下流の処理のために認知スキル パイプラインからの出力を保持するものです。 

パイプラインでは、生のコンテンツとして画像と非構造化テキストを受け取り、Cognitive Services (画像や自然言語処理など) を介して AI を適用し、出力としてエンリッチされたコンテンツ (新しい構造と情報) を作成します。 パイプラインによって作成される物理的な成果物の 1 つは[ナレッジ ストア](knowledge-store-concept-intro.md)です。ツールを使用してこれにアクセスし、コンテンツを分析および探索することができます。

このクイックスタートでは、Azure クラウドのサービスとデータを組み合わせてナレッジ ストアを作成します。 すべての準備が整ったら、ポータルの**データ インポート** ウィザードを実行して、それらを 1 つにまとめます。 最終的な結果は、元のコンテンツと、ポータル ([Storage Explorer](knowledge-store-view-storage-explorer.md)) で表示できる AI で生成されたコンテンツです。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-services-and-load-data"></a>サービスを作成してデータを読み込む

このクイックスタートでは、Azure Cognitive Search、Azure Blob Storage、[Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) を AI に使用します。 

ワークロードは非常に小さいので、1 日に最大 20 トランザクションの処理を無料で Azure Cognitive Search から呼び出して使うことができる Cognitive Services を内部で利用しています。 提供されるサンプル データを使用する限り、Cognitive Services リソースの作成とアタッチはスキップしてかまいません。

1. [HotelReviews_Free.csv をダウンロードします](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)。 このデータは CSV ファイルに保存されたホテル レビュー データ (ソースは Kaggle.com) であり、1 つのホテルに関する 19 個の顧客フィードバックが含まれています。 

1. [Azure Storage アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)するか、ご自分の現在のサブスクリプションから[既存のアカウントを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)してください。 インポートされる生のコンテンツと最終的な結果であるナレッジ ストアの両方に Azure ストレージを使用します。

   このアカウントには、次の 2 つの要件があります。

   + リージョンは、Azure Cognitive Search と同じものを選択します。 
   
   + [StorageV2 (汎用 v2)] のアカウントの種類を選択します。 

1. Blob service ページを開き、コンテナーを作成します。  

1. **[アップロード]** をクリックします。

    ![データをアップロードする](media/knowledge-store-create-portal/upload-command-bar.png "ホテルのレビューをアップロードする")

1. 最初の手順でダウンロードした **HotelReviews-Free.csv** ファイルを選択します。

    ![Azure Blob コンテナーを作成する](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Azure Blob コンテナーを作成する")

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. Get a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

1. [Azure Cognitive Search サービスを作成](search-create-service-portal.md)するか、[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このクイック スタート用には、無料のサービスを使用できます。

これでデータ インポート ウィザードに進む準備が整いました。

## <a name="run-the-import-data-wizard"></a>データ インポート ウィザードを実行する

検索サービスの [概要] ページで、コマンド バーの **[データのインポート]** をクリックし、4 つの手順でナレッジ ストアを作成します。

  ![[データのインポート] コマンド](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する

1. **[データへの接続]** で、 **[Azure Blob Storage]** を選択し、作成したアカウントとコンテナーを選択します。 
1. **[名前]** に「`hotel-reviews-ds`」を入力します。
1. **[解析モード]** で **[Delimited text]\(区切りテキスト\)** を選択し、 **[最初の行にヘッダーが含まれています]** チェック ボックスをオンにします。 **[区切り記号文字]** がコンマ (,) になっていることを確認します。
1. 前の手順で保存したストレージ サービスの**接続文字列**を入力します。
1. **[コンテナー名]** に「`hotel-reviews`」と入力します。
1. **[次へ: Add AI enrichment (Optional)]\(AI エンリッチメントの追加 (省略可能)\)** をクリックします。

      ![データ ソース オブジェクトを作成する](media/knowledge-store-create-portal/hotel-reviews-ds.png "データ ソース オブジェクトを作成する")

1. 次のページに進みます。

### <a name="step-2-add-cognitive-skills"></a>手順 2:コグニティブ スキルを追加する

このウィザードの手順では、コグニティブ スキルのエンリッチメントを含んだスキルセットを作成します。 このサンプルで使用するスキルは、キー フレーズを抽出し、言語とセンチメントを検出するものです。 これらのエンリッチメントは、後の手順でナレッジ ストアに Azure テーブルとして "投影" されます。

1. **[Cognitive Services をアタッチする]** を展開します。 既定では **[無料 (制限付きのエンリッチメント)]** が選択されます。 この無料リソースで許容されるトランザクションは 1 日あたり最大 20 件です。HotelReviews-Free.csv のレコード数は 19 件なので、このリソースを使用することができます。
1. **[Add cognitive skills]\(コグニティブ スキルを追加する\)** を展開します。
1. **[スキルセット名]** に「`hotel-reviews-ss`」と入力します。
1. **[ソース データ] フィールド**には **reviews_text** を選択します。
1. **[エンリッチメントの粒度レベル]** で **[ページ (5,000 文字チャンク)]** を選択します。
1. 次のコグニティブ スキルを選択します。
    + **キー フレーズを抽出する**
    + **言語を検出する**
    + **センチメントを検出する**

      ![スキルセットを作成する](media/knowledge-store-create-portal/hotel-reviews-ss.png "スキルセットを作成する")

1. **[ナレッジ ストアにエンリッチメントを保存する]** を展開します。
1. 前の手順で保存した**ストレージ アカウントの接続文字列**を入力します。
1. 次の **Azure テーブル プロジェクション**を選択します。
    + **ドキュメント**
    + **ページ**
    + **キー フレーズ**

    ![ナレッジ ストアを構成する](media/knowledge-store-create-portal/hotel-reviews-ks.png "ナレッジ ストアを構成する")

1. 次のページに進みます。

### <a name="step-3-configure-the-index"></a>手順 3:インデックスの構成

このウィザードの手順では、オプションのフルテキスト検索クエリ用のインデックスを構成します。 フィールドとデータ型を推測するためのデータ ソースが、このウィザードによってサンプリングされます。 必要なのは、目的の動作に対応した属性を選択することだけです。 たとえば **[取得可能]** 属性を選択した場合、検索サービスからフィールド値を取得することができます。これに対し、 **[検索可能]** を選択した場合、そのフィールドに対するフルテキスト検索が可能になります。

1. **[インデックス名]** に「`hotel-reviews-idx`」と入力します。
1. 属性については、次のように選択します。
    + すべてのフィールドについて **[取得可能]** を選択します。
    + 以下のフィールドで、 **[フィルター可能]** と **[ファセット可能]** をオンにします。*Sentiment*、*Language*、*Keyphrases*
    + *city*、*name*、*reviews_text*、*language*、*Keyphrases* の各フィールドについて、 **[検索可能]** を選択します。

    インデックスは、次の画像のようになります。 一覧が長いため、この画像には一部のフィールドが表示されていません。

    ![インデックスを構成する](media/knowledge-store-create-portal/hotel-reviews-idx.png "インデックスを構成する")

1. 次のページに進みます。

### <a name="step-4-configure-the-indexer"></a>手順 4:インデクサーを構成する

このウィザードの手順では、ウィザードのこれまでの手順で定義したデータ ソース、スキルセット、インデックスのまとめ役となるインデクサーを構成します。

1. **名前**には、`hotel-reviews-idxr`を入力します。
1. **[スケジュール]** では、既定値の **[Once]\(1 回\)** のままにします。
1. **[Submit]\(送信\)** をクリックしてインデクサーを実行します。 データの抽出、インデックス作成、コグニティブ スキルの適用がすべて、この手順で実行されます。

## <a name="monitor-status"></a>状態を監視する

コグニティブ スキルのインデックス作成は、一般的なテキストベースのインデックス作成よりも完了までに時間がかかります。 進行状況を追跡できるように、ウィザードの [概要] ページにインデクサーの一覧が開きます。 自己ナビゲーションを行うには、[概要] ページに移動し、 **[インデクサー]** をクリックします。

Azure portal の [通知] アクティビティ ログで、クリック可能な **[Azure Cognitive Search notification]\(Azure Cognitive Search による通知\)** 状態リンクを監視することもできます。 実行が完了するまでに数分かかる場合があります。

## <a name="next-steps"></a>次の手順

Cognitive Services を使用してデータをエンリッチし、その結果をナレッジ ストアに投影したら、エンリッチ済みのデータ セットを Storage Explorer または Power BI を使用して探索することができます。

Storage Explorer でコンテンツを表示することや、さらに Power BI を使用して視覚化による分析情報を得ることができます。

> [!div class="nextstepaction"]
> [Storage Explorer を使用したビュー](knowledge-store-view-storage-explorer.md)
> [Power BI との接続](knowledge-store-connect-power-bi.md)

> [!Tip]
> ここに示した手順をもう一度やってみたい場合や、別の AI エンリッチメントに関するチュートリアルに挑戦してみたい場合には、*hotel-reviews-idxr* インデクサーを削除してください。 インデクサーを削除すると、Cognitive Services の処理に関する 1 日あたりの無料トランザクションのカウンターがリセットされ、ゼロに戻ります。
