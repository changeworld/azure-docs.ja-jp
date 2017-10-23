---
title: "カスタム コネクタの OpenAPI 拡張機能 - Azure Logic Apps | Microsoft Docs"
description: "高度な機能を使用してカスタム コネクタの OpenAPI を拡張する"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: e8565019bd4631043485224c73c2fe60e633951c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-custom-connectors-openapi-with-advanced-functionality"></a>高度な機能を使用してカスタム コネクタの OpenAPI を拡張する

Azure Logic Apps、Microsoft Flow、または Microsoft PowerApps のカスタム コネクタを作成するには、OpenAPI 定義ファイルを用意する必要があります。このファイルは、API の操作とパラメーターについて記述された、コンピューターが読み取り可能で言語に依存しないドキュメントです。 Logic Apps および Flow 用のカスタム コネクタを作成するときに、OpenAPI の標準の機能と共に、これらの OpenAPI 拡張機能を含めることができます。

* [`summary`](#summary)
* [`x-ms-summary`](#x-ms-summary)
* [`description`](#description)
* [`x-ms-visibility`](#x-ms-visibility)
* [`x-ms-dynamic-values`](#x-ms-dynamic-values)
* [`x-ms-dynamic-schema`](#x-ms-dynamic-schema)

ここでは、これらの拡張機能の詳細について説明します。

<a name="summary"></a>

## <a name="summary"></a>summary

アクション (操作) のタイトルを指定します。 </br>
適用先: 操作 </br>
推奨: `summary` の*文の先頭文字は大文字*にします。 </br>
例: "When an event is added to calendar" または "Send an email"

![各操作の "summary"](./media/custom-connector-openapi-extensions/summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "summary": "Send an email",
    /// Other action properties here...
  }
},
```

<a name="x-ms-summary"></a>

## <a name="x-ms-summary"></a>x-ms-summary

エンティティのタイトルを指定します。 </br>
適用先: パラメーター、応答スキーマ </br>
推奨: `x-ms-summary` の*タイトルの先頭文字は大文字*にします。 </br>
例: "Calendar ID"、"Subject"、"Event Description" など

![各エンティティの "x-ms-summary"](./media/custom-connector-openapi-extensions/x-ms-summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "parameters": [ 
      {
        /// Other parameters here...
        "x-ms-summary": "Subject",
        /// Other parameters here...
      }
    ]
  }
},
```

<a name="description"></a>

## <a name="description"></a>description

操作の機能、またはエンティティの形式と関数に関する詳細な説明を指定します。 </br>
適用先: 操作、パラメーター、応答スキーマ </br>
推奨: `description` の*文の先頭文字は大文字*にします。 </br>
例: "This operation triggers when a new event is added to the calendar"、"Specify the subject of the mail" など

![各操作またはエンティティの "description"](./media/custom-connector-openapi-extensions/description.png)

``` json
"actions" {
  "Send_an_email": {
     "description": "Specify the subject of the mail",
     /// Other action properties here...
  }
},
```

<a name="visibility"></a>

## <a name="x-ms-visibility"></a>x-ms-visibility

エンティティのユーザー向けの可視性を指定します。 </br>
使用できる値: `important`、`advanced`、および `internal` </br>
適用先: 操作、パラメーター、スキーマ

* `important` の操作とパラメーターは必ずユーザーに最初に表示されます。
* `advanced` の操作とパラメーターは、追加メニューで非表示になります。
* `internal` の操作とパラメーターは、ユーザーに表示されません。

> [!NOTE] 
> `internal` および `required` のパラメーターの場合、これらのパラメーターの既定値を指定する**必要があります**。

例: **[See more]\(詳細\)** メニューや **[Show advanced options]\(詳細オプションの表示\)** メニューは非表示の `advanced` 操作およびパラメーターです。

![操作とパラメーターを表示または非表示にする "x-ms-visibility"](./media/custom-connector-openapi-extensions/x-ms-visibility.png)

``` json
"actions" {
  "Send_an_email": {
     /// Other action properties here...
     "parameters": [
         {
           "name": "Subject",
           "type": "string",
           "description": "Specify the subject of the mail",
           "x-ms-summary": "Subject",
           "x-ms-visibility": "important",
           /// Other parameter properties here...
         }
     ]
     /// Other action properties here...
  }
},
```

<a name="x-ms-dynamic-values"></a>

## <a name="x-ms-dynamic-values"></a>x-ms-dynamic-values

操作の入力パラメーターを選択できるように、ユーザー向けに設定された一覧を表示します。 </br>
適用先: パラメーター </br>
使用方法: `x-ms-dynamic-values` オブジェクトをパラメーターの定義に追加します。 例については、こちらの [OpenAPI サンプル](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json)を参照してください。

![一覧を表示する "x-ms-dynamic-values"](./media/custom-connector-openapi-extensions/x-ms-dynamic-values.png)

### <a name="properties-for-x-ms-dynamic-values"></a>x-ms-dynamic-values のプロパティ

| 名前 | 必須または省略可能 | 説明 | 
| ---- | -------------------- | ----------- | 
| **operationID** | 必須 | 一覧を設定するために呼び出す操作 | 
| **value-path** | 必須 | パラメーター値を参照する `value-collection` 内のオブジェクトのパス文字列。 </br>`value-collection` が指定されていない場合、応答は配列と評価されます。 | 
| **value-title** | 省略可能 | 値の説明を参照する `value-collection` 内のオブジェクトのパス文字列。 </br>`value-collection` が指定されていない場合、応答は配列と評価されます。 | 
| **value-collection** | 省略可能 | 応答ペイロード内のオブジェクトの配列に評価されるパス文字列。 | 
| **parameters** | 省略可能 | dynamic-values 操作を呼び出すために必要な入力パラメーターをプロパティで指定するオブジェクト。 | 
|||| 

`x-ms-dynamic-values` のプロパティを表示する例を次に示します。

``` json
"x-ms-dynamic-values": {
  "operationId": "PopulateDropdown",
  "value-path": "name",
  "value-title": "properties/displayName",
  "value-collection": "value",
  "parameters": {
     "staticParameter": "<value>",
     "dynamicParameter": {
        "parameter": "<value-to-pass-to-dynamicParameter>"
     }
  }
}
```

## <a name="example-all-the-openapi-extensions-up-to-this-point"></a>例: 現時点のすべての OpenAPI 拡張機能

``` json
"/api/lists/{listID-dynamic}": {
    "get": {
        "description": "Get items from a single list - uses dynamic values and outputs dynamic schema",
        "summary": "Gets items from the selected list",
        "operationID": "GetListItems",
        "parameters": [
           {
             "name": "listID-dynamic",
             "type": "string",
             "in": "path",
             "description": "Select the list from where you want outputs",
             "required": true,
             "x-ms-summary": "Select List",
             "x-ms-dynamic-values": {
                "operationID": "GetLists",
                "value-path": "id",
                "value-title": "name"
             }
           }
        ]
    }
}
```

<a name="x-ms-dynamic schema"></a>

## <a name="x-ms-dynamic-schema"></a>x-ms-dynamic-schema

現在のパラメーターまたは応答のスキーマが動的であることを示します。 このオブジェクトは、このフィールドの値で定義された操作を呼び出し、スキーマを動的に検出し、ユーザーの入力を収集する操作や使用可能なフィールドを表示する操作に適した UI を表示することができます。 

適用先: パラメーター、応答

使用方法: `x-ms-dynamic-schema` オブジェクトを要求のパラメーターまたは応答本文の定義に追加します。 例については、こちらの [OpenAPI サンプル](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json)を参照してください。

この例は、ユーザーがドロップダウン リストから選択した項目に基づいて、入力フォームがどのように変わるかを示します。

![動的パラメーターの "x-ms-dynamic-schema"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema.png)

この例は、ユーザーがドロップダウン リストから選択した項目に基づいて、出力がどのように変わるかを示します。 このバージョンでは、ユーザーは、"Cars" を選択します。

![項目 "Cars" を選択した場合の "x-ms-dynamic-schema-response"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output1.png)

このバージョンでは、ユーザーは "Food" を選択します。

![項目 "Food" を選択した場合の "x-ms-dynamic-schema-response"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output2.png)

### <a name="properties-for-x-ms-dynamic-schema"></a>x-ms-dynamic-schema のプロパティ

| 名前 | 必須または省略可能 | Description | 
| ---- | -------------------- | ----------- | 
| **operationID** | 必須 | スキーマを取得するために呼び出す操作 | 
| **parameters** | 必須 | dynamic-schema 操作を呼び出すために必要な入力パラメーターをプロパティで指定するオブジェクト。 | 
| **value-path** |省略可能 | スキーマがあるプロパティを参照するパス文字列。 </br>指定しない場合、応答は、ルート オブジェクトのプロパティにスキーマが含まれていると見なされます。 | 
|||| 

動的パラメーターの例を次に示します。

``` json
{
  "name": "dynamicListSchema",
  "in": "body",
  "description": "Dynamic schema for items in the selected list",
  "schema": {
    "type": "object",
    "x-ms-dynamic-schema": {
        "operationID": "GetListSchema",
        "parameters": {
          "listID": {
            "parameter": "listID-dynamic"
          }
        },
        "value-path": "items"
    }
  }
}
```

動的応答の例を次に示します。

``` json
"DynamicResponseGetListSchema": {
   "type": "object",
   "x-ms-dynamic-schema": {
      "operationID": "GetListSchema",
      "parameters": {
         "listID": {
            "parameter": "listID-dynamic"
         }
      },
      "value-path": "items"
    }
}
```

## <a name="next-steps"></a>次のステップ

* [カスタム API とコネクタの概要](../logic-apps/custom-connector-api-postman-collection.md)
* [Logic Apps: コネクタの登録](../logic-apps/logic-apps-custom-connector-register.md)
* [フロー: カスタム コネクタを登録する](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)