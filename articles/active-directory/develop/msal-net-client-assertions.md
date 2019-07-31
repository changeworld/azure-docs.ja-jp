---
title: .NET 用 Microsoft Authentication Library でのクライアント アサーション | Azure
description: .NET 用の Microsoft Authentication Library (MSAL.NET) での機密クライアント アプリケーションに対する署名付きクライアント アサーションのサポートについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1ea75499334f3f6eb2f5d3c15526067fcef4eb8
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442503"
---
# <a name="confidential-client-assertions"></a>機密クライアント アサーション
ID を証明するために、機密クライアント アプリケーションは Azure AD とシークレットを交換します。 次のようなシークレットがあります。
- クライアント シークレット (アプリケーション パスワード)。
- 証明書。標準の要求を含む署名付きアサーションの構築に使用されます。

このシークレットは直接署名されたアサーションの場合もあります。

MSAL.NET には、機密クライアント アプリに資格情報またはアサーションを提供する方法が 4 つあります。
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

### <a name="signed-assertions"></a>署名付きアサーション

署名付きクライアント アサーションは、Base64 エンコードであり、Azure AD で必須とされる、必要な認証要求を含むペイロードがある署名付き JWT という形式です。 使用方法:

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Azure AD で想定される要求は次のとおりです。

要求の種類 | 値 | 説明
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | "aud" (対象ユーザー) 要求では、JWT が意図する受信者 (ここでは Azure AD) を指定します。[RFC 7519、セクション 4.1.3] を参照してください。
exp | 2019 年 6 月 27 日木曜日 15:04:17 GMT + 0200 (ロマンス夏時間) | "exp" (有効期限) 要求は、JWT の処理を受け入れることができなくなる時刻を指定します。 [RFC 7519、セクション 4.1.4] を参照してください。
iss | {ClientID} | "iss" (発行者) 要求では、JWT を発行した元本を指定します。 この要求の処理はアプリケーション固有です。 "iss" 値は、StringOrURI 値を含む大文字と小文字が区別される文字列です。 [RFC 7519、セクション 4.1.1]
jti | (Guid) | "jti" (JWT ID) 要求は、JWT の一意の識別子を提供します。 識別子の値は、同じ値が誤って異なるデータ オブジェクトに割り当てられる可能性が無視できるほど低くなる方法で割り当てる必要があります。複数の発行者を使用するアプリケーションの場合は、異なる発行者が生成した値が競合しないように防ぐ必要があります。 "jti" 要求を使用すると、JWT の再生を防ぐことができます。 "jti" 値は大文字と小文字が区別される文字列です。 [RFC 7519、セクション 4.1.7]
nbf | 2019 年 6 月 27 日木曜日 14:54:17 GMT+0200 (ロマンス夏時間) | "nbf" (指定時刻よりも後) 要求では、指定した時刻よりも後に JWT の処理を受け入れることができるようになります。 [RFC 7519、セクション 4.1.5]
sub | {ClientID} | "sub" (主題) 要求では、JWT の件名を指定します。 通常、JWT の要求は主題に関するステートメントです。 主題値は、発行者のコンテキストのローカルで一意、またはグローバルで一意になるようにスコープを設定する必要があります。 [RFC 7519、セクション 4.1.2] を参照してください

これらの要求を作成する方法の例を次に示します。

```CSharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = "https://login.microsoftonline.com/72f988bf-86f1-41af-hd4m-2d7cd011db47/v2.0";

      string ConfidentialClientID = "61dab2ba-145d-4b1b-8569-bf4b9aed5dhb" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

署名付きクライアント アサーションを作成する方法は次のとおりです。

```CSharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
    X509Certificate2 certificate = ReadCertificate(config.CertificateName);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string SignedAssertion = string.Concat(token, ".", signature);
    return SignedAssertion;
}
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` では、既定で、Azure AD で想定される要求と、送信する追加のクライアント要求を含む署名付きアサーションが生成されます。 これを行う方法を示すコード スニペットは次のとおりです。

```CSharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

渡したディクショナリ内の要求の 1 つが必須の要求の 1 つと同じである場合は、追加の要求の値が考慮されます。 これによって、MSAL.NET で計算された要求がオーバーライドされます。

Azure AD で想定される必須の要求を含め、独自の要求を指定する場合は、`mergeWithDefaultClaims` パラメーターに `false` を渡します。
