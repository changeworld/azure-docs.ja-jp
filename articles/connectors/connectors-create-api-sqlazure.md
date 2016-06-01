<properties
	pageTitle="ロジック アプリに SQL Azure API を追加する | Microsoft Azure"
	description="SQL Azure API と REST API パラメーターの概要"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/16/2016"
   ms.author="mandia"/>


# SQL Azure API の概要
Azure SQL Database に接続して、行の挿入、テーブルの取得など、テーブルと行を管理します。

Azure SQL Database API は、次のアプリから使用できます。

- Logic Apps (このトピックで説明)
- PowerApps (詳細な一覧については、[PowerApps 接続リスト](https://powerapps.microsoft.com/tutorials/connections-list/)を参照してください)

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

Azure SQL Database では、次のことができます。

- Azure SQL Database から取得したデータに基づいてビジネス フローを構築できます。 
- 行の取得、行の挿入などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、Azure SQL Database からデータの行を取得し、そのデータを Excel に追加できます。 

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。


## トリガーとアクション
SQL には、次のアクションがあります。トリガーはありません。

トリガー | アクション
--- | ---
なし | <ul><li>1 行を取得する</li><li>複数行を取得す</li><li>行を挿入する</li><li>行を削除する</li><li>テーブルを取得する</li><li>行を更新する</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## SQL への接続を作成する
この API をロジック アプリに追加するときに、次の値を入力します。

|プロパティ| 必須|説明|
| ---|---|---|
|SQL 接続文字列|あり|Azure SQL Database 接続文字列を入力します|

接続を作成したら、テーブル名など、SQL のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### 行を取得する 
SQL テーブルから 1 行を取得します。```GET: /datasets/default/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|SQL テーブルの名前|
|id|string|○|path|なし|取得する行の一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を取得する 
SQL テーブルから複数の行を取得します。```GET: /datasets/default/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|SQL テーブルの名前|
|$skip|integer|×|query|なし|スキップするエントリの数 (既定値 = 0)|
|$top|integer|×|query|なし|取得するエントリの最大数 (既定値 = 256)|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を挿入する 
SQL テーブルに新しい行を挿入します。```POST: /datasets/default/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|SQL テーブルの名前|
|item|ItemInternalId: string|○|body|なし|SQL の指定されたテーブルに挿入する行|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を削除する 
SQL テーブルから行を削除します。```DELETE: /datasets/default/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|SQL テーブルの名前|
|id|string|○|path|なし|削除する行の一意識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### テーブルを取得する 
SQL データベースからテーブルを取得します。```GET: /datasets/default/tables```

この呼び出しには、パラメーターはありません。

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 行を更新する 
SQL テーブルの既存の行を更新します。```PATCH: /datasets/default/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|SQL テーブルの名前|
|id|string|○|path|なし|更新する行の一意識別子|
|item|ItemInternalId: string|○|body|なし|更新された値のある行|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

## オブジェクト定義

#### DataSetsMetadata

|プロパティ名 | データ型 | 必須 |
|---|---|---|
|tabular|未定義|×|
|BLOB|未定義|×|

#### TabularDataSetsMetadata

|プロパティ名 | データ型 | 必須 |
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|
|tableDisplayName|string|×|
|tablePluralName|string|×|

#### BlobDataSetsMetadata

|プロパティ名 | データ型 |必須 |
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|

#### TableMetadata

|プロパティ名 | データ型 |必須 |
|---|---|---|
|name|string|×|
|title|string|×|
|x-ms-permission|string|×|
|schema|未定義|×|

#### DataSetsList

|プロパティ名 | データ型 |必須 |
|---|---|---|
|値|array|×|

#### DataSet

|プロパティ名 | データ型 |必須 |
|---|---|---|
|名前|string|×|
|DisplayName|string|×|

#### テーブル

|プロパティ名 | データ型 |必須 |
|---|---|---|
|名前|string|×|
|DisplayName|string|×|

#### 項目

|プロパティ名 | データ型 |必須 |
|---|---|---|
|ItemInternalId|string|×|

#### ItemsList

|プロパティ名 | データ型 |必須 |
|---|---|---|
|値|array|×|

#### TablesList

|プロパティ名 | データ型 |必須 |
|---|---|---|
|値|array|×|


## 次のステップ

[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。

<!---HONumber=AcomDC_0518_2016-->