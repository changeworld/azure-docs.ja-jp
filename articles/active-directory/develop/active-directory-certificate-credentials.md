---
title: Microsoft ID プラットフォームの証明書資格情報
titleSuffix: Microsoft identity platform
description: この記事では、アプリケーションを認証するための証明書資格情報の登録と使用について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d834d37f686279facf0052e3c8f4da3c8d259e0b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309686"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft ID プラットフォーム アプリケーションの認証証明書資格情報

Microsoft ID プラットフォームでは、[OAuth 2.0 クライアント資格情報付与フロー v2.0](v2-oauth2-client-creds-grant-flow.md)や [On-Behalf-Of フロー](v2-oauth2-on-behalf-of-flow.md)などで、アプリケーションが認証用の独自の資格情報を使用することが許可されています。

アプリケーションが認証を行うために使用できる資格情報の 1 つの形式は、アプリケーションが所有している証明書で署名された JSON Web トークン(JWT) アサーションです。

## <a name="assertion-format"></a>アサーションの形式
Microsoft ID プラットフォームでは、アサーションを計算するために、多数の [JSON Web トークン](https://jwt.ms/) ライブラリの中から好きな言語を選択して使用できます。 トークンによって伝達される情報は次のとおりです。

### <a name="header"></a>ヘッダー

| パラメーター |  注記 |
| --- | --- |
| `alg` | **RS256** です |
| `typ` | **JWT** です |
| `x5t` | X.509 証明書 SHA-1 サムプリントです |

### <a name="claims-payload"></a>要求 (ペイロード)

| パラメーター |  解説 |
| --- | --- |
| `aud` | 対象ユーザー: **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** である必要があります。 |
| `exp` | 有効期限: トークンの有効期限が切れる日付。 日時は、UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) からトークンが有効期限切れになるまでの秒数で表されます。|
| `iss` | 発行者: client_id (クライアント サービスのアプリケーション ID) である必要があります。 |
| `jti` | GUID: JWT ID |
| `nbf` | 期間の開始時刻: トークンの使用開始日。 日時は UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) から、トークンが発行された日時までの秒数で表されます。 |
| `sub` | 件名: `iss` の場合は、client_id (クライアント サービスのアプリケーション ID) である必要があります。 |

### <a name="signature"></a>署名

署名は、[JSON Web トークン RFC7519 仕様](https://tools.ietf.org/html/rfc7519)の説明に従って、証明書を適用することで計算されます

## <a name="example-of-a-decoded-jwt-assertion"></a>デコードされた JWT アサーションの例

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>エンコードされた JWT アサーションの例

次の文字列は、エンコードされたアサーションの例です。 よく見ると、ピリオド (.) で区切られた 3 つのセクションがあることがわかります。
* 最初のセクションは、ヘッダーをエンコードします。
* 2 番目のセクションは、ペイロードをエンコードします。
* 最後のセクションは、先の 2 つのセクションのコンテンツの証明書によって計算された署名です。

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Microsoft ID プラットフォームに証明書を登録する

次のいずれかの方法を使用して、Azure Portal 経由で Microsoft ID プラットフォームのクライアント アプリケーションに証明書資格情報を関連付けることができます。

### <a name="uploading-the-certificate-file"></a>証明書ファイルのアップロード

クライアント アプリケーションの Azure アプリ登録で、以下を実行します。
1. **[証明書とシークレット]** を選択します。
2. **[証明書のアップロード]** をクリックし、アップロードする証明書ファイルを選択します。
3. **[追加]** をクリックします。
  証明書がアップロードされると、サムプリント、開始日、有効期限の値が表示されます。

### <a name="updating-the-application-manifest"></a>アプリケーション マニフェストの更新

証明書を入手したら、次を計算する必要があります。

- `$base64Thumbprint`。証明書ハッシュの base64 エンコード
- `$base64Value`。証明書生データの base64 エンコード

また、GUID を指定して、アプリケーション マニフェストでキーを特定する必要もあります (`$keyId`)。

クライアント アプリケーションの Azure アプリ登録で、以下を実行します。
1. **[マニフェスト]** を選択して、アプリケーション マニフェストを開きます。
2. 次のスキーマを使用して、*keyCredentials* プロパティを新しい証明書情報に置き換えます。

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. 編集内容をアプリケーション マニフェストに保存した後、そのマニフェストを Microsoft ID プラットフォームにアップロードします。

   `keyCredentials` プロパティは複数値であるため、複数の証明書をアップロードして、高度なキー管理を行うこともできます。

## <a name="code-sample"></a>コード サンプル

> [!NOTE]
> X5T ヘッダーを計算するには、証明書のハッシュを使用してベース 64 文字列に変換する必要があります。 これを C# で実行する場合のコードは、`System.Convert.ToBase64String(cert.GetCertHash());` です。

「[Microsoft ID プラットフォームを使用した .NET Core デーモン コンソール アプリケーション](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)」のコード サンプルには、独自の資格情報がアプリケーションによって認証に使用される方法が示されています。 それは、[ Powershell コマンドを使用した](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script)自己証明書の作成`New-SelfSignedCertificate`方法も示しています。 [アプリ作成スクリプト](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md)を利用して、証明書の作成やサムプリントの計算などを実行することもできます。
