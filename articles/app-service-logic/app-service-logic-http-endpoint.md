---
title: 呼び出し可能なエンドポイントとしてのロジック アプリ
description: トリガー エンドポイントを作成および構成し、Azure App Service の Logic App で使用する方法
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/10/2016
ms.author: jehollan

---
# 呼び出し可能なエンドポイントとしてのロジック アプリ
Logic Apps はトリガーとして同期 HTTP エンドポイントをネイティブで公開できます。また、呼び出し可能なエンドポイントのパターンを使用すると、Logic App 内の "ワークフロー" アクションによって、入れ子になったワークフローとして Logic Apps を起動することもできます。

要求を受信できるトリガーは 3 種類あります。

* 要求
* ApiConnectionWebhook
* HttpWebhook

この記事の残りの部分では、例として **request** を取り上げますが、主要事項はすべて他の 2 種類のトリガーにも同様に適用されます。

## 定義へのトリガーの追加
最初の手順として、受信要求を受信できるトリガーをロジック アプリの定義に追加します。デザイナーで "HTTP 要求" を検索してトリガー カードを追加できます。要求本文の JSON スキーマを定義することができ、ワークフローを介して manual トリガーからのデータを解析したり渡したりするのに役立つトークンがデザイナーによって生成されます。サンプル本文のペイロードから JSON スキーマを生成するには、[jsonschema.net](http://jsonschema.net) などのツールを使用することをお勧めします。

![要求トリガー カード][2]

Logic App の定義を保存すると、次のようなコールバック URL が生成されます。

``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?...
```

この URL には、認証に使用されるクエリ パラメーターに SAS キーが含まれています。

Azure ポータルでこのエンドポイントを取得することもできます。

![][1]

以下を呼び出して取得することもできます。

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

## ロジック アプリ トリガーのエンドポイントの呼び出し
トリガーのエンドポイントを作成した後は、完全な URL に対する `POST` を介してそれをトリガーすることができます。追加のヘッダー、および任意のコンテンツを本文に含めることができます。

content-type が `application/json` である場合は、要求内からプロパティを参照できます。それ以外の場合は、他の API に渡すことはできても、コンテンツを変換しないとワークフロー内部で参照できない、単一バイナリ ユニットとして扱われます。たとえば、`application/xml` コンテンツを渡す場合は、`@xpath()` を使用して xpath を抽出すること、または `@json()` を使用して XML から JSON に変換することができます。コンテンツの種類の使用方法の詳細については、[こちらをご覧ください](app-service-logic-content-type.md)。

さらに、定義では JSON スキーマを指定できます。これにより、デザイナーでトークンを生成し、それをステップに渡すことができます。たとえば、次のコードは、`title` および `name` トークンをデザイナーで使用できるようにします。

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## 受信要求のコンテンツの参照
`@triggerOutputs()` 関数は、受信要求のコンテンツを出力します。たとえば、次のようになります。

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

具体的には、`@triggerBody()` ショートカットを使用して、`body` プロパティにアクセスできます。

## 要求への応答
ロジック アプリを起動する要求に対しては、何らかのコンテンツで呼び出し元に応答した方がよい場合があります。**response** という新しい種類のアクションがあります。これを使用して、応答の状態コード、本文、ヘッダーを作成できます。**response** 図形が存在しない場合、ロジック アプリのエンドポイントは*即時に* **202 承認済み**と応答します。

![HTTP 応答アクション][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

応答は次のプロパティを持っています。

| プロパティ | Description |
| --- | --- |
| StatusCode |受信要求に応答する HTTP 状態コード。2xx、4xx、または 5xx で始まる任意の有効な状態コードを使用できます。3xx 状態コードは指定できません。 |
| body |文字列、JSON オブジェクト、または前のステップから参照されるバイナリ コンテンツを指定できる body オブジェクト。 |
| headers |応答に含める任意の数のヘッダーを定義できます。 |

応答のために必要なロジック アプリにおけるすべてのステップは、元の要求が応答を受信できるように、**ワークフローが入れ子になった Logic Apps として呼び出されていない限り**、*60 秒*以内に完了する必要があります。60 秒以内に response アクションに至らない場合は、受信要求はタイムアウトとなり、**408 Client timeout** という HTTP 応答を受信します。入れ子になった Logic Apps の場合、親 Logic App は、時間がかかっても応答が完了するまで待機します。

## エンドポイントの詳細構成
Logic Apps には直接アクセス エンドポイントのサポートが組み込まれており、実行を開始する場合は常に `POST` メソッドを使用して Logic Apps の実行を開始します。**HTTP リスナー**の API アプリも以前は、URL セグメントと HTTP メソッドの変更をサポートしていました。追加のセキュリティまたはカスタム ドメインを、API アプリ ホスト (API アプリをホストする Web アプリ) に追加することで、セットアップすることもできました。

この機能は次の **API 管理**を通じて利用できます。

* [要求のメソッドを変更する](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [要求の URL セグメントを変更する](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* クラシック Azure ポータルの **[構成]** タブで API 管理ドメインをセットアップする
* 基本認証を確認するためのポリシーをセットアップする (**リンクが必要**)

## 2014-12-01-preview からの移行の概要
| 2014-12-01-preview | 2016-06-01 |
| --- | --- |
| **HTTP リスナー** API アプリをクリックする |**[手動トリガー]** をクリックする (API アプリは不要) |
| HTTP リスナー設定 "*Sends response automatically*" |ワークフロー定義に **response** アクションを含めるまたは含めない |
| 基本認証または OAuth 認証を構成する |API 管理による |
| HTTP メソッドを構成する |API 管理による |
| 相対パスを構成する |API 管理による |
| `@triggerOutputs().body.Content` を介して受信本文を参照する |`@triggerOutputs().body` を介した参照 |
| HTTP リスナーの **Send HTTP response** アクション |**[HTTP 要求に応答]** をクリックする (API アプリは不要) |

[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png

<!---HONumber=AcomDC_0810_2016-->