---
title: アクセス キーまたは Shared Access Signature を使用して Azure Event Grid 発行クライアントを認証する
description: この記事では、アクセス キーと Shared Access Signature を使用して Azure Event Grid 発行クライアントを認証する方法について説明します。
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 5a271372afcc36df97d0a7afa37ee0b065a5a5e6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779372"
---
# <a name="authenticate-azure-event-grid-publishing-clients-using-access-keys-or-shared-access-signatures"></a>アクセス キーまたは Shared Access Signature を使用して Azure Event Grid 発行クライアントを認証する
この記事では、**アクセス キー** または **Shared Access Signature (SAS)** トークンを使用して、Azure Event Grid のトピック、ドメイン、パートナー名前空間にイベントを発行するクライアントを認証する方法について説明します。 

> [!IMPORTANT]
> Azure AD の ID を使用したユーザーやアプリケーションの認証および承認は、キー ベースや Shared Access Signatures (SAS) の認証よりも優れたセキュリティと使いやすさを提供します。 Azure AD を使用すれば、コードに認証用のシークレットを格納する必要がないため、潜在的なセキュリティの脆弱性をリスクに晒すことはなくなります。 Azure Event Grid イベント発行アプリケーションで Azure AD を使用することを強くお勧めします。 詳細については、[Azure Active Directory を使用した発行クライアントの認証](authenticate-with-active-directory.md)に関するページを参照してください。

## <a name="authenticate-using-access-key"></a>アクセス キーを使用して認証する
アクセス キーによる認証は、最も簡単な形式の認証です。 アクセス キーは、HTTP ヘッダーまたは URL クエリ パラメーターとして渡すことができます。 

### <a name="access-key-in-an-http-header"></a>HTTP ヘッダー内のアクセス キー
HTTP ヘッダー `aeg-sas-key` の値としてアクセス キーを渡します。

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>クエリ パラメーターとしてのアクセス キー
クエリ パラメーターとして `aeg-sas-key` を指定することもできます。 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

トピックまたはドメインに対するアクセス キーを取得する方法については、[アクセス キーの取得](get-access-keys.md)に関する記事を参照してください。

## <a name="authenticate-using-sas"></a>SAS を使用して認証する 
Event Grid リソースに対する SAS トークンには、リソース、有効期限、署名が含まれます。 SAS トークンの形式は `r={resource}&e={expiration}&s={signature}` です。

リソースは、イベントを送信する Event Grid グリッド トピックのパスです。 たとえば、有効なリソース パスは `https://<yourtopic>.<region>.eventgrid.azure.net/api/events` です。 サポートされているすべての API バージョンについては、「[Microsoft.EventGrid リソースの種類](/azure/templates/microsoft.eventgrid/allversions)」を参照してください。 

最初に、プログラムで SAS トークンを生成してから、`aeg-sas-token` ヘッダーまたは `Authorization SharedAccessSignature` ヘッダーを使用して Event Grid での認証を行います。 

### <a name="generate-sas-token-programmatically"></a>プログラムで SAS トークンを生成する
次の例では、Event Grid で使用する SAS トークンを作成します。

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>aeg-sas-token ヘッダーの使用
`aeg-sas-token` ヘッダーの値として SAS トークンを渡す例を次に示します。 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Authorization ヘッダーの使用
`Authorization` ヘッダーの値として SAS トークンを渡す例を次に示します。 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>次のステップ
イベントを配信するためのイベント ハンドラーでの認証の詳細については、[イベント配信の認証](security-authentication.md)に関する記事を参照してください。 
