---
title: 'クイックスタート: OCR と画像分析'
titleSuffix: Azure Cognitive Search
description: データ インポート ウィザードと AI コグニティブ スキルを使用して、OCR と画像分析を適用し、画像ファイルから検索可能なテキストを識別して抽出します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/07/2021
ms.openlocfilehash: 68fc76ab80320a41cd9c9f0884dec8dc7771bccc
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065276"
---
# <a name="quickstart-apply-ocr-and-image-analysis-using-the-import-data-wizard"></a>クイックスタート: データ インポート ウィザードを使用して OCR と画像分析を適用する

画像ファイルから検索可能なコンテンツを作成するために、Azure Cognitive Search の AI エンリッチメントによって、光学式文字認識 (OCR) と画像分析がどのように追加されるかについて説明します。 

このクイックスタートでは、**データ インポート** ウィザードを実行して、JPG ファイルのビジュアル コンテンツを分析します。 コンテンツは、標識の写真で構成されています。 出力されるのは、OCR で識別されたキャプション、タグ、テキストを含む検索可能なインデックスです。これらはすべて、[Search エクスプローラー](search-explorer.md)を使用して、portal でクエリ可能です。 

準備として、ウィザードを実行する前に、いくつかのリソースを作成してサンプル ファイルをアップロードします。

コードから始める場合は、次のようにします。 [.NET チュートリアル](cognitive-search-tutorial-blob-dotnet.md)、[Python チュートリアル](cognitive-search-tutorial-blob-python.md)、または [REST チュートリアル](cognitive-search-tutorial-blob-dotnet.md)をお試しください。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、次の前提条件を満たしておく必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。

+ Azure Cognitive Search サービス。 [サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)します。 このクイック スタート用には、無料のサービスを使用できます。 

+ Blob Storage を持つ Azure Storage アカウント。 [ストレージ アカウントを作成する](../storage/common/storage-account-create.md?tabs=azure-portal)か、[既存のアカウントを検索します](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 

  + ウィザードでストレージ アカウントを検索して接続を設定する場合は、同じサブスクリプションを選択します。
  + 帯域幅の料金を避けるため、リージョンは、Azure Cognitive Search と同じものを選択してください。
  + StorageV2 (汎用 V2) を選択します。

> [!NOTE]
> また、このクイックスタートでは [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) を AI に使用します。 ワークロードは非常に小さいので、最大 20 トランザクションの処理を無料で使用するために Cognitive Services を内部で利用しています。 つまり、追加の Cognitive Services リソースを作成しなくても、この演習を完了できるということです。

## <a name="set-up-your-data"></a>自分のデータを設定する

次の手順では、異種コンテンツ ファイルを格納するために Azure Storage で BLOB コンテナーを設定します。

1. GitHub から[サンプル データをダウンロードします](https://github.com/Azure-Samples/azure-search-sample-data)。 データ セットは複数存在します。 このクイックスタートでは、**unsplash-images\jpg-signs** フォルダー内のファイルを使用します。

1. サンプル データを BLOB コンテナーにアップロードします。

   1. [Azure portal](https://portal.azure.com/) にサインインして、目的のストレージ アカウントを見つけます。
   1. 左側のナビゲーション ウィンドウで **[コンテナー]** を選択します。
   1. "signs" という名前の[コンテナーを作成します](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)。 既定のパブリック アクセス レベルを使用します。
   1. "signs" コンテナーで、 **[アップロード]** を選択して、ローカルの **unsplash-images\jpg-signs** フォルダーからファイルをアップロードします。

標識の写真を含む 10 個のファイルがあるはずです。

目印になる建物を格納した 2 つ目のサブフォルダーがあります。 [Cognitive Services キーをアタッチする](cognitive-search-attach-cognitive-services.md)場合は、これらのファイルを含めて、埋め込みテキストを含まない画像ファイルで画像分析がどのように動作するかを確認することもできます。 このキーは、無料枠を超えるジョブに必要です。

これでデータ インポート ウィザードに進む準備が整いました。

## <a name="run-the-import-data-wizard"></a>データ インポート ウィザードを実行する

1. Azure アカウントで [Azure Portal](https://portal.azure.com/) にサインインします。

1. [使用する検索サービスを探し](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)、[概要] ページにあるコマンド バーの **[データのインポート]** をクリックして、4 つの手順でコグニティブ エンリッチメントを設定します。

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="[データのインポート] コマンドのスクリーンショット" border="true":::

### <a name="step-1---create-a-data-source"></a>手順 1 - データ ソースを作成する

1. **[データへの接続]** で、 **[Azure Blob Storage]** を選択します。 ストレージ アカウントと作成したコンテナーへの既存の接続を選択します。 データ ソースの名前を指定し、残りの部分には既定値を使用します。 

   :::image type="content" source="media/cognitive-search-quickstart-blob/connect-to-signs.png" alt-text="Azure BLOB の構成" border="true":::

### <a name="step-2---add-cognitive-skills"></a>手順 2 - コグニティブ スキルを追加する

次に、OCR と画像分析を呼び出すように AI エンリッチメントを構成します。 

1. このクイックスタートでは、**無料** の Cognitive Services リソースを使用します。 サンプル データは 19 個のファイルで構成されているため、このクイックスタートでは、Cognitive Services のインデクサーあたり 1 日 20 件の無料トランザクションの割り当てで十分間に合います。 

   :::image type="content" source="media/cognitive-search-quickstart-blob/free-enrichments.png" alt-text="無料の Cognitive Services 処理をアタッチする" border="true":::

1. 同じページで、 **[エンリッチメントの追加]** を展開し、ツリーの選択を行います。

   OCR を有効にして、すべてのテキストを merged_content フィールドにマージします。

   [画像からタグを生成する] と [画像からキャプションを生成する] を選択します。

   :::image type="content" source="media/cognitive-search-quickstart-blob/select-ocr-image-enrichments.png" alt-text="[Cognitive Services をアタッチする] でのスキルセットのサービスの選択" border="true":::

   画像分析では、ドキュメント解析中に画像がテキストから分離されます。 "merged_content" フィールドは、AI エンリッチメント パイプラインでテキストとイメージを再度関連付けます。

### <a name="step-3---configure-the-index"></a>手順 3 - インデックスを構成する

検索可能なコンテンツがインデックスに格納され、**データ インポート** ウィザードでは通常、データをサンプリングして、スキーマを自動的に推論することができます。 この手順では、生成されたスキーマを確認し、必要であれば設定に変更を加えます。 以下は、デモのデータ セット用に作成された既定のスキーマです。

このクイックスタートでは、ウィザードによって妥当な既定値が適切に設定されます。  

+ 既定のフィールドは、既存の BLOB のプロパティに加え、エンリッチメントの出力を格納するための新しいフィールド (`text`、`layoutText`、`imageCaption` など) に基づきます。 データ型は、メタデータとデータのサンプリングから推定されます。

+ 既定のドキュメント キーは *metadata_storage_path* です (このフィールドが選択されるのは一意の値が含まれるため)。

+ これらのフィールドの既定の属性は **[取得可能]** と **[検索可能]** です。 **[検索可能]** は、フィールドのフルテキスト検索を許可します。 **[取得可能]** は、フィールド値を結果で取得できることを意味します。 これらのフィールドはスキルセットを介して作成されていることから、取得可能かつ検索可能にするのがユーザーの意図であると見なされます。

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields-ocr-images.png" alt-text="インデックスのフィールド" border="true":::

フィールドを **[取得可能]** としてマークしたからといって、このフィールドが検索結果に "*必ず*" 存在するという意味ではありません。 検索結果の構成は、対象となるフィールドを **$select** クエリ パラメーターを使用して指定することにより、厳密に制御することができます。 テキスト量の多いフィールド (`content` など) には、 **$select** パラメーターが役立ちます。アプリケーションのユーザーには扱いやすい検索結果を提供しつつ、クライアント コードでは必要なすべての情報に **[取得可能]** 属性からアクセスできます。

### <a name="step-4---configure-the-indexer"></a>手順 4 - インデクサーを構成する

インデクサーは、インデックス作成プロセスを開始する高度なリソースです。 これは、データ ソース名、ターゲット インデックス、および実行の頻度を指定します。 **データ インポート** ウィザードでは、いくつかのオブジェクトが作成されます。その中には、繰り返し実行できるインデクサーが常に含まれます。

1. **[インデクサー]** ページで、既定の名前を受け入れ、**一度だけ** 実行するスケジュール オプションをクリックすれば、即時にこれを実行できます。 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-signs.png" alt-text="インデクサーの定義" border="true":::

1. **[送信]** をクリックして、インデクサーを作成し、同時に実行します。

## <a name="monitor-status"></a>状態を監視する

コグニティブ スキルのインデックス作成は、一般的なテキストベースのインデックス作成よりも完了までに時間がかかります。 進行状況を監視するには、概要ページに移動して、ページの中央にある **[インデクサー]** タブを選択します。

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-status-signs.png" alt-text="インデクサーの状態" border="true":::

実行状態の詳細を確認するには、一覧からインデクサーを選択します。

## <a name="query-in-search-explorer"></a>Search エクスプローラーでクエリを実行する

インデックスを作成した後は、クエリを実行して結果を取得することができます。 このタスクは、ポータルから **Search エクスプローラー** を使用して行います。 

1. Search サービスのダッシュボード ページで、コマンド バーの **[Search エクスプローラー]** をクリックします。

1. 一番上にある **[インデックスの変更]** を選択して、作成したインデックスを選択します。

1. [クエリ文字列] に、インデックスのクエリを実行する検索文字列 (例: `search=sign&searchFields=imageTags&$select=text,imageCaption,imageTags&$count=true`) を入力し、 **[検索]** を選択します。

   :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-query-string-signs.png" alt-text="Search エクスプローラーのクエリ文字列" border="true":::

結果は JSON として返されます。これは、Azure BLOB から送信された大きいドキュメントでは特に、詳細で読み取りにくい場合があります。 このツールで検索するためのいくつかのヒントを次に示します。

+ `$select` を追加して、結果に含めるフィールドを指定します。
+ `searchField` を追加して、フルテキスト検索の範囲を特定のフィールドに設定します。
+ JSON 内で特定のプロパティまたは用語を検索するには、Ctrl + F キーを押します。

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-results-signs.png" alt-text="Search エクスプローラーの例" border="true":::

クエリ文字列は大文字と小文字が区別されます。"不明なフィールド" というメッセージが返された場合は、 **[フィールド]** または **[インデックス定義 (JSON)]** をチェックして名前と大文字と小文字の区別を確認してください。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[All resources]\(すべてのリソース\)** または **[Resource groups]\(リソース グループ\)** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。 

## <a name="next-steps"></a>次のステップ

Cognitive Search には、他にもデータ インポート ウィザードで実行できる組み込みのスキルがあります。 次のクイックスタートでは、エンティティの認識、言語検出、テキスト翻訳を使用します。 

> [!div class="nextstepaction"]
> [クイックスタート: データ インポート ウィザードを使用してテキストを翻訳し、エンティティを認識する](cognitive-search-quickstart-blob.md)