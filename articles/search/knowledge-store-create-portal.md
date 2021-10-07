---
title: 'クイック スタート: Azure portal でナレッジ ストアを作成する'
titleSuffix: Azure Cognitive Search
description: データのインポート ウィザードを使用して、エンリッチされたコンテンツを保持するために使用されるナレッジ ストアを作成します。 他のアプリからの分析のためにナレッジ ストアに接続するか、エンリッチされたコンテンツを下流プロセスに送信します。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/02/2021
ms.openlocfilehash: f80a4a5961c0506f423da4d4f1578b8cf8999b51
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755234"
---
# <a name="quickstart-create-a-knowledge-store-in-the-azure-portal"></a>クイック スタート: Azure portal でナレッジ ストアを作成する

[ナレッジ ストア](knowledge-store-concept-intro.md)は、Azure Cognitive Search の機能です。[AI エンリッチメント パイプライン](cognitive-search-concept-intro.md)からの出力を Azure Storage に送信します。 パイプラインによって作成されたエンリッチメント、たとえば翻訳されたテキスト、OCR テキスト、認識されたエンティティ、その他のエンリッチメントがテーブルまたは BLOB に投影され、Azure Storage に接続するすべてのアプリまたはワークロードからアクセスできます。

このクイックスタートでは、データを設定し、**データのインポート** ウィザードを実行して、ナレッジ ストアも生成するエンリッチメント パイプラインを作成します。 このナレッジ ストアには、ソース (ホテルの顧客レビュー) から取得された元のテキスト コンテンツに加えて、AI によって生成されたコンテンツ (センチメント ラベル、キー フレーズ抽出、非英語圏の顧客のコメントのテキスト翻訳など) が含まれます。

> [!NOTE]
> このクイックスタートでは、Azure Storage でナレッジ ストアを非常に速く完成させる方法を紹介します。 各手順の詳細な説明については、代わりに [REST でのナレッジ ストアの作成](knowledge-store-create-rest.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートでは、次のサービスを使用します。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。

+ Azure Cognitive Search サービス。 [サービスを作成](search-create-service-portal.md)するか、アカウントで[既存のサービスを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)します。 このクイック スタート用には、無料のサービスを使用できます。 

+ [Blob Storage](../storage/blobs/index.yml) を持つ Azure Storage アカウント。

また、このクイックスタートでは [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) を AI に使用します。 ワークロードは非常に小さいので、最大 20 トランザクションの処理を無料で使用するために Cognitive Services を内部で利用しています。 つまり、追加の Cognitive Services リソースを作成しなくても、この演習を完了できるということです。

## <a name="set-up-your-data"></a>自分のデータを設定する

次の手順では、異種コンテンツ ファイルを格納するために Azure Storage で BLOB コンテナーを設定します。

1. [HotelReviews_Free.csv をダウンロードします](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)。 このデータは CSV ファイルに保存されたホテル レビュー データ (ソースは Kaggle.com) であり、1 つのホテルに関する 19 個の顧客フィードバックが含まれています。 

1. [Azure Storage アカウントを作成](../storage/common/storage-account-create.md?tabs=azure-portal)するか、[既存のアカウントを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)してください。 インポートされる生のコンテンツと最終的な結果であるナレッジ ストアの両方に Azure Storage を使用します。

   **[StorageV2 (general purpose V2)]\(StorageV2 (汎用 V2)\)** のアカウントの種類を選択します。

1. Azure Storage リソースで、**Storage Explorer** を使用して **hotel-reviews** という名前の BLOB コンテナーを作成します。

1. ページの上部にある **[アップロード]** を選択して、前の手順でダウンロードした **HotelReviews-Free.csv** ファイルを読み込みます。

   :::image type="content" source="media/knowledge-store-create-portal/blob-container-storage-explorer.png" alt-text="アップロードしたファイルと左側のナビゲーション ペインが示された Storage Explorer のスクリーンショット" border="true":::

1. このリソースでの作業はほぼ完了ですが、これらのページを離れる前に、インデクサーを使用してこのデータを取得できるように、左側のナビゲーション ペインで **[アクセス キー]** を選択して接続文字列を取得します。

1. **[アクセス キー]** で、ページの上部にある **[Show Keys]\(キーの表示\)** を選択して接続文字列を再表示し、key1 または key2 のいずれかの接続文字列をコピーします。

   接続文字列は次のような形式です。`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

これで **データのインポート** ウィザードに進む準備が整いました。

## <a name="start-the-wizard"></a>ウィザードを起動する

1. Azure アカウントで [Azure Portal](https://portal.azure.com/) にサインインします。

1. [検索サービスを探し](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)、検索サービスの [概要] ページで、コマンド バーの **[データのインポート]** をクリックして、4 つのステップでナレッジ ストアを作成します。

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="[データのインポート] コマンドのスクリーンショット" border="true":::

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する

データは、1 つの CSV ファイル内の複数行であるため、行ごとに 1 つの検索ドキュメントを取得するように *解析モード* を設定します。

1. **[データへの接続]** で、 **[Azure Blob Storage]** を選択し、作成したアカウントとコンテナーを選択します。 

1. **[名前]** に「`hotel-reviews-ds`」を入力します。

1. **[解析モード]** で **[Delimited text]\(区切りテキスト\)** を選択し、 **[最初の行にヘッダーが含まれています]** チェック ボックスをオンにします。 **[区切り記号文字]** がコンマ (,) になっていることを確認します。

1. **[接続文字列]** で、Azure Storage からコピーした接続文字列を貼り付けます。

1. **[コンテナー]** で、データを保持している BLOB コンテナーの名前 (`hotel-reviews`) を入力します。

    実際のページは次のスクリーンショットのようになります。

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ds.png" alt-text="データ ソース定義のスクリーンショット" border="true":::

1. 次のページに進みます。

### <a name="step-2-add-skills"></a>手順 2: スキルを追加する

ウィザードのこの手順では、AI エンリッチメントのスキルを追加します。 ソース データは、英語とフランス語の顧客レビューで構成されています。 このデータ セットに関連するスキルには、キー フレーズ抽出、センチメント検出、テキスト翻訳などがあります。 これらのエンリッチメントは、後の手順でナレッジ ストアに Azure テーブルとして "投影" されます。

1. **[Cognitive Services をアタッチする]** を展開します。 既定では **[無料 (制限付きのエンリッチメント)]** が選択されます。 この無料リソースで許容されるトランザクションは 1 日あたり最大 20 件です。HotelReviews-Free.csv のレコード数は 19 件なので、このリソースを使用することができます。

1. **[エンリッチメントの追加]** を展開します。

1. **[スキルセット名]** に「`hotel-reviews-ss`」と入力します。

1. **[ソース データ] フィールド** には **reviews_text** を選択します。

1. **[エンリッチメントの粒度レベル]** で **[ページ (5,000 文字チャンク)]** を選択します。

1. **[テキストの認知技術]** に、次のスキルを選択します。

    + **キー フレーズを抽出する**
    + **テキストを翻訳する**
    + **言語検出**
    + **センチメントを検出する**

   ページは次のスクリーンショットのようになります。

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ss.png" alt-text="スキルセット定義のスクリーンショット" border="true":::

1. 下にスクロールして **[Save enrichments to knowledge store]\(ナレッジ ストアにエンリッチメントを保存する\)** を展開します。

1. 次の **Azure テーブル プロジェクション** を選択します。 このウィザードでは、**ドキュメント** プロジェクションは常に提供されます。 その他のプロジェクションは、選択したスキル (**キー フレーズ** など)、またはエンリッチメントの粒度レベル (**ページ**) に応じて提供されます。

    + **ドキュメント**
    + **ページ**
    + **キー フレーズ**

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ks.png" alt-text="ナレッジ ストア定義のスクリーンショット" border="true":::

1. 前の手順で保存した **ストレージ アカウントの接続文字列** を入力します。

1. 次のページに進みます。

### <a name="step-3-configure-the-index"></a>手順 3:インデックスの構成

ウィザードのこの手順では、オプションのフルテキスト検索クエリ用のインデックスを構成します。 フィールドとデータ型を推測するためのデータ ソースが、このウィザードによってサンプリングされます。 必要なのは、目的の動作に対応した属性を選択することだけです。 たとえば **[取得可能]** 属性を選択した場合、検索サービスからフィールド値を取得することができます。これに対し、 **[検索可能]** を選択した場合、そのフィールドに対するフルテキスト検索が可能になります。

1. **[インデックス名]** に「`hotel-reviews-idx`」と入力します。

1. 以下の属性については、既定の設定をそのまま使用します。パイプラインによって作成されている新しいフィールドの **[取得可能]** と **[検索可能]** 。

    インデックスは、次の画像のようになります。 一覧が長いため、この画像には一部のフィールドが表示されていません。

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-idx.png" alt-text="インデックス定義のスクリーンショット" border="true":::

1. 次のページに進みます。

### <a name="step-4-configure-and-run-the-indexer"></a>手順 4: インデクサーを構成して実行する

ウィザードのこの手順では、ウィザードのこれまでの手順で定義したデータ ソース、スキルセット、インデックスのまとめ役となるインデクサーを構成します。

1. **名前** には、`hotel-reviews-idxr`を入力します。

1. **[スケジュール]** では、既定値の **[Once]\(1 回\)** のままにします。

1. **[送信]** を選択してインデクサーを実行します。 データの抽出、インデックス作成、コグニティブ スキルの適用がすべて、この手順で実行されます。

## <a name="check-status"></a>状態の確認

**[概要]** ページで、ページの中央にある **[インデクサー]** タブを開き、**hotels-reviews-ixr** を選択します。 1、2 分以内に、状態が "進行中" から "成功" になり、エラーと警告がゼロになります。

## <a name="check-tables-in-storage-explorer"></a>Storage Explorer でテーブルを確認する

Azure portal で Azure Storage アカウントに切り替え、**Storage Explorer** を使用して新しいテーブルを表示します。 [エンリッチメントの追加] ページの [Save enrichments]\(エンリッチメントの保存\) セクションで提供されたプロジェクションごとに、3 つのテーブルが表示されます。

+ `hotelReviewssDocument` には、ドキュメントのエンリッチメント ツリーの、コレクションではない、第 1 レベルのノードがすべて含まれます。

+ `hotelReviewssPages` には、ドキュメントから分割された各ページに対して作成されたエンリッチメントされたフィールドが含まれます。 ページ レベルのエンリッチメントは、センチメント ラベルと翻訳されたテキストで構成されます。 スキルセット定義で "ページ" 粒度を選択すると、ページ テーブル (特定の粒度レベルを指定した場合は文テーブル) が作成されます。 ページまたは文レベルで実行されるスキルには、このテーブルに投影される出力があります。

+ `hotelReviewssKeyPhrases` には、すべてのレビューから抽出されたキー フレーズの長いリストが含まれます。 キー フレーズやエンティティなど、コレクション (配列) を出力するスキルでは、出力がスタンドアロン テーブルに送信されます。

これらのテーブルすべてに、他のツールやアプリでテーブルのリレーションシップをサポートするための ID 列が含まれています。 テーブルを開いたときに、これらのフィールドの下までスクロールして、パイプラインによって追加されたコンテンツ フィールドを表示します。

このクイックスタートでは、テーブルは次のスクリーンショットのようになります。

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="Storage Explorer で生成されたテーブルのスクリーンショット" border="true":::

## <a name="clean-up"></a>クリーンアップ

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[All resources]\(すべてのリソース\)** または **[Resource groups]\(リソース グループ\)** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。

> [!TIP]
> この演習を繰り返したい場合や、別の AI エンリッチメントのチュートリアルを試したい場合は、**hotel-reviews-idxr** インデクサーと関連オブジェクトを削除し、再作成してください。 インデクサーを削除すると、1 日あたりの無料トランザクションのカウンターがリセットされ、ゼロに戻ります。

## <a name="next-steps"></a>次のステップ

ここでは、ナレッジ ストアを紹介しました。REST API のチュートリアルに進んで各手順を詳しく見ていきましょう。 ウィザードが内部的に処理したタスクについては、REST チュートリアルで説明します。

> [!div class="nextstepaction"]
> [REST と Postman を使用してナレッジ ストアを作成する](knowledge-store-create-rest.md)
