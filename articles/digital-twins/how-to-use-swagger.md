---
title: Azure Digital Twins Swagger リファレンスの使用方法について | Microsoft Docs
description: Azure Digital Twins Swagger リファレンス ドキュメントの使用方法について
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 842c35a1575c7bcf6f547fb04d5680178b3bee78
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730411"
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

[![Swagger の最上部](media/how-to-use-swagger/swagger_management_top.PNG)](media/how-to-use-swagger/swagger_management_top.PNG#lightbox)

Management API オブジェクト モデルも一覧表示されます。

[![Swagger モデル](media/how-to-use-swagger/swagger_management_models.PNG)](media/how-to-use-swagger/swagger_management_models.PNG#lightbox)

一覧表示されている各オブジェクト モデルを選択すると、キー属性の詳細な概要が表示されます。

[![Swagger モデル](media/how-to-use-swagger/swagger_management_model.PNG)](media/how-to-use-swagger/swagger_management_model.PNG#lightbox)

生成された Swagger オブジェクト モデルは、Azure Digital Twins の使用可能なすべての[オブジェクトおよび API](./concepts-objectmodel-spatialgraph.md) を確認するのに便利です。 開発者は、Azure Digital Twins でソリューションを作成するときに、このリソースの使用できます。

## <a name="endpoint-summary"></a>エンドポイントの概要

Swagger では、Management API を構成するすべてのエンドポイントの完全な概要も提供します。

一覧表示されている各エンドポイントには、次のような要求の必須情報も含まれます。

* 必須のパラメーター。
* 必須のパラメーターのデータ型。
* リソースにアクセスする HTTP メソッド。

[![Swagger エンドポイント](media/how-to-use-swagger/swagger_management_endpoints.PNG)](media/how-to-use-swagger/swagger_management_endpoints.PNG#lightbox)

さらに詳細な概要を見るには、各リソースをクリックします。

## <a name="use-swagger-to-test-endpoints"></a>Swagger を使用してエンドポイントをテストする

Swagger が提供する強力な機能の 1 つは、ドキュメントの UI から直接、API エンドポイントをテストできることです。

特定のエンドポイントを選択すると、 **[試してみる]** と表示されます。

[![Swagger の試用](media/how-to-use-swagger/swagger_management_try.PNG)](media/how-to-use-swagger/swagger_management_try.PNG#lightbox)

そのセクションを展開すると、必須および省略可能な各パラメーターの入力フィールドが表示されます。 適切な値を入力し、 **[Execute]\(実行\)** を選択します。

[![Swagger の試用後](media/how-to-use-swagger/swagger_management_tried.PNG)](media/how-to-use-swagger/swagger_management_tried.PNG#lightbox)

テストを実行した後は、応答データを検証できます。

## <a name="swagger-response-data"></a>Swagger の応答データ

一覧表示されている各エンドポイントには、開発およびテストを検証するための応答本文データも含まれます。 これらの例には、HTTP 要求が成功したときに表示される状態コードと JSON が含まれます。

[![Swagger の応答](media/how-to-use-swagger/swagger_management_response.PNG)](media/how-to-use-swagger/swagger_management_response.PNG#lightbox)

これらの例には、失敗したテストのデバッグまたは改善に役立つエラー コードも含まれます。

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 承認

OAuth 2.0 によって保護された要求の対話的なテストに関する詳細については、[公式ドキュメント](https://swagger.io/docs/specification/authentication/oauth2/)を参照してください。

> [!NOTE]
> Azure Digital Twins リソースを作成したユーザー プリンシパルにはスペース管理者ロールが割り当てられ、他のユーザーに対して追加のロール割り当てを作成できます。

1. [こちらのクイック スタート](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)の手順に従って、種類が ***Web アプリ/API*** である Azure AD アプリケーションを作成します。 既存のアプリの登録を再利用することもできます。

2. 次の応答 URL をアプリの登録に追加します。

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name  | 置換後の文字列 | 例 |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | ポータルで見つかった Management REST API ドキュメントの URL  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

3. Azure Digital Twins にアクセスするアクセス許可をアプリに付与します。 **[必要なアクセス許可]** に「`Azure Digital Twins`」と入力して、 **[委任されたアクセス許可]** を選択します。 その後、 **[アクセス許可の付与]** を選択します。

    ![Azure AD のアプリ登録追加 API](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

4. OAuth 2.0 暗黙的フローを許可するようにアプリケーション マニフェストを構成します。 **[マニフェスト]** をクリックして、アプリのアプリケーション マニフェストを開きます。 *oauth2AllowImplicitFlow* を `true` に設定します。

    ![Azure AD の暗黙的なフロー](../../includes/media/digital-twins-permissions/aad-app-allow-implicit-flow.png)

5. Azure AD アプリの ID をコピーします。

Azure Active Directory の登録を完了した後:

6. Swagger ページで **[Authorize]\(承認\)** ボタンをクリックします。

    [![Swagger の [Authorize]\(承認\) ボタンをクリックする](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

7. アプリケーション ID を **[client_id]** フィールドに貼り付けます。

    [![Swagger の [client_id] フィールド](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

8. 次の成功のモーダルにリダイレクトされます。

    [![Swagger のリダイレクト モーダル](media/how-to-use-swagger/swagger_auth_redirect.PNG)](media/how-to-use-swagger/swagger_auth_redirect.PNG#lightbox)

## <a name="next-steps"></a>次の手順

- Azure Digital Twins のオブジェクト モデルおよび空間インテリジェンス グラフについて詳しくは、[Azure Digital Twins オブジェクト モデル](./concepts-objectmodel-spatialgraph.md)に関する記事をご覧ください。

- Management API を使用して認証を行う方法については、[API を使用した認証](./security-authenticating-apis.md)に関する記事をご覧ください。