---
title: ナレッジ ストアの概念
titleSuffix: Azure Cognitive Search
description: エンリッチメントされたドキュメントを Azure Storage に送信し、そこで Azure Cognitive Search および他のアプリケーション内のエンリッチメントされたドキュメントを表示、整形、および使用することができます。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 659e625aa98a92c2730e76bd847dfdddc3c955bf
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577329"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Azure Cognitive Search のナレッジ ストア

ナレッジ ストアは、Cognitive Search [AI エンリッチメント パイプライン](cognitive-search-concept-intro.md)によって作成されたデータ シンクで、Azure Storage のテーブルおよび blob コンテナーにエンリッチされたコンテンツを格納します。これにより、ナレッジ マイニングなどの非検索シナリオで、独立した分析や下流の処理を行うことができます。

過去にコグニティブ スキルを使用したことがある場合は、"*スキルセット*" を使用して、エンティティの認識やテキストの翻訳など、アトミック変換を呼び出す一連のエンリッチメントを介してドキュメントを移動することを既にご存知でしょう。 結果は、検索インデックスまたはナレッジ ストア内のプロジェクションとなります。 2 つの出力 (検索インデックスとナレッジ ストア) は、同じパイプラインから生成された相互に排他的な製品であり、同じ入力から派生しますが、結果としては、さまざまなアプリケーションで出力が構造化、格納、および使用されます。

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="スキルセットを使用したパイプライン" border="false":::

物理的には、ナレッジ ストアは [Azure Storage](../storage/common/storage-account-overview.md) です。つまり Azure Table Storage か Azure Blob Storage、またはその両方になります。 Azure Storage に接続できるすべてのツールまたはプロセスは、ナレッジ ストアのコンテンツを使用できます。

ストレージ ブラウザーを使用して表示されるナレッジ ストアは、テーブル、オブジェクト、またはファイルの他のコレクションと似ています。 次の例は、データ ソースから転送されるフィールド、またはエンリッチメントによって作成されたフィールドを含む 3 つのテーブルで構成されるナレッジ ストアを示しています (「センチメント スコア」と「translated_text」を参照)。

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="エンリッチメント ツリーからのスキルの読み取りおよび書き込み" border="true":::

## <a name="benefits-of-knowledge-store"></a>ナレッジ ストアのメリット

ナレッジ ストアの主な利点は、コンテンツに柔軟にアクセスできることと、データを形成する機能という 2 つの点にあります。

Cognitive Search のクエリを介してアクセスする必要がある検索インデックスとは異なり、ナレッジ ストアには、Azure Storage への接続をサポートする任意のツール、アプリ、プロセスからアクセスできます。 この柔軟性によって、エンリッチメント パイプラインによって生成された、分析およびエンリッチメントされたコンテンツを消費するための新しいシナリオが開きます。

データをエンリッチする同じスキルセットを、データの形成にも使用できます。 Power BI のようなツールは、テーブルの方が適していますが、データ サイエンス ワークロードには BLOB 形式の複雑なデータ構造が必要になる場合があります。 スキルセットに [Shaper スキル](cognitive-search-skill-shaper.md)を追加すると、データのシェイプを制御できるようになります。 そして、このシェイプをテーブルや BLOB などのプロジェクションに渡すことで、データの使用目的に沿った物理的なデータ構造を作成することができます。

次のビデオでは、これらの利点の両方について説明します。

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>ナレッジ ストアの定義

ナレッジ ストアは、スキルセット定義内で定義されており、2 つのコンポーネントがあります。 

+ Azure ストレージの接続文字列

+ ナレッジ ストアがテーブル、オブジェクト、ファイルのいずれで構成されているかを決定する [**プロジェクション**](knowledge-store-projection-overview.md)。 

プロジェクション要素は配列です。 1 つのナレッジ ストア内に、テーブル、オブジェクト、ファイルの組み合わせを複数セット作成することができます。

```json
"knowledgeStore": {
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

  プロジェクションされたコンテンツは集計または正規化されません。 次のスクリーンショットは、キー フレーズで並べ替えられたテーブルを示しており、隣接する列に親ドキュメントが示されています。 インデックス作成中のデータ インジェストとは対照的に、言語分析やコンテンツの集計はありません。 複数形と大文字と小文字の違いは、一意のインスタンスと見なされます。

  :::image type="content" source="media/kstore-keyphrases-per-document.png" alt-text="テーブル内のキー フレーズとドキュメントのスクリーンショット" border="true":::

+ `objects` では、JSON ドキュメントを BLOB ストレージに投影します。 `object` の物理的表現は、エンリッチメントされたドキュメントを表す階層型の JSON 構造体です。

+ `files` では、イメージ ファイルを BLOB ストレージに投影します。 `file` は、ドキュメントから抽出され、BLOB ストレージにそのまま転送されるイメージです。 "ファイル" という名前ですが、ファイル ストレージではなく Blob Storage に表示されます。

## <a name="create-a-knowledge-store"></a>ナレッジ ストアの作成

ナレッジ ストアを作成するには、ポータルまたは API を使用します。 [Azure Storage](../storage/index.yml)、[スキルセット](cognitive-search-working-with-skillsets.md)、および[インデクサー](search-indexer-overview.md)が必要になります。 インデクサーには検索インデックスが必要なので、インデックス定義も指定する必要があります。

完成したナレッジ ストアへの最短ルートとしては、ポータル アプローチを採用してください。 または、オブジェクトがどのように定義され、関連しているかをより深く理解するには、REST API を選択します。

### <a name="azure-portal"></a>[**Azure portal**](#tab/kstore-portal)

**データのインポート** ウィザードを使用して、[**4 つの手順で最初のナレッジ ストアを作成します**](knowledge-store-create-portal.md)。

1. データ ソースを定義する

1. スキルセットを定義し、ナレッジ ストアを指定します。

1. インデックス スキーマを定義します。 これはウィザードによって要求され、ウィザードに推測してもらうことができます。

1. ウィザードを実行します。 この最後の手順で、抽出、エンリッチメント、および格納が行われます。

このウィザードを使用すると、手動で処理する必要があるタスクを自動化できます。 具体的には、整形とプロジェクションの両方 (Azure Storage 内の物理データ構造の定義) が作成されます。 

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

[**Postman を使用した初めてのナレッジ ストアの作成**](knowledge-store-create-rest.md)に関する記事は、この [ナレッジ ストアのコレクション](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store)に属するオブジェクトと要求について説明するチュートリアルです。

REST API バージョン `2020-06-30` を使用すると、スキルセットに追加してナレッジ ストアを作成できます。

+ [スキルセットの作成 (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [スキルセットの更新 (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

スキルセット内:

+ Azure Storage (テーブル、オブジェクト、ファイル) に組み込むプロジェクションを指定する
+ スキルセットに Shaper スキルを含め、プロジェクションのスキーマとコンテンツを決定する
+ 名前付き図形をプロジェクションに割り当てる

### <a name="net-sdk"></a>[ **.NET SDK**](#tab/kstore-dotnet)

.NET 開発者の場合、Azure.Search.Documents クライアント ライブラリ内の [KnowledgeStore クラス](/dotnet/api/azure.search.documents.indexes.models.knowledgestore)を使用します。

---

<a name="tools-and-apps"></a>

## <a name="connect-with-apps"></a>アプリに接続する

エンリッチメントがストレージ内に存在した後、Azure Blob または Table Storage に接続する任意のツールまたはテクノロジを使用して、コンテンツを探索、分析、または使用できます。 次の一覧が開始点です。

+ エンリッチメントされたドキュメントの構造とコンテンツを表示する[ストレージ ブラウザー](knowledge-store-view-storage-explorer.md)。 これは、ナレッジ ストアのコンテンツを表示するためのベースライン ツールと考えてください。

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
