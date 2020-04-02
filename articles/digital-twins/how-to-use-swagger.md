---
title: リファレンス Swagger ドキュメントの使用方法 - Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins Swagger リファレンス ドキュメントの使用方法について
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023295"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Azure Digital Twins Swagger リファレンス ドキュメント

プロビジョニングされた各 Azure Digital Twins インスタンスには、自動生成された独自の Swagger リファレンス ドキュメントが含まれています。

[Swagger](https://swagger.io/) (または [OpenAPI](https://www.openapis.org/)) では、複雑な API 情報を、言語に依存しない対話型のリファレンス リソースに統合します。 Swagger は、API に対する操作を実行するために使用する JSON ペイロード、HTTP メソッド、および特定のエンドポイントに関する重要な参考資料を提供します。

## <a name="swagger-summary"></a>Swagger の概要

Swagger では、以下を含む API の対話型の概要が提供されます。

* API とオブジェクト モデルの情報。
* 必須の要求ペイロード、ヘッダー、パラメーター、コンテキストのパス、および HTTP メソッドを指定する REST API エンドポイント。
* API の機能のテスト。
* HTTP 応答の検証と確認に使用される応答情報の例。
* エラー コード情報。

Swagger は、Azure Digital Twins Management API に対して行われる呼び出しの開発およびテストを支援する便利なツールです。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>参考資料

自動的に生成される Swagger の参考資料では、重要な概念の簡単な概要、使用可能な管理 API エンドポイント、および開発とテストを支援する各オブジェクト モデルの説明を提供します。

簡潔な概要では、API について説明されています。

[![Swagger 概要と API 概要情報](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Management API オブジェクト モデルも一覧表示されます。

[![Swagger UI の下部に一覧表示されている Swagger モデル](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

一覧表示されている各オブジェクト モデルを選択すると、キー属性の詳細な概要が表示されます。

[![Swagger モデルが展開されてモデルの内容が読めるようになっている](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

生成された Swagger オブジェクト モデルは、Azure Digital Twins の使用可能なすべての[オブジェクトおよび API](./concepts-objectmodel-spatialgraph.md) を確認するのに便利です。 開発者は、Azure Digital Twins でソリューションを作成するときに、このリソースの使用できます。

## <a name="endpoint-summary"></a>エンドポイントの概要

Swagger では、Management API を構成するすべてのエンドポイントの完全な概要も提供します。

一覧表示されている各エンドポイントには、次のような要求の必須情報も含まれます。

* 必須のパラメーター。
* 必須のパラメーターのデータ型。
* リソースにアクセスする HTTP メソッド。

[![Swagger UI に表示されている Swagger エンドポイント](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

各リソースを選択すると、追加の内容が表示され、さらに詳細な概要が得られます。

## <a name="use-swagger-to-test-endpoints"></a>Swagger を使用してエンドポイントをテストする

Swagger が提供する強力な機能の 1 つは、ドキュメントの UI から直接、API エンドポイントをテストできることです。

特定のエンドポイントを選択すると、 **[試してみる]** ボタンが表示されます。

[![Swagger の [試してみる] ボタン](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

そのセクションを展開すると、必須および省略可能な各パラメーターの入力フィールドが表示されます。 適切な値を入力し、 **[Execute]\(実行\)** を選択します。

[![Swagger の [試してみる] の結果例](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

テストを実行した後は、応答データを検証できます。

## <a name="swagger-response-data"></a>Swagger の応答データ

一覧表示されている各エンドポイントには、開発およびテストを検証するための応答本文データも含まれます。 これらの例には、成功した HTTP 要求の状態コードおよび JSON が含まれます。

[![Swagger JSON 応答例](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

これらの例には、失敗したテストのデバッグまたは改善に役立つエラー コードも含まれます。

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 承認

> [!NOTE]
> * Azure Digital Twins リソースを作成したユーザー プリンシパルにはスペース管理者ロールが割り当てられ、他のユーザーに対して追加のロール割り当てを作成できます。 このようなユーザーとそのロールには、API の呼び出しを許可することができます。

1. [こちらのクイックスタート](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app)の手順を実行して、Azure Active Directory アプリケーションを作成し、構成します。 または、既存のアプリの登録を再利用することもできます。

1. 次の**リダイレクト URI** を Azure Active Directory アプリ登録に追加します。

    [![AAD に Swagger リダイレクト URL を登録する](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | 名前  | 置換後の文字列 | 例 |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | ポータルで見つかった Management REST API ドキュメントの URL  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. **[暗黙の付与]** 、 **[アクセス トークン]** チェック ボックスの順に選択し、OAuth 2.0 の暗黙的な許可のフローを使用できるようにします。 **[構成]** 、 **[保存]** の順に選択します。

1. Azure Active Directory アプリの **[クライアント ID]** をコピーします。

Azure Active Directory の登録を完了した後:

1. Swagger ページで **[Authorize]\(承認\)** ボタンをクリックします。

    [![Swagger の [Authorize]\(承認\) ボタンをクリックする](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. アプリケーション ID を **[client_id]** フィールドに貼り付けます。

    [![Swagger の [client_id] フィールド](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. 次の成功のモーダルにリダイレクトされます。

    [![Swagger のリダイレクト モーダル](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

OAuth 2.0 によって保護された要求の対話的なテストに関する詳細については、[公式ドキュメント](https://swagger.io/docs/specification/authentication/oauth2/)をお読みください。

## <a name="next-steps"></a>次のステップ

- Azure Digital Twins のオブジェクト モデルおよび空間インテリジェンス グラフについて詳しくは、[Azure Digital Twins オブジェクト モデル](./concepts-objectmodel-spatialgraph.md)に関する記事をご覧ください。

- Management API を使用して認証を行う方法については、[API を使用した認証](./security-authenticating-apis.md)に関する記事をご覧ください。
