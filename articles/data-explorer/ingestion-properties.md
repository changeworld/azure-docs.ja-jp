---
title: Azure Data Explorer のデータ インジェスト プロパティ
description: Azure Data Explorer でサポートされるさまざまなデータ インジェスト プロパティについて説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 48bce1bf03a0a4c8d81fff7ae54e0b22261b70f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80089179"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Azure Data Explorer データ インジェスト プロパティ 

データ インジェストは、データをテーブルに追加して Azure Data Explorer でのクエリに使用できるようにするプロセスです。 インジェスト コマンドの `with` キーワードの後ろにプロパティを追加します。

## <a name="ingestion-properties"></a>インジェストのプロパティ

次の表に、Azure Data Explorer でサポートされるプロパティの一覧を示し、それらについて説明し、例を示します。 

|プロパティ              |説明                                              |例                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |ソース ファイルのデータをテーブルの実際の列にマップする方法を示す文字列値。 関連するマッピングの種類を使用して `format` の値を定義します。 「[データ マッピング](/azure/kusto/management/mappings)」を参照してください。|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(非推奨: `avroMapping`、`csvMapping`、`jsonMapping`) |
|`ingestionMappingReference`|名前付きマッピング ポリシー オブジェクトを使用して、ソース ファイルのデータをテーブルの実際の列にマップする方法を示す文字列値。 関連するマッピングの種類を使用して `format` の値を定義します。 「[データ マッピング](/azure/kusto/management/mappings)」を参照してください。|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(非推奨: `avroMappingReference`、`csvMappingReference`、`jsonMappingReference`)|
|`creationTime` |取り込まれたデータ エクステントの作成時刻に使用する datetime 値 (ISO8601 文字列の形式)。 指定しない場合は、現在の値 (`now()`) が使用されます。 既定値のオーバーライドは、古いデータを取り込んで、保持ポリシーが正しく適用されるようにするときに役立ちます。|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|指定した場合、テーブルのスキーマを拡張するようにコマンドに指示するブール値 (既定値は `false`)。 このオプションは、`.append` コマンドと `.set-or-append` コマンドにのみ適用されます。 許可されるスキーマ拡張のみ、テーブルの最後に列が追加されます。|元のテーブル スキーマが `(a:string, b:int)` の場合、有効なスキーマ拡張は `(a:string, b:int, c:datetime, d:string)` になりますが、`(a:string, c:datetime)` は有効にはなりません|
|`folder` |[ingest-from-query](/azure/kusto/management/data-ingestion/ingest-from-query) コマンドの場合、テーブルに割り当てるフォルダー。 テーブルが既に存在する場合は、このプロパティによりテーブルのフォルダーがオーバーライドされます。|`with (folder="Tables/Temporary")`|
|`format` |データ形式 ([サポートされるデータ形式](ingestion-supported-formats.md)に関する記事を参照してください)。|`with (format="csv")`|
|`ingestIfNotExists`|指定した場合、同じ値を持つ、`ingest-by:` タグでタグが付けられたデータが既にテーブルにある場合、インジェストが成功しないようにする文字列値。 これにより、べき等データ インジェストが確保されます。 詳細については、[ingest-by: タグ](/azure/kusto/management/extents-overview#ingest-by-extent-tags)に関する記事を参照してください。|プロパティ `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` は、タグ `ingest-by:Part0001` を備えたデータが既に存在する場合、現在のインジェストが完了されないことを示します。 まだ存在しない場合は、(今後インジェストで同じデータが再び取り込もうとされた場合に備えて、) この新しいインジェストにこのタグが設定されている必要があります。|
|`ignoreFirstRecord` |`true` に設定されている場合、インジェストで、すべてのファイルの最初のレコードを無視することを示すブール値。 このプロパティは、ファイルの最初のレコードが列名である場合に、`CSV` および同様の形式のファイルに対して有用です。 既定では、`false` が想定されます。|`with (ignoreFirstRecord=false)`|
|`persistDetails` |指定されている場合、コマンドが詳細な結果 (成功の場合でも) を保持して、[.show operation details](/azure/kusto/management/operations#show-operation-details) コマンドでそれらを取得できるようにする必要があることを示すブール値。 既定値は `false` です。|`with (persistDetails=true)`|
|`policy_ingestiontime`|ブール値。指定されている場合、このコマンドで作成されるテーブルで [IngestionTime ポリシー](/azure/kusto/management/ingestiontimepolicy)を有効にするかどうかを記述します 既定では、 `true`です。|`with (policy_ingestiontime=false)`|
|`recreate_schema` |指定した場合、コマンドでテーブルのスキーマを再作成するかどうかを示すブール値。 このプロパティは、`.set-or-replace` コマンドに対してのみ適用されます。 両方が設定されている場合、`extend_schema` プロパティよりもこのプロパティが優先されます。|`with (recreate_schema=true)`|
|`tags`|JSON 文字列として書式設定された、取り込まれたデータに関連付けられる[タグ](/azure/kusto/management/extents-overview#extent-tagging)の一覧。 |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|インジェスト中に実行する検証を示す JSON 文字列。 さまざまなオプションの説明については、「[データ インジェスト](/azure/kusto/management/data-ingestion/)」を参照してください。| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')` (これが実際の既定のポリシーです)|
|`zipPattern`|ZIP アーカイブを持つストレージからデータを取り込むときに、このプロパティを使用します。 これは、取り込む ZIP アーカイブ内のファイルを選択するときに使用する正規表現を示す文字列値です。  アーカイブ内の他のファイルはすべて無視されます。|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>次のステップ

* [データ インジェスト](/azure/data-explorer/ingest-data-overview)の詳細を確認する
* [サポートされるデータ形式](ingestion-supported-formats.md)の詳細を確認する
