---
title: API Management でクライアント証明書認証を使用して API を保護する
titleSuffix: Azure API Management
description: クライアント証明書を使用して API へのアクセスを保護する方法について説明します。 ポリシー式を使用して、受信証明書を検証できます。
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2021
ms.author: danlep
ms.openlocfilehash: 2eac7a8de0ca49cdab85f3ab948e54e402073128
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582918"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management でクライアント証明書認証を使用して API を保護する方法

API Management には、クライアント証明書を使用して API (つまりクライアントから API Management) へのアクセスを保護する機能が備わっています。 接続しているクライアントによって提示される証明書を検証し、ポリシー式を使用して証明書のプロパティを目的の値に対して確認することができます。

クライアント証明書を使用して API のバックエンド サービスへの (つまり、API Management からバックエンドへの) アクセスをセキュリティで保護する方法については、[クライアント証明書認証を使用してバックエンド サービスをセキュリティで保護する方法](./api-management-howto-mutual-certificates.md)に関するページを参照してください。

> [!IMPORTANT]
> Developer、Basic、Standard、または Premium レベルで HTTP/2 経由でクライアント証明書を受信して確認するには、次に示すように、[カスタム ドメイン] ブレードで [クライアント証明書のネゴシエート] 設定を有効にする必要があります。

![[Negotiate client certificate] (クライアント証明書をネゴシエートする)](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Consumption レベルでクライアント証明書を受信して確認するには、下に示すように、[カスタム ドメイン] ブレードにある [Request client certificate] (クライアント証明書を要求する) の設定を有効にする必要があります。

![[Request client certificate] (クライアント証明書を要求する)](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="policy-to-validate-client-certificates"></a>クライアント証明書を検証するためのポリシー

[validate-client-certificate](api-management-access-restriction-policies.md#validate-client-certificate) ポリシーを使用して、API Management インスタンスでホストされている API へのアクセスに使用されるクライアント証明書の 1 つまたは複数の属性を検証します。

証明書の発行者、サブジェクト、拇印、証明書がオンライン失効リストに対して検証されるかどうかなど、1 つまたは複数の属性を検証するようにポリシーを構成します。

詳細については、「[API Management のアクセス制限ポリシー](api-management-access-restriction-policies.md)」を参照してください。

## <a name="certificate-validation-with-context-variables"></a>コンテキスト変数を使用した証明書の検証

[`context` 変数](api-management-policy-expressions.md#ContextVariables)を使用してポリシー式を作成し、クライアント証明書を確認することもできます。 次のセクションの例では、`context.Request.Certificate` プロパティと他の `context` プロパティを使用した式を示します。

> [!IMPORTANT]
> 2021 年 5 月以降、`context.Request.Certificate` プロパティで証明書が要求されるのは、API Management インスタンスの [`hostnameConfiguration`](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration) で `negotiateClientCertificate` プロパティが True に設定されている場合のみとなりました。 既定では、`negotiateClientCertificate` は False に設定されます。

### <a name="checking-the-issuer-and-subject"></a>発行者とサブジェクトの確認

以下のポリシーを構成して、クライアント証明書の発行者とサブジェクトを確認できます。

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 証明書失効リストの確認を無効にするには、`context.Request.Certificate.Verify()` の代わりに `context.Request.Certificate.VerifyNoRevocation()` を使用します。
> クライアント証明書が自己署名済みである場合、`context.Request.Certificate.Verify()` と `context.Request.Certificate.VerifyNoRevocation()` が機能するには、ルート (または中間) の CA 証明書を API Management に[アップロード](api-management-howto-ca-certificates.md)する必要があります。

### <a name="checking-the-thumbprint"></a>拇印の確認

以下のポリシーを構成して、クライアント証明書の拇印を確認できます。

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 証明書失効リストの確認を無効にするには、`context.Request.Certificate.Verify()` の代わりに `context.Request.Certificate.VerifyNoRevocation()` を使用します。
> クライアント証明書が自己署名済みである場合、`context.Request.Certificate.Verify()` と `context.Request.Certificate.VerifyNoRevocation()` が機能するには、ルート (または中間) の CA 証明書を API Management に[アップロード](api-management-howto-ca-certificates.md)する必要があります。

### <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management にアップロードされた証明書に対する拇印の確認

以下の例では、API Management にアップロードされた証明書に対して、クライアント証明書の拇印を確認する方法を示しています。

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> 証明書失効リストの確認を無効にするには、`context.Request.Certificate.Verify()` の代わりに `context.Request.Certificate.VerifyNoRevocation()` を使用します。
> クライアント証明書が自己署名済みである場合、`context.Request.Certificate.Verify()` と `context.Request.Certificate.VerifyNoRevocation()` が機能するには、ルート (または中間) の CA 証明書を API Management に[アップロード](api-management-howto-ca-certificates.md)する必要があります。

> [!TIP]
> この[記事](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)で説明されているクライアント証明書のデッドロックに関する問題が、要求が凍結する、要求がタイムアウト後に `403 Forbidden` 状態コードになる、`context.Request.Certificate` が `null` である、などのいくつかの形で現れる場合があります。 この問題は通常、コンテンツの長さが約 60KB 以上ある `POST` および `PUT` 要求に影響を与えます。
> この問題が発生しないようにするには、このドキュメントの最初の画像で示すように、[カスタム ドメイン] ブレードにある目的のホスト名の [クライアント証明書のネゴシエート] の設定を有効にします。 この機能は、Consumption レベルでは使用できません。

## <a name="next-steps"></a>次の手順

-   [クライアント証明書認証を使用してバックエンド サービスを保護する方法](./api-management-howto-mutual-certificates.md)
-   [証明書のアップロード方法](./api-management-howto-mutual-certificates.md)
