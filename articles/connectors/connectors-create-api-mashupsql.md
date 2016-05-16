<properties
pageTitle="SQL Connector | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。SQL コネクタは、SQL Database と連携する API を提供します。"
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="04/29/2016"
ms.author="deonhe"/>

# SQL Connector コネクタの使用



SQL Connector コネクタは、次のツールから使用できます。

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flows](http://flows.microsoft.com)  

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

SQL Connector コネクタは、アクションとして使用できます。SQL Connector コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 SQL Connector コネクタでは、次のアクションやトリガーを使用できます。

### SQL Connector のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|[GetTables](connectors-create-api-sqlconnector.md#gettables)|SQL データベースからテーブルを取得します。|
|[PostItem](connectors-create-api-sqlconnector.md#postitem)|SQL テーブルに新しい行を挿入します。|
|[GetItem](connectors-create-api-sqlconnector.md#getitem)|SQL テーブルから 1 行を取得します。|
|[DeleteItem](connectors-create-api-sqlconnector.md#deleteitem)|SQL テーブルから行を削除します。|
|[PatchItem](connectors-create-api-sqlconnector.md#patchitem)|SQL テーブルの既存の行を更新します。|
### SQL Connector のトリガー
次のイベントをリッスンできます。

|トリガー | 説明|
|--- | ---|


## SQL Connector への接続の作成
SQL Connector を使用してロジック アプリを作成するには、まず**接続**を作成し、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|説明|
| ---|---|---|
|SqlConnectionString|あり|SQL 接続文字列を指定します。|
接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## SQL Connector のリファレンス
適用されるバージョン: 1.0

## GetTables
テーブルの取得: SQL データベースからテーブルを取得します。

```GET: /datasets/default/tables```

この呼び出しには、パラメーターはありません
#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## PostItem
行の挿入: SQL テーブルに新しい行を挿入します。

```POST: /datasets/default/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|SQL テーブルの名前|
|item| |○|body|なし|SQL の指定されたテーブルに挿入する行|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## GetItem
行の取得: SQL テーブルから 1 行を取得します。

```GET: /datasets/default/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|SQL テーブルの名前|
|id|string|○|path|なし|取得する行の一意識別子|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## DeleteItem
行の削除: SQL テーブルから行を削除します。

```DELETE: /datasets/default/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|SQL テーブルの名前|
|id|string|○|path|なし|削除する行の一意識別子|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## PatchItem
行の更新: SQL テーブルの既存の行を更新します。

```PATCH: /datasets/default/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テーブル|string|○|path|なし|SQL テーブルの名前|
|id|string|○|path|なし|更新する行の一意識別子|
|item| |○|body|なし|更新された値のある行|

#### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## オブジェクト定義 

### DataSetsMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|tabular|未定義|いいえ |
|BLOB|未定義|いいえ |



### TabularDataSetsMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|source セクション|string|いいえ |
|displayName|string|いいえ |
|urlEncoding|string|いいえ |
|tableDisplayName|string|いいえ |
|tablePluralName|string|いいえ |



### BlobDataSetsMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|source セクション|string|いいえ |
|displayName|string|いいえ |
|urlEncoding|string|いいえ |



### TableMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|name|string|いいえ |
|title|string|いいえ |
|x-ms-permission|string|いいえ |
|x-ms-capabilities|未定義|いいえ |
|schema|未定義|いいえ |



### TableCapabilitiesMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|sortRestrictions|未定義|いいえ |
|filterRestrictions|未定義|いいえ |
|filterFunctions|array|いいえ |



### オブジェクト


| プロパティ名 | データ型 | 必須 |
|---|---|---|



### TableSortRestrictionsMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|sortable|boolean|いいえ |
|unsortableProperties|array|いいえ |
|ascendingOnlyProperties|array|いいえ |



### TableFilterRestrictionsMetadata


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|filterable|boolean|いいえ |
|nonFilterableProperties|array|いいえ |
|requiredProperties|array|いいえ |



### DataSetsList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### DataSet


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|名前|string|いいえ |
|DisplayName|string|いいえ |



### テーブル


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|名前|string|いいえ |
|DisplayName|string|いいえ |



### 項目


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|ItemInternalId|string|いいえ |



### TablesList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |



### ItemsList


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|いいえ |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->