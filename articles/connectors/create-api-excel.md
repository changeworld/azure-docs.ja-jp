<properties
pageTitle="PowerApps Enterprise への Excel API の追加 | Microsoft Azure"
description="Excel API と REST API パラメーターの概要"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/23/2016"
ms.author="deonhe"/>

# Excel API を使ってみる

Excel に接続して行の挿入、削除などを行います。

Excel API は PowerApps Enterprise から使用できます。

Excel で次の操作を実行できます。

- Excel API を PowerApps Enterprise に追加。その後、ユーザーはアプリ内でこの API を使用できます。 

PowerApps Enterprise での API の追加方法について詳しくは、「[Register an API in PowerApps (PowerApps での API の登録)](../power-apps/powerapps-register-from-available-apis.md)」をご覧ください。

ロジック アプリでの操作の追加については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」をご覧ください。

## トリガーとアクション
Excel には次のアクションが含まれています。トリガーはありません。

|トリガー|アクション|
|--- | ---|
|なし | <ul><li>行の取得</li><li>行の挿入</li><li>行の削除</li><li>行の取得</li><li>テーブルの取得</li><li>行の更新</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## Swagger REST API リファレンス
#### このドキュメントはバージョン 1.0 を対象としています。


### Excel テーブルに新しい行を挿入します。
**```POST: /datasets/{dataset}/tables/{table}/items```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|データセット|string|○|path|なし|Excel ファイル名|
|テーブル|string|○|path|なし|Excel テーブル名|
|項目| |○|本文|なし|指定された Excel テーブルに挿入する行|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|
------



### Excel テーブルから 1 つの行を取得します。
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|データセット|string|○|path|なし|Excel ファイル名|
|テーブル|string|○|path|なし|Excel テーブル名|
|id|string|○|path|なし|取得する行の一意識別子|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|
------



### Excel テーブルから行を削除します。
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|データセット|string|○|path|なし|Excel ファイル名|
|テーブル|string|○|path|なし|Excel テーブル名|
|id|string|○|path|なし|削除する行の一意識別子。|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|
------



### Excel テーブルの既存の行を更新します。
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|データセット|string|○|path|なし|Excel ファイル名|
|テーブル|string|○|path|なし|Excel テーブル名|
|id|string|○|path|なし|更新する行の一意識別子|
|item| |○|本文|なし|更新された値のある行|


### Response

|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|
------



## オブジェクト定義

#### DataSetsMetadata

| 名前 | データ型 | 必須|
|---|---|---|
|表形式|未定義|×|
|BLOB|未定義|×|

#### TabularDataSetsMetadata

| 名前 | データ型 |必須|
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|
|tableDisplayName|string|×|
|tablePluralName|string|×|

#### BlobDataSetsMetadata

| 名前 | データ型 |必須|
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|

#### TableMetadata

| 名前 | データ型 |必須|
|---|---|---|
|name|string|×|
|title|string|×|
|x-ms-permission|string|×|
|schema|未定義|×|

#### DataSetsList

| 名前 | データ型 |必須|
|---|---|---|
|値|array|×|

#### DataSet

| 名前 | データ型 |必須|
|---|---|---|
|名前|string|×|
|DisplayName|string|×|

#### テーブル

| 名前 | データ型 |必須|
|---|---|---|
|名前|string|×|
|DisplayName|string|×|

#### 項目

| 名前 | データ型 |必須|
|---|---|---|
|ItemInternalId|string|×|

#### TablesList

| 名前 | データ型 |必須|
|---|---|---|
|値|array|×|

#### ItemsList

| 名前 | データ型 |必須|
|---|---|---|
|値|array|×|


## 次のステップ
[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md) [パワー アプリの作成](../power-apps/powerapps-get-started-azure-portal.md)

<!---HONumber=AcomDC_0224_2016-->