---
title: ナレッジ ストアの概念
titleSuffix: Azure Cognitive Search
description: エンリッチメントされたドキュメントを Azure Storage に送信し、そこで Azure Cognitive Search および他のアプリケーション内のエンリッチメントされたドキュメントを表示、整形、および使用することができます。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: baa4a78e23b83fa7c138e71b92dba12ba23a3ab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736316"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Azure Cognitive Search のナレッジ ストア

ナレッジ ストアは、Azure Cognitive Search の機能です。[AI エンリッチメント パイプライン](cognitive-search-concept-intro.md)からの出力を、独立した分析またはダウンストリーム処理に使用できるよう Azure Storage に永続化するものです。 "*エンリッチメントされたドキュメント*" とは、AI プロセスを使用して抽出、構造化、および分析されたコンテンツから作成される、パイプラインの出力のことです。 標準的な AI パイプラインでは、エンリッチメントされたドキュメントは一時的なものであり、インデックス作成時にのみ使用され、その後破棄されます。 ナレッジ ストアを作成すると、検査や他のナレッジ マイニング シナリオのために、エンリッチメントされたドキュメントが保持されます。

過去にコグニティブ スキルを使用したことがある方であれば、"*スキルセット*" によって、ドキュメントが一連のエンリッチメントを通じて移動されることを既にご存じと思われます。 結果は、検索インデックスまたはナレッジ ストア内のプロジェクションとなります。 2 つの出力 (検索インデックスとナレッジ ストア) は、同じパイプラインから生成され、同じ入力から派生しますが、結果としては、さまざまなアプリケーションで出力が構造化、格納、および使用されます。

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="スキルセットを使用したパイプライン" border="false":::

<!-- previous version of the architecture diagram
:::image type="content" source="media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png" alt-text="Knowledge store in pipeline diagram" border="false"::: -->

物理的には、ナレッジ ストアは [Azure Storage](../storage/common/storage-account-overview.md) です。つまり Azure Table Storage か Azure Blob Storage、またはその両方になります。 Azure Storage に接続できるすべてのツールまたはプロセスは、ナレッジ ストアのコンテンツを使用できます。

Storage Explorer で表示されるナレッジ ストアは、テーブル、オブジェクト、またはファイルの単なるコレクションです。 次の例は、データ ソースから転送されるフィールド、またはエンリッチメントによって作成されたフィールドを含む 3 つのテーブルで構成されるナレッジ ストアを示しています (「センチメント スコア」と「translated_text」を参照)。

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="エンリッチメント ツリーからのスキルの読み取りおよび書き込み" border="true":::

## <a name="benefits-of-knowledge-store"></a>ナレッジ ストアのメリット

ナレッジ ストアでは、BLOB などの非構造化および半構造化データ ファイル、分析が実行されたイメージ ファイル、または新しいフォームに整形された構造化データから収集された構造体、コンテキスト、および実際のコンテンツが提供されます。 [ステップ バイ ステップ チュートリアル](knowledge-store-create-rest.md)では、高密度の JSON ドキュメントが下部構造にパーティション分割される方法、新しい構造体に再構築される方法、または機械学習やデータ サイエンスのワークロードなどのダウンストリーム プロセスで使用できるようにする方法を直接確認できます。

AI エンリッチメント パイプラインで何を生成できるかを確認するのに便利ですが、ナレッジ ストアの真の可能性はデータを整形する機能にあります。 基本的なスキルセットから開始し、反復処理して構造のレベルを追加し、それを Azure Cognitive Search 以外のアプリ内でも使用できるように新しい構造に結合できます。

ナレッジ ストアのメリットの列挙には以下が含まれます。

+ 検索以外の[分析およびレポート作成ツール](#tools-and-apps)内でエンリッチメントされたドキュメントを使用する。 Power Query を使用した Power BI は魅力的な選択肢ですが、Azure Storage に接続できる任意のツールまたはアプリで、作成するナレッジ ストアからプルできます。

+ 手順とスキルセットの定義をデバッグ中に、AI インデックス作成パイプラインを調整する。 ナレッジ ストアによって、AI インデックス作成パイプライン内のスキルセット定義の製品が示されます。 これらの結果を使用すると、エンリッチメントがどのようになるかを正確に確認できるので、より優れたスキルセットを設計できます。 Azure Storage 内で [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) を使用して、ナレッジ ストアのコンテンツを表示できます。

+ データを新しいフォームに整形する。 リシェイプはスキルセットで体系化されますが、Azure Cognitive Search の [Shaper スキル](cognitive-search-skill-shaper.md)では、明示的な制御と複数の図形を作成する機能が提供されます。 整形により、関係を維持しながら、データの使用目的に合致したプロジェクションを定義することができます。

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>ナレッジ ストアの定義

インデクサーの実行中に、ナレッジ ストアは Azure Table Storage、Azure Blob Storage、またはその両方を使用して、[Azure Storage アカウント](../storage/common/storage-account-overview.md)に作成されます。 

Azure Storage 内のデータ構造は、スキルセット内の `knowledgeStore` 定義の `projections` 要素によって指定されます。 [プロジェクション](knowledge-store-projection-overview.md)は、テーブル、オブジェクト、またはファイルとして表現することが可能で、複数のセット (つまり *プロジェクション グループ*) を持ち、さまざまな用途向けに複数のデータ構造を作成できます。

```json
"knowledgeStore":{
   "storageConnectionString":"<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>",
   "projections":[
      {
         "tables":[ ],
         "objects":[ ],
         "files":[ ]
      }
   }
```

この構造で指定するプロジェクションの種類によって、ナレッジ ストアによって使用されるストレージの種類が決まります。

+ `tables` は、エンリッチメントされたコンテンツを Table Storage に投影します。 分析ツールへの入力のために表形式のレポート構造が必要な場合や、データ フレームとして他のデータ ストアにエクスポートする場合は、テーブル プロジェクションを定義します。 同じプロジェクション グループ内の複数の `tables` を指定して、エンリッチメントされたドキュメントのサブセットまたは断面を取得することができます。 同じプロジェクション グループ内では、テーブルのリレーションシップが保持されるため、すべてのテーブルを操作できます。

+ `objects` では、JSON ドキュメントを BLOB ストレージに投影します。 `object` の物理的表現は、エンリッチメントされたドキュメントを表す階層型の JSON 構造体です。

+ `files` では、イメージ ファイルを BLOB ストレージに投影します。 `file` は、ドキュメントから抽出され、BLOB ストレージにそのまま転送されるイメージです。

## <a name="create-a-knowledge-store"></a>ナレッジ ストアの作成

ナレッジ ストアを作成するには、ポータルまたは API を使用します。 [Azure Storage](../storage/index.yml)、[スキルセット](cognitive-search-working-with-skillsets.md)、および[インデクサー](search-indexer-overview.md)が必要になります。 インデクサーには検索インデックスが必要なので、インデックス定義も指定する必要があります。

### <a name="azure-portal"></a>[**Azure portal**](#tab/kstore-portal)

**データのインポート** ウィザードを使用して、[4 つの手順で最初のナレッジ ストアを作成します](knowledge-store-connect-power-bi.md)。 ウィザードでは、次のタスクを行います。

1. 未加工のコンテンツを提供するサポートされているデータ ソースを選択します。

1. エンリッチメントの指定: Cognitive Services リソースを添付し、スキルを選択し、ナレッジ ストアを指定します。 この手順では、Azure Storage アカウントを選択し、オブジェクト、テーブル、または両方を作成するかどうかを選択します。

1. インデックス スキーマを作成します。 これはウィザードによって要求され、ウィザードに推測してもらうことができます。

1. ウィザードを実行します。 この最後の手順で、抽出、エンリッチメント、および格納が行われます。

ウィザードを使用すると、そうでない場合はコードで処理する必要があるいくつかの追加タスクが内部的に処理されます。 整形とプロジェクションの両方 (Azure Storage 内の物理データ構造の定義) が作成されます。 ナレッジ ストアを手動で作成する場合は、コードでこれらの手順を処理する必要があります。

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

REST API バージョン `2020-06-30` を使用すると、スキルセットに追加してナレッジ ストアを作成できます。

+ [スキルセットの作成 (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [スキルセットの更新 (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

[スキルセット](cognitive-search-working-with-skillsets.md)内で `knowledgeStore` が定義されます。次に、スキルセットが[インデクサー](search-indexer-overview.md)によって呼び出されます。 エンリッチメント中に、Azure Cognitive Search によって Azure Storage アカウント内にスペースが作成され、ご利用の構成に応じて BLOB として、またはテーブルに、エンリッチメントされたドキュメントがプロジェクションされます。

コードで処理する必要があるタスク:

+ Azure Storage (テーブル、オブジェクト、ファイル) に組み込むプロジェクションを指定する
+ スキルセットに Shaper スキルを含め、プロジェクションのスキーマとコンテンツを決定する
+ 名前付き図形をプロジェクションに割り当てる

探索する簡単な方法は、[Postman を使用して最初のナレッジ ストアを作成する](knowledge-store-create-rest.md)ことです。

### <a name="net-sdk"></a>[ **.NET SDK**](#tab/kstore-dotnet)

.NET 開発者の場合、Azure.Search.Documents クライアント ライブラリ内の [KnowledgeStore クラス](/dotnet/api/azure.search.documents.indexes.models.knowledgestore)を使用します。

---

<a name="tools-and-apps"></a>

## <a name="connect-with-apps"></a>アプリに接続する

エンリッチメントがストレージ内に存在した後、Azure Blob または Table Storage に接続する任意のツールまたはテクノロジを使用して、コンテンツを探索、分析、または使用できます。 次の一覧が開始点です。

+ エンリッチメントされたドキュメントの構造とコンテンツを表示する [Storage Explorer](knowledge-store-view-storage-explorer.md)。 これは、ナレッジ ストアのコンテンツを表示するためのベースライン ツールと考えてください。

+ レポートと分析のための [Power BI](knowledge-store-connect-power-bi.md)。 

+ さらに操作するための [Azure Data Factory](../data-factory/index.yml)。

## <a name="content-lifecycle"></a>コンテンツのライフサイクル

インデクサーとスキルセットを実行するたび、スキルセットまたは基になるソース データが変更された場合、ナレッジ ストアが更新されます。 インデクサーによって取得された変更は、エンリッチメント プロセスを通じてナレッジ ストア内のプロジェクションに反映され、投影されたデータが元のデータ ソース内のコンテンツの現在の表現になります。 

> [!Note]
> プロジェクション内のデータを編集することができますが、ソース データ内のドキュメントが更新された場合、次のパイプライン呼び出しですべての編集が上書きされます。 

### <a name="changes-in-source-data"></a>ソース データの変更

変更の追跡をサポートするデータ ソースの場合、インデクサーは新規および変更されたドキュメントを処理し、既に処理されている既存のドキュメントをバイパスします。 タイムスタンプ情報はデータ ソースによって異なりますが、BLOB コンテナーでは、インデクサーによって `lastmodified` の日付が確認され、取り込む必要がある BLOB が特定されます。

### <a name="changes-to-a-skillset"></a>スキルセットの変更

スキルセットに変更を加える場合は、[エンリッチメントされたドキュメントのキャッシュを有効にして](cognitive-search-incremental-indexing-conceptual.md)、可能な限り既存のエンリッチメントを再利用する必要があります。

増分キャッシュを使用しない場合、インデクサーは常に高いウォーター マークの順に逆戻りせずドキュメントを処理します。 BLOB の場合、インデクサーは、インデクサーの設定やスキルセットに対する変更に関係なく、`lastModified` で並べ替えた BLOB を処理します。 スキルセットを変更した場合、以前に処理されたドキュメントは、新しいスキルセットを反映するように更新されません。 スキルセットの変更後に処理されたドキュメントでは新しいスキルセットが使用され、その結果、インデックス ドキュメントには古いスキルセットと新しいスキルセットが混在します。

増分キャッシュを使用する場合、スキルセットの更新後に、インデクサーはスキルセットの変更の影響を受けないエンリッチメントを再利用します。 アップストリーム エンリッチメントは、変更されたスキルから独立して分離されたエンリッチメントと同様に、キャッシュからプルされます。

### <a name="deletions"></a>削除

インデクサーは、Azure Storage 内の構造とコンテンツを作成および更新しますが、それらを削除しません。 インデクサーまたはスキルセットが削除された場合でも、プロジェクションは引き続き存在します。 ストレージ アカウントの所有者は、不要になったプロジェクションを削除する必要があります。 

## <a name="next-steps"></a>次のステップ

ナレッジ ストアは、エンリッチメントされたドキュメントを永続化する手段として、スキルセットを設計する際に役立つほか、Azure Storage アカウントにアクセスする機能を備えた、あらゆるクライアント アプリケーションから利用する新しい構造やコンテンツを作成する際にも役立てることができます。

エンリッチメントされたドキュメントを作成する最も簡単なアプローチは、[ポータルを使用する](knowledge-store-create-portal.md)ことですが、Postman と REST API を使用する方法もあります。プログラムでオブジェクトがどのように作成され、参照されるのかについて分析情報が必要な場合には、後者の方が便利です。

> [!div class="nextstepaction"]
> [Postman と REST を使用してナレッジ ストアを作成する](knowledge-store-create-rest.md)

または、[プロジェクション](knowledge-store-projection-overview.md)について詳しく見てみましょう。 スライス、インラインの整形、リレーションシップなどの高度なプロジェクションの概念を示す例を確認するには、「[ナレッジ ストアでプロジェクションを定義する](knowledge-store-projections-examples.md)」を開始してください。
