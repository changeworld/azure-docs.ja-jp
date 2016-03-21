<properties
pageTitle="PowerApps Enterprise またはロジック アプリに Dynamic CRM Online API を追加する | Microsoft Azure"
description="CRM Online API と REST API パラメーターの概要"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/02/2016"
ms.author="deonhe"/>

# CRM API の概要
Dynamics CRM Online に接続して、新しいレコードの作成、項目の更新などを行います。CRM Online API は、以下のツールから使用できます。

- ロジック アプリ
- PowerApps

> [AZURE.SELECTOR]
- [ロジック アプリ](../articles/connectors/create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

CRM Online では、次の操作を実行できます。

- CRM Online から取得したデータに基づいてビジネス フローを構築できます。 
- レコードの削除、エンティティの取得などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、CRM で項目を更新するときに、Office 365 を使用して電子メールを送信できます。


PowerApps Enterprise に API を追加する方法については、「[Microsoft 管理の API または IT 管理の API を登録する](../power-apps/powerapps-register-from-available-apis.md)」を参照してください。

ロジック アプリに操作を追加する方法については、「[SaaS サービスを接続する新しいロジック アプリを作成します。](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション
CRM API には、次のアクションがあります。トリガーはありません。

| トリガー | アクション|
| --- | --- |
|なし| <ul><li>新しいレコードを作成する</li><li>レコードを取得する</li><li>レコードを削除する</li><li>レコードを取得する</li><li>エンティティを取得する</li><li>項目を更新する</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## CRM Online への接続を作成する

この API をロジック アプリに追加する場合、Dynamics CRM Online にサインインする必要があります。次の手順に従って、CRM Online にサインインし、ロジック アプリの**接続**の構成を完了します。

1. ロジック アプリで、**[アクションの追加]**を選択します。 ![CRM Online の構成][13]
4. 検索ボックスに CRM を入力し、名前に CRM を含むすべてのエントリが戻されるまで検索を待機します。
5. **[Dynamics CRM Online - 新しいレコードの作成]** を選択します。
6. **[Dynamics CRM Online へのサインイン]**を選択します。  
![CRM Online の構成][14]
7. CRM Online の資格情報を入力し、アプリケーションを承認するためにサインインします。
![CRM Online の構成][15]  
8. サインインした後、その他のトリガーと必要なアクションを追加することによって完了するロジック アプリに戻ります。
9. メニュー バー上の **[保存]** を選択して、作業内容を保存します。

接続を作成したら、テーブルやデータセットなど、CRM Online のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### 新しいレコードを作成する 
エンティティに新しいレコードを作成します。```POST: /datasets/{dataset}/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし|CRM 組織 contoso.crm の一意の名前|
|テーブル|string|○|path|なし|エンティティの名前|
|item| |○|body|なし|作成するレコード|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### レコードを取得する 
 エンティティのレコードを取得します。```GET: /datasets/{dataset}/tables/{table}/items```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし|CRM 組織 contoso.crm の一意の名前|
|テーブル|string|○|path|なし|エンティティの名前|
|$skip|integer|×|query|なし|スキップするエントリの数。既定値は 0 です。|
|$top|integer|×|query|なし|取得するエントリの最大数。既定値は 100 です。|
|$filter|string|×|query|なし|エントリ数を制限する ODATA filter クエリ。|
|$orderby|string|×|query|なし|エントリの順序を指定する ODATA orderBy クエリ。|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|




### データ セットを返す 
 データ セットを返します。```GET: /datasets```

この呼び出しには、パラメーターはありません。

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### テーブル項目を取得する 
CRM エンティティに存在する特定のレコードを取得するために使用されます。```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし|CRM 組織 contoso.crm の一意の名前|
|テーブル|string|○|path|なし|エンティティの名前|
|id|string|○|path|なし|レコードの識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### 一覧から項目を削除する 
一覧から項目を削除します。```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし|CRM 組織 contoso.crm の一意の名前|
|テーブル|string|○|path|なし|エンティティの名前|
|id|string|○|path|なし|レコードの識別子|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|



### 既存のテーブル項目を修正する 
CRM エンティティの既存のレコードの一部を更新するために使用されます。```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし|CRM 組織 contoso.crm の一意の名前|
|テーブル|string|○|path|なし|エンティティの名前|
|id|string|○|path|なし|レコードの識別子|
|item| |○|body|なし|更新するレコード|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

### エンティティを取得する 
CRM インスタンスに存在するエンティティの一覧を取得するために使用されます。```GET: /datasets/{dataset}/tables```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|dataset|string|○|path|なし|CRM 組織 contoso.crm の一意の名前|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


## オブジェクト定義

#### DataSetsMetadata

|プロパティ名 | データ型 | 必須|
|---|---|---|
|tabular|未定義|×|
|BLOB|未定義|×|

#### TabularDataSetsMetadata

|プロパティ名 | データ型 |必須|
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|
|tableDisplayName|string|×|
|tablePluralName|string|×|

#### BlobDataSetsMetadata

|プロパティ名 | データ型 |必須|
|---|---|---|
|source セクション|string|×|
|displayName|string|×|
|urlEncoding|string|×|


#### TableMetadata

|プロパティ名 | データ型 |必須|
|---|---|---|
|name|string|×|
|title|string|×|
|x-ms-permission|string|×|
|schema|未定義|×|

#### DataSetsList

|プロパティ名 | データ型 |必須|
|---|---|---|
|値|array|×|

#### DataSet

|プロパティ名 | データ型 |必須|
|---|---|---|
|名前|string|×|
|DisplayName|string|×|


#### テーブル

|プロパティ名 | データ型 |必須|
|---|---|---|
|名前|string|×|
|DisplayName|string|×|

#### 項目

|プロパティ名 | データ型 |必須|
|---|---|---|
|ItemInternalId|string|×|

#### ItemsList

|プロパティ名 | データ型 |必須|
|---|---|---|
|値|array|×|


#### TablesList

|プロパティ名 | データ型 |必須|
|---|---|---|
|値|array|×|


## 次のステップ

[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)。

[API リスト](apis-list.md)に戻ります。


[9]: ./media/create-api-crmonline/aad-tenant-applications-add-appinfo.png
[10]: ./media/create-api-crmonline/aad-tenant-applications-add-app-properties.png
[12]: ./media/create-api-crmonline/contoso-aad-app-configure.png
[13]: ./media/create-api-crmonline/crmconfig1.png
[14]: ./media/create-api-crmonline/crmconfig2.png
[15]: ./media/create-api-crmonline/crmconfig3.png

<!---HONumber=AcomDC_0309_2016-->