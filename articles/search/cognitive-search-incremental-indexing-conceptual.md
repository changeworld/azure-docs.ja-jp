---
title: インクリメンタル エンリッチメントの概念 (プレビュー)
titleSuffix: Azure Cognitive Search
description: AI エンリッチメント パイプラインからの中間コンテンツと増分変更を Azure Storage にキャッシュして、既存の処理済みドキュメントへの投資を維持します。 現在、この機能はパブリック プレビュー段階にあります。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/17/2021
ms.openlocfilehash: cb04bbea71588ea8d9fa1f1c4734b10e3d0b6792
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179205"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure Cognitive Search のインクリメンタル エンリッチメントとキャッシュ

> [!IMPORTANT] 
> この機能はパブリック プレビュー段階にあり、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下で提供されます。 [プレビューの REST API](/rest/api/searchservice/index-preview) ではこの機能がサポートされます。

"インクリメンタル エンリッチメント" とは、[スキルセットの実行](cognitive-search-working-with-skillsets.md)中、新しいスキルとドキュメントおよび変更されたスキルとドキュメントでのみ AI 処理が発生するよう、キャッシュされたエンリッチメントを使用することをいいます。 キャッシュには、[ドキュメント解析](search-indexer-overview.md#document-cracking)の出力に加え、ドキュメントごとの各スキルの出力が格納されます。 画像抽出と AI 処理は[課金対象のイベント](search-sku-manage-costs.md#billable-events)であるため、キャッシュを有効にすれば、AI エンリッチメントのコストが削減され、またその処理時間も短くなります。 

キャッシュを有効にすると、インデクサーによって更新内容が評価され、既にあるエンリッチメントをキャッシュから取得できるかどうかが判断されます。 ドキュメント解析フェーズから出力される画像やテキスト コンテンツに加え、編集の上流のスキル出力や、編集に対して直交するスキル出力は再利用できる可能性が高くなります。

スキルセットの更新によって示されるインクリメンタル エンリッチメントの実行後、更新された結果が再度キャッシュに書き込まれるほか、検索インデックスやナレッジ ストアにも書き込まれます。

## <a name="cache-configuration"></a>キャッシュの構成

物理的には、ご使用の Azure ストレージ アカウントの BLOB コンテナーに、インデクサーごとに 1 つキャッシュが格納されます。 それぞれのインデクサーには、使用しているコンテナーに対応する一意かつ不変のキャッシュ識別子が割り当てられます。

キャッシュは、"cache" プロパティを指定してインデクサーを実行すると作成されます。 キャッシュできるのは、エンリッチされたコンテンツのみです。 インデクサーにスキルセットが関連付けられていない場合、キャッシュは適用されません。 

次の例は、キャッシュが有効になっているインデクサーを示しています。 詳細な手順については、[エンリッチメント キャッシュの有効化](search-howto-incremental-index.md)に関するページを参照してください。 cache プロパティを追加するときの要求には、プレビュー API バージョン 2020-06-30-Preview 以降を使用することに注意してください。

```json
POST https://[search service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    {
        "name": "myIndexerName",
        "targetIndexName": "myIndex",
        "dataSourceName": "myDatasource",
        "skillsetName": "mySkillset",
        "cache" : {
            "storageConnectionString" : "<Your storage account connection string>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
    }
```

## <a name="cache-management"></a>キャッシュ管理

キャッシュのライフサイクルは、インデクサーによって管理されます。 インデクサーが削除されると、そのキャッシュも削除されます。 インデクサーの "cache" プロパティが null に設定されるか、接続文字列が変更された場合、既存のキャッシュはインデクサーの次回の実行時に削除されます。 

インクリメンタル エンリッチメントはユーザーの介入なしに変更を検出して対応するように設計されています。その一方で、特定の動作を呼び出すために使用できるパラメーターが用意されています。

+ [新しいドキュメントを優先する](#Prioritize-new-documents)
+ [スキルセットのチェックをバイパスする](#Bypass-skillset-checks)
+ [データ ソースのチェックをバイパスする](#Bypass-data-source-check)
+ [スキルセットの評価を強制的に実行する](#Force-skillset-evaluation)

<a name="Prioritize-new-documents"></a>

### <a name="prioritize-new-documents"></a>新しいドキュメントを優先する

"cache" プロパティは、"enableReprocessing" パラメーターを含んでいます。 キャッシュ内で既に表現されている受信ドキュメントの処理を制御する目的で使用されます。 true (既定値) の場合、インデクサーを再実行すると、キャッシュ内に既にあるドキュメントが再処理されます。これは、スキルの更新によってそのドキュメントが影響を受けると仮定されるためです。 

false の場合、既存のドキュメントは再処理されず、新しい受信コンテンツが既存のコンテンツよりも実質的に優先されます。 "enableReprocessing" を false に設定するのは、あくまで一時的な措置としてください。 "enableReprocessing" を true に設定しておけば、ほとんどの場合、新しいドキュメントか既存のドキュメントかにかかわらず、すべてのドキュメントが最新のスキルセット定義に従って確実に有効になります。

<a name="Bypass-skillset-checks"></a>

### <a name="bypass-skillset-evaluation"></a>スキルセットの評価をバイパスする

通常、スキルの変更とそのスキルの再処理は連動します。 ただし、スキルを変更しても再処理が発生しない場合があります (たとえば、カスタム スキルを新しい場所にデプロイした場合や、新しいアクセス キーを使用した場合)。 ほとんどの場合、これらは、スキル出力の実体そのものに実際の影響を与えない末梢的な変更です。 

スキルに対する変更が実際は皮相的なものであることがわかっている場合は、"disableCacheReprocessingChangeDetection" パラメーターを true に設定して、スキルセット評価をオーバーライドする必要があります。

1. [スキルセットの更新](/rest/api/searchservice/update-skillset)を呼び出し、スキルセット定義を変更します。
1. 要求に "disableCacheReprocessingChangeDetection=true" パラメーターを追加します。
1. 変更を送信します。

このパラメーターを設定すると、スキルセット定義に対する更新だけがコミットされ、変更が既存のキャッシュに及ぼす影響は評価されません。 プレビューの API バージョンである 2020-06-30-Preview 以降を使用してください。

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30-Preview
    {
        "disableCacheReprocessingChangeDetection" : true
    }
```

<a name="Bypass-data-source-check"></a>

### <a name="bypass-data-source-validation-checks"></a>データ ソースの検証チェックをバイパスする

ほとんどの場合、データ ソース定義を変更すると、キャッシュが無効になります。 ただし、接続文字列の変更やストレージ アカウントのキーのローテーションなど、変更によってキャッシュが無効にならないことがわかっているシナリオでは、データ ソースの更新時に "ignoreResetRequirement" パラメーターを追加します。 このパラメーターを true に設定すると、リセット条件 (結果的にすべてのオブジェクトが最初から再構築されて設定される条件) をトリガーすることなく、コミットを実行できます。

```http
PUT https://[search service].search.windows.net/datasources/[data source name]?api-version=2020-06-30-Preview
    {
        "ignoreResetRequirement" : true
    }
```

<a name="Force-skillset-evaluation"></a>

### <a name="force-skillset-evaluation"></a>スキルセットの評価を強制的に実行する

キャッシュの目的は不必要な処理を回避することにあります。ここで、インデクサーによって検出されない変更 (たとえば、カスタム スキルなどの外部コード内の変更) をスキルに加えるケースを考えてみましょう。

この場合は、[スキルのリセット](/rest/api/searchservice/preview-api/reset-skills)を使用して、特定のスキル (そのスキルの出力に依存するダウンストリームのスキルも含まれます) を強制的に再処理することができます。 この API は、無効にして再処理用にマークする必要があるスキルのリストが含まれた POST 要求を受け取ります。 スキルのリセット後、[インデクサー実行](/rest/api/searchservice/run-indexer)要求を行ってパイプライン処理を呼び出します。

## <a name="re-cache-specific-documents"></a>特定のドキュメントを再キャッシュする

[インデクサーのリセット](/rest/api/searchservice/reset-indexer)を使用すると、検索コーパス内のすべてのドキュメントが再処理されます。 再処理を必要とするドキュメントがごく少数のシナリオでは、[ドキュメントのリセット (プレビュー)](/rest/api/searchservice/preview-api/reset-documents) を使用して、特定のドキュメントを強制的に再処理します。 ドキュメントがリセットされると、インデクサーによってそのドキュメントのキャッシュが無効にされ、ドキュメントはその後データ ソースから読み取ることによって再処理されます。 詳細については、[インデクサー、スキル、ドキュメントの実行またはリセット](search-howto-run-reset-indexers.md)に関するページを参照してください。

特定のドキュメントをリセットする場合は、検索インデックスから読み取るドキュメント キーのリストを要求で指定します。 キーが外部データソースのフィールドにマップされている場合、指定する値は検索インデックスで使用されているものであることが必要です。

API の呼び出し方法に応じて、要求ではキー リストを追加、上書き、またはキューに登録します。

+ 異なるキーを使用して API を複数回呼び出すと、ドキュメント キーのリセットの一覧に新しいキーが追加されます。 

+ "overwrite" クエリ文字列パラメーターを true に設定して API を呼び出すと、リセットされるドキュメント キーの現在のリストが要求のペイロードで上書きされます。

+ API を呼び出しても、ドキュメント キーはインデクサーによって実行される処理のキューに追加されるだけです。 スケジュールに従ってまたはオンデマンドでインデクサーが次に呼び出されると、データ ソースからの他の変更よりも、リセットされたドキュメント キーの処理が優先されます。

次の例は、ドキュメントのリセット要求を示しています。

```http
POST https://[search service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
    {
        "documentKeys" : [
            "key1",
            "key2",
            "key3"
        ]
    }
```

## <a name="changes-that-invalidate-the-cache"></a>キャッシュが無効になる変更

キャッシュを有効にすると、インデクサーによってパイプライン構成の変更が評価され、再利用できるコンテンツと再処理が必要なコンテンツが特定されます。 このセクションでは、キャッシュが完全に無効になる変更を示した後、増分処理がトリガーされる変更を示します。 

無効化につながる変更とは、キャッシュ全体の有効性が失われる変更をいいます。 たとえばデータ ソースの更新は、無効化につながる変更です。 以下、インデクサー パイプラインのいずれかの構成要素に対する変更のうち、キャッシュが無効化される可能性のある変更をすべて列挙します。

+ データ ソースの種類の変更
+ データ ソース コンテナーの変更
+ データ ソースの資格情報の変更
+ データ ソースの変更検出ポリシーの変更
+ データ ソースの削除検出ポリシーの変更
+ インデクサーのフィールドのマッピングの変更
+ インデクサーのパラメーターの変更:
  + 解析モード
  + ファイル名拡張子を除外
  + ファイル名拡張子のインデックスを作成
  + サイズの大きいドキュメントのストレージ メタデータのみのインデックスを作成
  + 区切りテキストのヘッダー
  + 区切りテキストの区切り記号
  + ドキュメントのルート
  + 画像操作 (画像の抽出方法に対する変更)

## <a name="changes-that-trigger-incremental-processing"></a>増分処理がトリガーされる変更

増分処理では、対象のスキルセット定義が評価された後、再実行する必要があるスキルが特定され、ドキュメント ツリーの影響を受ける部分が選択的に更新されます。 結果的にインクリメンタル エンリッチメントが発生する変更の完全な一覧を次に示します。

+ スキルの種類を変更する (スキルの OData 型が更新された)。
+ スキルに固有のパラメーター (URL、defaults など) が更新された。
+ スキルの出力が変更 (スキルから返される出力が追加または変更) された。
+ 先祖の変更を伴うスキルの入力の変更があった (スキルのチェーンが変更された)。
+ アップストリームのスキルが無効化された (このスキルへの入力となっているスキルが更新された場合)。
+ ドキュメントの再プロジェクションを伴う更新がナレッジ ストアのプロジェクション場所に生じた。
+ ドキュメントの再プロジェクションを伴う変更がナレッジ ストアのプロジェクションに生じた。
+ インデックスに対するドキュメントの再プロジェクションを伴う変更が、インデクサーの出力フィールドのマッピングに生じた。

## <a name="apis-used-for-caching"></a>キャッシュに使用される API

REST API バージョン `2020-06-30-Preview` 以降では、インデクサーの追加のプロパティを使用して、インクリメンタル エンリッチメントが提供されます。 スキルセットとデータ ソースは、一般公開されているバージョンを使用できます。 操作の順序について詳しくは、リファレンス ドキュメントに加えて、[インクリメンタル エンリッチメント用のキャッシュの構成](search-howto-incremental-index.md)に関するページを参照してください。

+ [インデクサーの作成または更新 (api-version=2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) 

+ [スキルセットの更新 (api-version=2020-06-30)](/rest/api/searchservice/update-skillset) (要求での新しい URI パラメーター)

+ [スキルのリセット (api-version=2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ [データ ソースの更新](/rest/api/searchservice/update-data-source)。プレビュー API バージョンを指定して呼び出す場合、"ignoreResetRequirement" という名前の新しいパラメーターを利用できます。更新操作でキャッシュを無効化すべきでない場合は true に設定してください。 "ignoreResetRequirement" は慎重に使用してください。容易には検出できない不整合が意図せずデータに生じることがあります。

## <a name="next-steps"></a>次のステップ

インクリメンタル エンリッチメントは、変更の追跡をスキルセットと AI エンリッチメントに拡張する強力な機能です。 インクリメンタル エンリッチメントを使用すると、スキルセットの設計を反復処理する際に、既存の処理済みコンテンツを再利用できます。 次のステップで、インデクサーのキャッシュを有効にします。

> [!div class="nextstepaction"]
> [インクリメンタル エンリッチメントのキャッシュを有効にする](search-howto-incremental-index.md)