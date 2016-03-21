<properties
	pageTitle="PowerApps 用 API の開発 | Microsoft Azure"
	description="PowerApps 用カスタム API の構築または作成"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="rajram"/>

# PowerApps 用 API の開発

PowerApps 内で使用できる独自の API を作成または開発できます。これには次の手順が含まれます。

- API の構築とデプロイ
- API のための [Swagger 2.0](http://swagger.io/) API 定義の記述

このトピックでは次の手順を示します。

## 手順 1: API の構築とデプロイ

PowerApps 用 API の構築とデプロイは API の作成と似ています。好きなプログラム言語とフレームワークを選択できます。API のホスト先を選択することもできます。[API アプリ](https://azure.microsoft.com/services/app-service/api/)と同じ PowerApps の App Service 環境にホストすることをおすすめします。

次の記事は、App Service 環境での、.Net、Java、または Node.js API の構築とデプロイについて書かれています。

- [Azure App Service での .NET の構築とデプロイ](../app-service-api-dotnet-get-started.md)
- [Azure App Service での Java API アプリの構築とデプロイ](../app-service-api-java-api-app.md)
- [Azure App Service での Node.js API アプリの構築とデプロイ](../app-service-api-nodejs-api-app.md)


## 手順 2: API のための Swagger 2.0 API 定義の記述

*手順 1* で挙げた参照先の記事のいずれかに従えば、標準的な Swagger 2.0 API 定義が API のために自動的に生成されます。PowerApps のための最適化として、次のスキーマ拡張を使用して生成された Swagger 2.0 API 定義を必要に応じてカスタマイズできます。

Swagger 2.0 API 定義の一般的なカスタマイズ方法については、「[Swashbuckle 生成の API 定義のカスタマイズ](../app-service-api-dotnet-swashbuckle-customize.md)」を参照してください。

### スキーマの拡張機能
Swashbuckle によって自動生成された Swagger だけでなく、PowerApps 用の API 作成時には追加で Swagger 拡張機能がいくつか使用可能です。このセクションではこれらの拡張機能について記述します。

##### x-ms-summary
Swagger spec ファイルで定義されている集計フィールドがないエンティティの表示名を表す文字列。**パラメーター名**が例です。

##### x-ms-visibility
この値は、エンティティがロジック フロー デザイナーに表示されているかどうかについて説明します。次の値を使用できます。

- "none" (既定値)
- "advanced"
- "internal" - ロジック フロー デザイナーでこれらの操作を表示しない

操作が「重要」としてマークされている場合は、ロジック フロー クライアントでこれらの操作が強調表示されている必要があります。

##### x-ms-trigger
ロジック フロー内でのトリガーとしてこの操作を使用できるかどうかを定義します。次のオプションがあります。
	
- "none" (既定値): 操作をトリガーとして使用できません。
- "single": 操作をトリガーとしても使用できます。
- "batched": この操作はトリガーとして使用できます。さらに、この操作はオブジェクトの JSON "array" で応答し、ロジック フローは array 内の各項目のトリガーを起動します。


##### x-ms-dynamic-values
ロジック フロー デザイナーに対して、API がこのパラメーターに対して動的に有効な値の一覧をヒントとして提供します。ロジック フロー デザイナーはこのフィールドの値によって定義されている操作を呼び出し、結果から使用可能な値を抽出することができます。ロジック フロー デザイナーはこれらの値をエンド ユーザーに対してオプションとして表示できます。

値は次のプロパティを含むオブジェクトです。
	
- operationId: 呼び出される操作の operationId に一致する文字列
- parameters: プロパティで操作に必要なパラメーターが定義されているオブジェクト
- value-collection: 応答ペイロード内でオブジェクトの配列に対して評価するパス文字列
- value-path: "value-collection" 内のオブジェクトにおけるパス文字列でパラメーター値を参照します。
- value-title: "value-collection" 内のオブジェクトにおけるパス文字列でパラメーターの説明を参照します。


例:

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

上の例では、Swagger は Salesforce に新しいオブジェクトを作成する _TableData\_CreateItem_ と呼ばれる操作を定義しています。

Salesforce にはたくさんの組み込みオブジェクトがあります。_x-ms-dynamic-values_ はここでは組み込みの Salesforce オブジェクトのリストの把握に役立ちます。取得するには、_TableMetadata\_ListTables_ を呼び出す必要があります。

##### x-ms-dynamic-schema
ロジック フロー デザイナーにとって、このパラメーターのスキーマ (または応答) がもともと動的であるというヒントとなります。このフィールドの値で定義されているように操作が呼び出され、スキーマを動的に検出します。適切な UI を表示して、ユーザーからの入力を促したり、使用可能なフィールドを表示したりできます。

例:

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

操作に対する入力が動的なシナリオにおいてこれは役立ちます。たとえば、SQL の場合を考えてみましょう。各テーブルのスキーマは異なります。そのため、ユーザーが特定のテーブルを選択する場合、ロジック フロー デザイナーはテーブルの構造を理解して列名を表示できるようにする必要があります。このコンテキストでは、Swagger 定義に _x-ms-dynamic-schema_ がある場合、スキーマの取得に対応する操作を呼び出します。

<!---HONumber=AcomDC_1203_2015-->