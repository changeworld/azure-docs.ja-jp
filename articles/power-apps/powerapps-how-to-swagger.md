<properties
	pageTitle="PowerApps と Logic Flows の Swagger 定義をカスタマイズする方法 | Microsoft Azure"
	description="PowerApps と Logic Flows を使用するために Swagger で必要とされるスキーマの拡張機能を確認します。"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="04/12/2016"
   ms.author="mandia"/>


# PowerApps と Logic Flows の Swagger 定義のカスタマイズ

## API の Swagger 2.0 API 定義の作成

WebAPI に Swagger を追加する方法については、[Swashbuckle][1] をご覧ください。

## スキーマの拡張機能
標準の Swagger 仕様に加え、PowerApps と Logic Flows 用のカスタム API を作成する際に使用できる Swagger 拡張機能がいくつかあります。このセクションでは、これらの拡張機能について説明します。

##### x-ms-summary
Swagger spec ファイルで定義されている `summary` フィールドがないエンティティの表示名を表す文字列。**パラメーター名**が例です。

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
	
- `operationId`: 呼び出される操作の operationId に一致する文字列。
- `parameters`: プロパティで操作に必要なパラメーターが定義されているオブジェクト。
- `value-collection`: 応答ペイロード内のオブジェクトの配列に対して評価するパス文字列。
- `value-path`: "value-collection" 内のオブジェクトのパス文字列。パラメーター値を参照します。
- `value-title`: "value-collection" 内のオブジェクトのパス文字列。値の説明を参照します。


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

この例では、Swagger は Salesforce に新しいオブジェクトを作成する `TableData_CreateItem` 操作を定義しています。

Salesforce には多数の組み込みオブジェクトがあります。ここで使用されている `x-ms-dynamic-values` は、組み込みの Salesforce オブジェクトのリストの把握に役立ちます。Salesforce は、`TableMetadata_ListTables` を呼び出してリストを取得します。

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

操作に対する入力が動的なシナリオにおいてこれは役立ちます。たとえば、SQL の場合を考えてみましょう。各テーブルのスキーマは異なります。そのため、ユーザーが特定のテーブルを選択する場合、ロジック フロー デザイナーはテーブルの構造を理解して列名を表示できるようにする必要があります。このコンテキストでは、Swagger 定義に `x-ms-dynamic-schema` が含まれている場合に、スキーマを取得するための対応する操作を呼び出します。


<!--Reference links in article-->
[1]: https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md

<!---HONumber=AcomDC_0413_2016-->