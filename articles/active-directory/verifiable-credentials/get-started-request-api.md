---
title: 要求サービス REST API を呼び出す方法
titleSuffix: Azure Active Directory Verifiable Credentials
description: 要求サービス REST API を使用して発行および検証する方法について説明します
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 6dff1940219aa666453d150ed24d84f8eb0165da
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809860"
---
# <a name="request-service-rest-api-preview"></a>要求サービス REST API (プレビュー)

Azure Active Directory の検証可能な資格情報の要求サービス REST API を使用すると、Azure AD の検証可能な資格情報サービスを使用して検証可能な資格情報を発行および検証できます。 この記事では、要求サービス REST API を使用して開始する方法について説明します。

> [!IMPORTANT]
> 現在、要求サービス REST API は、パブリック プレビュー (ベータ) 段階にあります。
> このプレビュー バージョンは、サービス レベル アグリーメントなしで提供されます。プレビュー バージョンでは、API に重大な変更が加えられたり、非推奨化されたりすることがあります。 プレビュー段階では、実稼働ワークロードに対して API を使用することはお勧めしません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="api-access-token"></a>API のアクセス トークン

アプリケーションから要求サービス REST API にアクセスするには、必要なアクセス許可を持つ有効なアクセス トークンを含める必要があります。 Microsoft ID プラットフォームによって発行されたアクセス トークンには、要求サービス REST API で呼び出し元を検証するために使用される情報 (スコープ) が含まれています。 呼び出し元が要求している操作を実行するための適切なアクセス許可を持っていることが保証されます。

アクセス トークンを取得するには、アプリが Microsoft ID プラットフォームに登録され、要求サービス API へのアクセスを管理者によって認可されている必要があります。 "*検証可能な資格情報アプリ*" のアプリケーションを登録していない場合は、[アプリを登録する方法](verifiable-credentials-configure-tenant.md#step-3-register-an-application-in-azure-ad)に関する記事の手順に従って、[アプリケーション シークレットを生成](verifiable-credentials-configure-issuer.md#configure-the-verifiable-credentials-app)します。

### <a name="get-an-access-token"></a>アクセス トークンを取得する

[OAuth 2.0 クライアント資格情報の付与フロー](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)を使用し、Microsoft ID プラットフォームを使用してアクセス トークンを取得します。 信頼された OAuth ライブラリを使用することをお勧めします。 このチュートリアルでは、Microsoft Authentication Library ([MSAL](../../active-directory/develop/msal-overview.md)) を使用します。 MSAL は、Microsoft が提供するライブラリであり、セキュリティで保護された Web API を呼び出すことができるアプリへの認証と認可の追加が簡略化されます。

# <a name="http"></a>[HTTP](#tab/http)

```http
Pleaes refer to to the Microsoft Authentication Library (MSAL) documentation for more information on how to acquire tokens via HTTP.
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
    .WithClientSecret(AppSettings.ClientSecret)
    .WithAuthority(new Uri(AppSettings.Authority))
    .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientSecret: config.azClientSecret,
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

上記のコードでは、次のパラメーターを指定します。

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| Authority | 必須 | アプリケーションで操作する対象のディレクトリ テナント。 たとえば、`https://login.microsoftonline.com/{your-tenant}` です。`your-tenant` を[テナント ID または名前](../fundamentals/active-directory-how-to-find-tenant.md)に置き換えます。 |
| クライアント ID | 必須 | お使いのアプリに割り当てられたアプリケーション ID。 この情報は、Azure portal のアプリを登録した場所で確認できます。 |
| クライアント シークレット | 必須 | アプリ用に生成したクライアント シークレット。|
| スコープ | 必須 | `bbb94529-53a3-4be5-a069-7eaf2712b826/.default` に設定する必要があります。 |


コンソール アプリの ID を使用してアクセス トークンを取得する方法の詳細については、[C#](../develop/quickstart-v2-netcore-daemon.md)、[Python](../develop/quickstart-v2-python-daemon.md)、[Node.js](../develop/quickstart-v2-nodejs-console.md)、または [Java](../develop/quickstart-v2-java-daemon.md) のいずれかの記事を参照してください。

クライアント シークレットではなく、[証明書を使用してアクセス トークンを要求する](../develop/v2-oauth2-client-creds-grant-flow.md)こともできます。

# <a name="http"></a>[HTTP](#tab/http)

```http
POST /{tenant}/oauth2/v2.0/token HTTP/1.1   //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=12345678-0000-0000-00000000000000000
&scope=bbb94529-53a3-4be5-a069-7eaf2712b826/.default
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
X509Certificate2 certificate = AppSettings.ReadCertificate(AppSettings.CertificateName);
    app = ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
        .WithCertificate(certificate)
        .WithAuthority(new Uri(AppSettings.Authority))
        .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientCertificate: {
            thumbprint: "CERT_THUMBPRINT", // a 40-digit hexadecimal string
            privateKey: "CERT_PRIVATE_KEY"
        }
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

## <a name="call-the-api"></a>API を呼び出す

検証可能な資格情報を発行または検証するには、次の手順を実行します。

1. 要求サービス REST API に対する HTTP POST 要求を作成します。 `{tenantID}` を実際の[テナント ID](https://TBD) またはテナント名に置き換えます。

    ```http
    POST https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
    ```

1. アクセス トークンをベアラー トークンとして HTTP 要求の Authorization ヘッダーにアタッチします。

    ```http
    Authorization: Bearer <token>
    ```

1. `Content-Type` ヘッダーを `Application/json` に設定します。

1. [発行](issuance-request-api.md#issuance-request-payload)または[プレゼンテーション](presentation-request-api.md#presentation-request-payload)の要求ペイロードを準備して、要求本文にアタッチします。

1. 要求を要求サービス REST API に送信します。

## <a name="issuance-request-example"></a>発行要求の例

次の例は、検証可能な資格情報の発行要求を示しています。 ペイロードの詳細については、[要求サービス REST API の発行の仕様](issuance-request-api.md)に関する記事を参照してください

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert1",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```  

完全なコードについては、[C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/IssuerController.cs) または [Node.js](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js) のいずれかのコード サンプルを確認してください。

## <a name="presentation-request-example"></a>プレゼンテーション要求の例

次の例は、検証可能な資格情報のプレゼンテーション要求を示しています。 ペイロードの詳細については、[要求サービス REST API のプレゼンテーションの仕様](presentation-request-api.md)に関する記事を参照してください

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
  "includeQRCode": true,
  "callback": {
    "url": "https://www.contoso.com/api/verifier/presentationCallback",
    "state": "92d076dd-450a-4247-aa5b-d2e75a1a5d58",
    "headers": {
      "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
    }
  },
  "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiOiJiRWo5MDY...",
  "registration": {
    "clientName": "Veritable Credential Expert Verifier"
  },
  "presentation": {
    "includeReceipt": true,
    "requestedCredentials": [
      {
        "type": "VerifiedCredentialExpert",
        "purpose": "So we can see that you a veritable credentials expert",
        "acceptedIssuers": [
          "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiO..."
        ]
      }
    ]
  }
}
```

完全なコードについては、次のコード サンプルのいずれかを確認してください。

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/VerifierController.cs) 
- [Node.js](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js)。

## <a name="callback-events"></a>コールバック イベント

要求ペイロードには、[発行](issuance-request-api.md#callback-events)および[プレゼンテーション](presentation-request-api.md#callback-events)のコールバック エンドポイントが含まれています。 エンドポイントは Web アプリケーションの一部であり、一般公開されている必要があります。 Azure AD の検証可能な資格情報サービスでは、エンドポイントを呼び出して、特定のイベントについてアプリに通知します。 たとえば、ユーザーが QR コードをスキャンしたときに、Authenticator アプリへのディープ リンクを使用するか、プレゼンテーション プロセスを終了します。

次の図は、要求サービス REST API に対してアプリで行う呼び出しと、アプリケーションへのコールバックを示しています。

![API への呼び出しとコールバック イベントを示す図。](media/get-started-request-api/callback-events.png)

受信 HTTP POST 要求をリッスンするようにエンドポイントを構成します。 次のコード スニペットは、発行のコールバック HTTP 要求を処理し、それに応じて UI を更新する方法を示しています。

# <a name="http"></a>[HTTP](#tab/http)

適用不可。 上記のいずれかのプログラミング言語を選択してください。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[HttpPost]
public async Task<ActionResult> IssuanceCallback()
{
try
{
    string content = new System.IO.StreamReader(this.Request.Body).ReadToEndAsync().Result;
    _log.LogTrace("callback!: " + content);
    JObject issuanceResponse = JObject.Parse(content);
    
    // More code here
    if (issuanceResponse["code"].ToString() == "request_retrieved")
    {
        var cacheData = new
        {
            status = "request_retrieved",
            message = "QR Code is scanned. Waiting for issuance...",
        };
        _cache.Set(state, JsonConvert.SerializeObject(cacheData));

        // More code here
    }
}
```

完全なコードについては、GitHub リポジトリの[発行](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/IssuerController.cs)と[プレゼンテーション](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/VerifierController.cs)のコードを参照してください。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
mainApp.app.post('/api/issuer/issuance-request-callback', parser, async (req, res) => {
  var body = '';
  req.on('data', function (data) {
    body += data;
  });
  req.on('end', function () {
    requestTrace( req );
    console.log( body );
    var issuanceResponse = JSON.parse(body.toString());
    var message = null;
    
    if ( issuanceResponse.code == "request_retrieved" ) {
      message = "QR Code is scanned. Waiting for issuance to complete...";
    }
    if ( issuanceResponse.code == "issuance_successful" ) {
      message = "Credential successfully issued";
    }
    if ( issuanceResponse.code == "issuance_error" ) {
      message = issuanceResponse.error.message;
    }
    
    // More code here
    res.send()
  });  
  res.send()
})
```

完全なコードについては、GitHub リポジトリの[発行](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js)と[プレゼンテーション](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js)のコードを参照してください。

---

## <a name="next-steps"></a>次の手順

次の記事を参照してください。

- [API の発行の仕様](issuance-request-api.md)
- [API のプレゼンテーションの仕様](presentation-request-api.md)
