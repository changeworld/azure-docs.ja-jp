---
title: Azure Event Grid のセキュリティと認証
description: この記事では、Event Grid リソース (WebHook、サブスクリプション、カスタム トピック) へのアクセスを認証するためのさまざまな方法について説明します。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 528c3613549ee49009f99d45e5bd9c2cf1745d78
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779996"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Azure Event Grid リソースへのアクセスの認証
この記事では、次のシナリオについて説明します。  

- Shared Access Signature (SAS) またはキーを使用して Azure Event Grid トピックにイベントを発行するクライアントを認証します。 
- Azure Active Directory (Azure AD) を使用して Webhook エンドポイントを保護し、イベントをエンドポイントに**配信**する Event Grid を認証します。

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>SAS またはキーを使用した発行クライアントの認証
カスタム トピックは、Shared Access Signature (SAS) またはキー認証を使用します。 SAS が推奨されますが、キー認証はプログラミングが簡単で、既存の多くの webhook 発行元と互換性があります。

認証値は、HTTP ヘッダーに含めます。 SAS の場合は、ヘッダー値に **aeg-sas-token** を使用します。 キー認証の場合は、ヘッダー値に **aeg-sas-key** を使用します。

### <a name="key-authentication"></a>キー認証

キー認証は、最も単純な形式の認証です。 次の形式を使用します。`aeg-sas-key: <your key>`

たとえば、次のようにキーを渡します。

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS トークン

Event Grid の SAS トークンには、リソース、有効期限、および署名が含まれます。 SAS トークンの形式は `r={resource}&e={expiration}&s={signature}` です。

リソースは、イベントを送信する Event Grid グリッド トピックのパスです。 たとえば、有効なリソース パスは `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01` です。 サポートされているすべての API バージョンについては、「[Microsoft.EventGrid リソースの種類](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)」を参照してください。 

キーから署名を生成します。

たとえば、有効な **aeg-sas-token** 値は次のとおりです。

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

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

### <a name="encryption-at-rest"></a>保存時の暗号化

Event Grid サービスによってディスクに書き込まれるすべてのイベントまたはデータは、保存時に確実に暗号化されるように、Microsoft マネージド キーによって暗号化されます。 また、[Event Grid の再試行ポリシー](delivery-and-retry.md)に従って、イベントまたはデータが保持される最大期間は 24 時間です。 Event Grid では、24時間またはイベントの Time-To-Live のいずれか少ない方が経過すると、すべてのイベントまたはデータが自動的に削除されます。

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Webhook エンドポイントへのイベント配信の認証
以下のセクションでは、Webhook エンドポイントへのイベント配信を認証する方法について説明します。 使用する方法に関係なく、検証ハンドシェイク メカニズムを使用する必要があります。 詳細については、「[Webhook のイベント配信](webhook-event-delivery.md)」を参照してください。 

### <a name="using-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) を使用する
Event Grid の認証に Azure Active Directory (Azure AD) を使用することで Webhook エンドポイントを保護し、エンドポイントにイベントを配信できます。 Azure AD アプリケーションを作成すること、アプリケーションで Event Grid を認証するためのロールとサービス プリンシパルを作成すること、Azure AD アプリケーションを使用するようにイベント サブスクリプションを構成することが必要になります。 [Event Grid を使用した Azure Active Directory の構成方法について説明します](secure-webhook-delivery.md)。

### <a name="using-client-secret-as-a-query-parameter"></a>クエリ パラメーターとしてクライアント シークレットを使用する
イベント サブスクリプションを作成するときに、webhook URL にクエリ パラメーターを追加することで、webhook エンドポイントをセキュリティで保護できます。 これらのクエリ パラメーターのいずれかを、[アクセス トークン](https://en.wikipedia.org/wiki/Access_token)などのクライアント シークレットまたは共有シークレットに設定します。 Webhook はシークレットを使用して、イベントが有効なアクセス許可を持つ Event Grid からのものであることを認識できます。 Event Grid には、webhook へのすべてのイベント配信にこれらのクエリ パラメーターが含められます。 クライアント シークレットが更新された場合は、イベント サブスクリプションも更新する必要があります。 このシークレットのローテーション中の配信エラーを回避するには、期限を限定して、古いシークレットと新しいシークレットの両方を Webhook に受け入れてください。 

クエリ パラメーターにはクライアント シークレットを含めることができるため、特別な注意を払って処理されます。 これらは暗号化されて格納され、サービス オペレーターがアクセスすることはできません。 これらは、サービス ログ/トレースの一部として記録されません。 イベント サブスクリプションを編集すると、Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) で [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) パラメーターを使用した場合を除き、クエリ パラメーターが表示されなくなるか、返されなくなります。

Webhook へのイベント配信の詳細については、「[Webhook のイベント配信](webhook-event-delivery.md)」を参照してください。

> [!IMPORTANT]
Azure Event Grid は、**HTTPS** Webhook エンドポイントのみをサポートします。 

## <a name="next-steps"></a>次のステップ

- Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
