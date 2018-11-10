---
title: Azure Digital Twins Swagger を使用する方法について | Microsoft Docs
description: Azure Digital Twins Swagger を使用する方法
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 3bc365c204ab75a2f136c3e26c4b598b25f66114
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093491"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>Azure Digital Twins Swagger を使用する方法

プロビジョニングされた各 Azure Digital Twins インスタンスには、自動生成された独自の Swagger リファレンス ドキュメントが含まれています。

[Swagger](https://swagger.io/) ([OpenAPI](https://www.openapis.org/)) では、複雑な API 情報を、言語に依存しない対話型のリファレンス リソースに統合します。 具体的には、Swagger は、API に対する操作を実行するために使用する JSON ペイロード、HTTP メソッド、および特定のエンドポイントに関する重要な参考資料を提供します。

## <a name="swagger-summary"></a>Swagger の概要

Swagger では、次を含む API の対話型の概要を提供します。

* API とオブジェクト モデルの情報。
* 必須の要求ペイロード、ヘッダー、パラメーター、コンテキストのパス、および HTTP メソッドを指定する REST API エンドポイント。
* API の機能のテスト。
* HTTP 応答を検証および確認するための応答情報の例。
* エラー コード情報。

したがって、Swagger は、Management API に対して行われる呼び出しの開発およびテストを支援する便利なツールです。

> [!TIP]
> リファレンスとして、API の機能を見ることができる Swagger のプレビューが提供されています。
> [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger) でホストされています。

生成された独自の Management API Swagger ドキュメントには、次の場所からアクセスできます。

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| カスタム属性の名前 | 置換後の文字列 |
| --- | --- |
| *yourInstanceName* | Azure Digital Twins インスタンスの名前 |
| *yourLocation* | インスタンスをホストするサーバーのリージョン |

## <a name="reference-material"></a>参考資料

自動的に生成された参照資料では、重要な概念とオブジェクト モデルについて説明しています。

簡潔な概要では、API について説明しています。

![Swagger の最上部][1]

コア API オブジェクト モデルも一覧表示されます。

![Swagger モデル][2]

一覧表示されている各オブジェクト モデルをクリックすると、キー属性の詳細な概要が表示されます。

![Swagger モデル][3]

生成された Swagger オブジェクト モデルは、Azure Digital Twins の使用可能なすべての[オブジェクトおよび API](./concepts-objectmodel-spatialgraph.md) を確認するのに便利です。 これらのモデルは、開発者が Azure Digital Twins でソリューションを構築する際に使用する優れたリソースです。

## <a name="endpoint-summary"></a>エンドポイントの概要

Swagger では、API を構成するすべてのエンドポイントの完全な概要も提供します。

一覧表示されている各エンドポイントには、次のような要求の必須情報も含まれます。

* 必須のパラメーター。
* 必須のパラメーターのデータ型。
* リソースにアクセスする HTTP メソッド。

![Swagger エンドポイント][4]

各リソースをクリックすると、詳細な概要が表示されます。

## <a name="using-swagger-to-test-endpoints"></a>Swagger を使用してエンドポイントをテストする

Swagger が提供する強力な機能の 1 つは、ドキュメントの UI から直接、API エンドポイントを**試用**したりテストしたりできることです。

特定のエンドポイントをクリックすると、**[Try it out]\(試用\)** ボタンが表示されます。

![Swagger の試用][5]

各セクションを展開すると、必須および省略可能な各パラメーターの入力フィールドが表示されます。 必要に応じて値を入力し、**[実行]** をクリックします。

![Swagger の試用後][6]

テストを実行した後は、応答データを検証できます。

## <a name="swagger-response-data"></a>Swagger の応答データ

一覧表示されている各エンドポイントには、開発およびテストを検証するための応答本文データも含まれます。 これらの例には、成功した HTTP 要求の目的の状態コードおよび JSON も含まれます。

![Swagger の応答][7]

これらの例には、失敗したテストのデバッグまたは改善に役立つエラー コードも含まれます。

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 承認

OAuth 2.0 によって保護された API のリソースに対する要求を対話的にテストするには、[公式ドキュメント](https://swagger.io/docs/specification/authentication/oauth2/)を参照してください。

## <a name="next-steps"></a>次の手順

Azure Digital Twins のオブジェクト モデルおよび空間インテリジェンス グラフの詳細については、[この記事](./concepts-objectmodel-spatialgraph.md)を参照してください。

Management API を使用して認証を行う方法については、[API を使用した認証](./security-authenticating-apis.md)に関するページを参照してください。

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
