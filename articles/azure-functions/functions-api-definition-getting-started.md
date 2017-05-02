---
title: "Azure Functions での OpenAPI メタデータの概要 | Microsoft Docs"
description: "Azure Functions での OpenAPI サポートの概要"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: ad82c1a552d9d77259c44b938975eebc44933b86
ms.lasthandoff: 04/26/2017


---
# <a name="creating-openapi-20-swagger-metadata-for-a-function-app-preview"></a>Function App のための OpenAPI 2.0 (Swagger) メタデータの作成 (プレビュー)

このドキュメントでは、Azure Functions でホストされる単純な API のための OpenAPI 定義を作成する段階的なプロセスについて説明します。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-swagger"></a>OpenAPI (Swagger) とは
[Swagger メタデータ](http://swagger.io/)は、API の機能と動作モードを定義し、REST API をホストしている機能がさまざまなその他のソフトウェアによって使用できるようにするファイルです。 PowerApps や [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier) などのマイクロソフト製品だけでなく、[Postman](https://www.getpostman.com/docs/importing_swagger) や[他の多くのパッケージ](http://swagger.io/tools/)のようなサード パーティ開発者ツールでも、Swagger 定義によって API を使用できるようになります。

## <a name="prepare-function"></a>単純な API を使用した関数の作成
  OpenAPI 定義を作成するには、まず単純な API を使用した関数を作成する必要があります。 既に Function App でホストされている API がある場合は、次のセクションに直接進むことができます
1. 新しい Function App を作成します。
  1. [[Azure Portal]](https://portal.azure.com) >  `+ New` >「Function App」を検索します
1. 新しい Function App 内で新しい HTTP トリガー関数を作成します
  1. 関数には、非常に単純な REST API を定義するコードがあらかじめ設定されています。
  1. クエリ パラメーターとしてあるいは本文内で、関数に渡す任意の文字列は、「Hello {input}」として返されます
1. 新しい HTTP トリガー関数の `Integrate` タブに移動します
  1. `Allowed HTTP methods` を `Selected methods` に切り替えます
  1. `Selected HTTP methods` で、POST 以外のすべての動詞をオフにします。
    ![選択されている HTTP メソッド](./media/functions-api-definition-getting-started/selectedHTTPmethods.png)
  1. この手順により、後の API 定義が簡略化されます。

## <a name="enable"></a>API 定義のサポートの有効化
1. `your function name` > `API Definition (preview)` に移動します
1. `API Definition Source` を `Function` に設定します
  1. この手順により、Function App のドメインから OpenAPI ファイルをホストするエンドポイント、[OpenAPI エディター](http://editor.swagger.io)のインライン コピー、クイック スタート定義ジェネレーターなどの、Function App のための一連の OpenAPI オプションが有効になります。
![有効な定義](./media/functions-api-definition-getting-started/enabledefinition.png)

## <a name="create-definition"></a>テンプレートからの API 定義の作成
1. [`Generate API Definition template`] をクリックします。
  1. この手順では、Function App をスキャンして HTTP トリガー関数を探し、functions.json 内の情報を使用して OpenAPI ドキュメントを生成します。
2. 操作オブジェクトを `paths: /api/yourfunctionroute post:` に追加します
  1. クイック スタート OpenAPI ドキュメントは完全な OpenAPI ドキュメントの概要を示します。 完全な OpenAPI 定義となるためには、操作オブジェクトや応答用テンプレートなどのさらに多くのメタデータが必要です。
  1. 以下のサンプル操作オブジェクトは、produces/consumes セクション、パラメーター オブジェクト、および応答用オブジェクトが入力されています。
  
  ```yaml
      post:
        operationId: /api/yourfunctionroute/post
        consumes: [application/json]
        produces: [application/json]
        parameters:
          - name: name
            in: body
            description: Your name
            x-ms-summary: Your name
            required: true
            schema:
              type: object
              properties:
                name:
                  type: string
        description: >-
          Replace with Operation Object
          #http://swagger.io/specification/#operationObject
        responses:
          '200':
            description: A Greeting
            x-ms-summary: Your name
            schema:
              type: string
        security:
          - apikeyQuery: []
  ```

> [!NOTE]
>  x-ms-summary は、Logic Apps、Flow、および PowerApps の表示名を示します。
>
> 詳細については、「[Customize your Swagger definition for PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)」を参照してください。

3. [`save`] をクリックして変更内容を保存します![テンプレート定義の追加](./media/functions-api-definition-getting-started/addingtemplate.png)

## <a name="use-definition"></a>API 定義の使用
1. `API definition URL` をコピーして新しいブラウザー タブに貼り付け、未加工の OpenAPI ドキュメントを表示します。
1. 使用する OpenAPI ドキュメントを、その URL を使用したテストおよび統合のための任意の数のツールにインポートできます。
  1. 多くの Azure リソースは、Function App 設定に保存されている API 定義 URL を使用して、OpenAPI ドキュメントを自動的にインポートできます。 `Function` API 定義ソースの一部として、その URL が更新されています。


## <a name="test-definition"></a>API 定義をテストするための Swagger UI の使用
組み込み型の API 定義エディターの UI のビューにはテスト ツールが組み込まれています。 API キーを追加し、各メソッドの下にある [`Try this operation`] ボタンを使用します。 ツールは API 定義を使用して要求の書式を設定し、応答が正常な場合は定義が正しいことを示します。

### <a name="steps"></a>手順:

1. 関数 API キーをコピーします
  1. API キーは`function name`  >  `Keys`  >  `Function Keys` 
  ![[Function key](関数キー)](./media/functions-api-definition-getting-started/functionkey.png) の HTTP トリガー関数から見つけることができます
1. [`API Definition (preview)`] ページに移動します。
  1. [`Authenticate`] をクリックして、上部にあるセキュリティ オブジェクトに関数 API キーを追加します。
  ![OpenAPI キー](./media/functions-api-definition-getting-started/definitionTest auth.png)
1. `/api/yourfunctionroute` > `POST` を選択します
1. [`Try it out`] をクリックしてテストする名前を入力します
1. `Pretty`
の下に成功の結果が表示されます。![API 定義テスト](./media/functions-api-definition-getting-started/definitionTest.png)

## <a name="next-steps"></a>次のステップ
* [関数の OpenAPI 定義の概要](functions-api-definition.md)
  * OpenAPI サポートに関する詳細な情報の完全なドキュメントを読みます。
* [Azure Functions 開発者向けリファレンス](functions-reference.md)  
  * 関数のコーディングとトリガーおよびバインドの定義に関するプログラマ向けリファレンスです。
* [Azure Functions GitHub リポジトリ](https://github.com/Azure/Azure-Functions/)
  * API 定義のサポート プレビューについてフィードバックを提供するには、Functions Github を参照してください。 更新が必要な内容については、Github の問題を作成してください。

