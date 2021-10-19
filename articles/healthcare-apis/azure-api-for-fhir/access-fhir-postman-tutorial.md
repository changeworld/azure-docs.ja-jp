---
title: Azure 内の Postman FHIR サーバー - Azure API for FHIR
description: このチュートリアルでは、Postman を使用して FHIR サーバーにアクセスするために必要な手順について説明します。 Postman は、API にアクセスするアプリケーションのデバッグに役立ちます。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: cavoeg
author: matjazl
ms.date: 03/26/2021
ms.openlocfilehash: 1080b8fb47ebcab07737d26464302a9e18465315
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788103"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Postman を使用して Azure API for FHIR にアクセスする

クライアント アプリケーションは、[REST API](https://www.hl7.org/fhir/http.html) を通して、FHIR の Azure API にアクセスできます。 要求を送信し、応答を表示し、アプリケーションをビルド中にデバッグするには、任意の API テスト ツールを使用します。 このチュートリアルでは [、Postman](https://www.getpostman.com/)を使用して FHIR サーバーにアクセスする手順について説明します。 

## <a name="prerequisites"></a>前提条件

- Azure 内の FHIR エンドポイント。 

  マネージド サービス (Azure API for FHIR) をデプロイするには、 [、PowerShell、](fhir-paas-powershell-quickstart.md)または Azure portal[を使用](fhir-paas-portal-quickstart.md)[Azure CLI。](fhir-paas-cli-quickstart.md)

- FHIR [サービスにアクセスする](register-confidential-azure-ad-client-app.md) 登録済みの機密クライアント アプリケーション。
- 機密クライアント アプリケーションとユーザー アカウント ("FHIR データ共同作成者" など) に、FHIR サービスにアクセスするためのアクセス許可を付与しました。 詳細については [、「Azure RBAC for FHIR の構成」を参照してください](./configure-azure-rbac.md)。
- Postman がインストールされている。 
    
  Postman の詳細については、「Postman を使用したはじめに[を参照してください](https://www.getpostman.com)。

## <a name="fhir-server-and-authentication-details"></a>FHIR サーバーと認証の詳細

Postman を使用するには、次の認証パラメーターが必要です。

- FHIR サーバー URL (例: ) `https://MYACCOUNT.azurehealthcareapis.com`

- ご使用の FHIR サーバーの ID プロバイダー `Authority` (例: `https://login.microsoftonline.com/{TENANT-ID}`)

- 通常 `audience` 、FHIR サーバーの URL である 構成済み (例: `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` または `https://azurehealthcareapis.com` )。

- `client_id`FHIR サービスへのアクセスに[使用される](register-confidential-azure-ad-client-app.md)機密クライアント アプリケーションの または アプリケーション ID。

- 機密 `client_secret` クライアント アプリケーションの または アプリケーション シークレット。

最後に、`https://www.getpostman.com/oauth2/callback` が実際のクライアント アプリケーションに対して登録されている応答 URL であることを確認する必要があります。

## <a name="connect-to-fhir-server"></a>FHIR サーバーに接続する

Postman を開き、 **[GET] を選択** して に要求します `https://fhir-server-url/metadata` 。

![Postman のメタデータ機能ステートメント](media/tutorial-postman/postman-metadata.png)

Azure API for FHIR のメタデータ URL は `https://MYACCOUNT.azurehealthcareapis.com/metadata` です。 

この例では、FHIR サーバー URL は であり、サーバーの `https://fhirdocsmsft.azurewebsites.net` 機能ステートメントは で使用できます `https://fhirdocsmsft.azurewebsites.net/metadata` 。 このエンドポイントには、認証なしでアクセスできます。

制限されたリソースにアクセスしようとすると、"認証に失敗しました" という応答が発生します。

![認証に失敗しました](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>アクセス トークンを取得する
**[Get New Access Token]\(新しいアクセス トークンを取得する\)** を選択します。

有効なアクセス トークンを取得するには、[**承認]** を選択し、[種類] ドロップダウンメニューから **[OAuth 2.0]** を選択します。

![OAuth 2.0 の設定](media/tutorial-postman/postman-select-oauth-two.png)

**[Get New Access Token]\(新しいアクセス トークンを取得する\)** を選択します。

![新しいアクセス トークンの要求](media/tutorial-postman/postman-request-token.png)

[新 **しいアクセス トークンの取得] ダイアログ** ボックスで、次の詳細を入力します。

| フィールド                 | 値の例                                                                                                   | 解説                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Token Name (トークン名)            | MYTOKEN                                                                                                         | 選択した名前          |
| 付与タイプ            | Authorization Code (承認コード)                                                                                              |                            |
| コールバック URL          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| 認証 URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` は Azure API for FHIR の `https://MYACCOUNT.azurehealthcareapis.com` です |
| Access Token URL (アクセス トークン URL)      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| クライアント ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | アプリケーション ID             |
| クライアント シークレット         | `XXXXXXXX`                                                                                                      | シークレット クライアント キー          |
| Scope | `<Leave Blank>` | スコープは使用されません。そのため、空白のままにすることができます。  
| State                 | `1234`     | [州](https://learning.postman.com/docs/sending-requests/authorization/) は、クロスサイト リクエスト フォージェリを防ぐための不透明な値です。 省略可能であり、"1234" などの任意の値を受け取る場合があります。                           |
| クライアント認証 | Send client credentials in body (本文でクライアントの資格情報を送信する)                                                                                 |                 

[**要求トークン]** を選択して Azure Active Directory認証フローを案内すると、トークンが Postman に返されます。 認証エラーが発生した場合は、Postman コンソールで詳細を参照してください。 **注**: リボンで [表示] を選択 **し****、[Postman コンソールの表示] を選択します**。 Postman コンソールへのキーボード ショートカットは **、Alt + Ctrl + C です**。

下にスクロールして返されたトークン画面を表示し、[ トークンの使用 ] **を選択します**。

![トークンの使用](media/tutorial-postman/postman-use-token.png)

新しく設定 **されたトークンを表示** するには、 [アクセス トークン] フィールドを参照してください。 [送信] を **選択して** リソース検索 `Patient` を繰り返した場合は、 **状態が** `200 OK` 返されます。 返された状態は `200 OK` 、成功した HTTP 要求を示します。

![200 OK](media/tutorial-postman/postman-two-hundred-ok.png)

患者検索 *の例* では、検索結果が空の患者がデータベースにはありません。

アクセス トークンは、 のようなツールを使用して検査[jwt.ms。](https://jwt.ms) コンテンツの例を次に示します。

```json
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

トラブルシューティングの状況では、まずは対象ユーザー (`aud` 要求) が正しいことを検証することをお勧めします。 トークンが正しい発行者 ( 要求) から取得され、適切な対象ユーザー ( 要求) を持っているが、FHIR API にアクセスできない場合は、ユーザーまたはサービス プリンシパル ( 要求) が `iss` `aud` `oid` FHIR データ プレーンにアクセスできない可能性があります。 ユーザーにデータ プレーン ロールを割り当てるには、Azure ロールベースのアクセス制御 [(Azure RBAC)](configure-azure-rbac.md) を使用することをお勧めします。 データ プレーンに外部のセカンダリ Azure Active Directory テナントを使用している場合は [、FHIR](configure-local-rbac.md) 割り当て用にローカル RBAC を構成する必要があります。

また、 を使用して、アプリケーションのトークンをAzure API for FHIR[取得Azure CLI。](get-healthcare-apis-access-token-cli.md) アプリケーションで取得したトークンを使用している場合はAzure CLIベアラー トークン を *使用する必要があります*。 トークンを直接貼り付けます。

## <a name="inserting-a-patient"></a>患者を挿入する

有効なアクセス トークンを使用して、新しい患者を挿入できます。 Postman で、Post を選択してメソッドを変更し、要求の本文に次の JSON ドキュメントを追加します。

[!code-json[](../samples/sample-patient.json)]

[送信 **] を** 選択して、患者が正常に作成されたことを確認します。

![患者が正常に作成されたことを示すスクリーンショット。](media/tutorial-postman/postman-patient-created.png)

患者の検索を繰り返す場合は、患者レコードが表示されます。

![作成された患者](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Postman を使用してAzure API for FHIRにアクセスしました。 次の機能の詳細についてはAzure API for FHIR参照してください。
 
>[!div class="nextstepaction"]
>[サポートされる機能](fhir-features-supported.md)
