<properties
pageTitle="Logic Apps での Salesforce コネクタの使用方法 | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。Salesforce コネクタは、Salesforce オブジェクトを操作するための API を提供します。"
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/22/2016"
ms.author="deonhe"/>

# Salesforce コネクタの使用

Salesforce コネクタは、Salesforce オブジェクトを操作するための API を提供します。

[任意のコネクタ](./apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。ロジック アプリの作成方法については、[こちら](../app-service-logic/app-service-logic-create-a-logic-app.md)をご覧ください。

## Salesforce コネクタに接続する

ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "*接続*" を作成する必要があります。[接続](./connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。

### Salesforce コネクタへの接続を作成する

>[AZURE.INCLUDE [Salesforce コネクタへの接続を作成する手順](../../includes/connectors-create-api-salesforce.md)]

## Salesforce コネクタ トリガーを使用する

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。[トリガーの詳細についてはこちらをご覧ください](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

>[AZURE.INCLUDE [Salesforce トリガーを作成する手順](../../includes/connectors-create-api-salesforce-trigger.md)]

## Add a condition 
>[AZURE.INCLUDE [Salesforce 条件を作成する手順](../../includes/connectors-create-api-salesforce-condition.md)]

## Salesforce コネクタ アクションを使用する

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。[アクションの詳細についてはこちらをご覧ください](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

>[AZURE.INCLUDE [Salesforce アクションを作成する手順](../../includes/connectors-create-api-salesforce-action.md)]

## 技術的な詳細

ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## Salesforce コネクタ トリガー

Salesforce コネクタには、次のトリガーがあります。

|トリガー | 説明|
|--- | ---|
|[オブジェクトの作成時](connectors-create-api-salesforceconnector.md#when-an-object-is-created)|この操作では、オブジェクトが作成されたときにフローをトリガーします。|
|[オブジェクトの変更時](connectors-create-api-salesforceconnector.md#when-an-object-is-modified)|この操作では、オブジェクトが変更されたときにフローをトリガーします。|


## Salesforce コネクタ アクション

Salesforce コネクタには、次のアクションがあります。


|アクション|説明|
|--- | ---|
|[複数のオブジェクトを取得する](connectors-create-api-salesforceconnector.md#get-objects)|この操作では、"潜在顧客" のような特定の種類のオブジェクトを取得します。|
|[オブジェクトを作成する](connectors-create-api-salesforceconnector.md#create-object)|この操作では、オブジェクトを作成します。|
|[オブジェクトを取得する](connectors-create-api-salesforceconnector.md#get-object)|この操作では、オブジェクトを取得します。|
|[オブジェクトを削除する](connectors-create-api-salesforceconnector.md#delete-object)|この操作では、オブジェクトを削除します。|
|[オブジェクトを更新する](connectors-create-api-salesforceconnector.md#update-object)|この操作では、オブジェクトを更新します。|
|[オブジェクトの種類を取得する](connectors-create-api-salesforceconnector.md#get-object-types)|この操作では、使用可能なオブジェクトの種類を一覧表示します。|
### アクションの詳細

ここでは、このコネクタのアクションとトリガー、および応答について詳しく説明します。



### 複数のオブジェクトを取得する
この操作では、"潜在顧客" のような特定の種類のオブジェクトを取得します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|table*|オブジェクトの種類|Salesforce オブジェクトの種類 (例: "潜在顧客")|
|$filter|Filter Query (フィルター クエリ)|エントリ数を制限する ODATA filter クエリ|
|$orderby|Order By (並べ替え)|エントリの順序を指定する ODATA orderBy クエリ|
|$skip|Skip Count (スキップ数)|スキップするエントリの数 (既定値 = 0)|
|$top|Maximum Get Count (最大取得数)|取得するエントリの最大数 (既定値 = 256)|

* は、必須のプロパティを示します。

#### 出力の詳細

ItemsList


| プロパティ名 | データ型 |
|---|---|
|値|array|




### オブジェクトを作成する
この操作では、オブジェクトを作成します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|table*|オブジェクトの種類|オブジェクトの種類 (例: "潜在顧客")|
|item*|オブジェクト|作成するオブジェクト|

* は、必須のプロパティを示します。

#### 出力の詳細

項目


| プロパティ名 | データ型 |
|---|---|
|ItemInternalId|string|




### オブジェクトを取得する
この操作では、オブジェクトを取得します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|table*|オブジェクトの種類|Salesforce オブジェクトの種類 (例: "潜在顧客")|
|id*|オブジェクト ID|取得するオブジェクトの識別子|

* は、必須のプロパティを示します。

#### 出力の詳細

項目


| プロパティ名 | データ型 |
|---|---|
|ItemInternalId|string|




### オブジェクトを削除する
この操作では、オブジェクトを削除します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|table*|オブジェクトの種類|オブジェクトの種類 (例: "潜在顧客")|
|id*|オブジェクト ID|削除するオブジェクトの識別子|

* は、必須のプロパティを示します。




### オブジェクトを更新する
この操作では、オブジェクトを更新します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|table*|オブジェクトの種類|オブジェクトの種類 (例: "潜在顧客")|
|id*|オブジェクト ID|更新するオブジェクトの識別子|
|item*|オブジェクト|変更されたプロパティがあるオブジェクト|

* は、必須のプロパティを示します。

#### 出力の詳細

項目


| プロパティ名 | データ型 |
|---|---|
|ItemInternalId|string|




### オブジェクトの作成時
この操作では、オブジェクトが作成されたときにフローをトリガーします。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|table*|オブジェクトの種類|オブジェクトの種類 (例: "潜在顧客")|
|$filter|Filter Query (フィルター クエリ)|エントリ数を制限する ODATA filter クエリ|
|$orderby|Order By (並べ替え)|エントリの順序を指定する ODATA orderBy クエリ|
|$skip|Skip Count (スキップ数)|スキップするエントリの数 (既定値 = 0)|
|$top|Maximum Get Count (最大取得数)|取得するエントリの最大数 (既定値 = 256)|

* は、必須のプロパティを示します。

#### 出力の詳細

ItemsList


| プロパティ名 | データ型 |
|---|---|
|値|array|




### オブジェクトの変更時
この操作では、オブジェクトが変更されたときにフローをトリガーします。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|table*|オブジェクトの種類|オブジェクトの種類 (例: "潜在顧客")|
|$filter|Filter Query (フィルター クエリ)|エントリ数を制限する ODATA filter クエリ|
|$orderby|Order By (並べ替え)|エントリの順序を指定する ODATA orderBy クエリ|
|$skip|Skip Count (スキップ数)|スキップするエントリの数 (既定値 = 0)|
|$top|Maximum Get Count (最大取得数)|取得するエントリの最大数 (既定値 = 256)|

* は、必須のプロパティを示します。

#### 出力の詳細

ItemsList


| プロパティ名 | データ型 |
|---|---|
|値|array|




### オブジェクトの種類を取得する
この操作では、使用可能なオブジェクトの種類を一覧表示します。


この呼び出しには、パラメーターはありません

#### 出力の詳細

TablesList


| プロパティ名 | データ型 | 
|---|---|
|値|array|



## HTTP 応答

上記のアクションとトリガーは、次の HTTP 状態コードを 1 つ以上返す場合があります。

|名前|説明|
|---|---|
|200|OK|
|202|承認済み|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました。|
|default|操作に失敗しました。|






## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
