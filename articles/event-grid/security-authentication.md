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
ms.openlocfilehash: bca450022322db7a7569fa1dc7ce80ec75a9ce69
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774303"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Azure Event Grid リソースへのアクセスの認証
この記事では、次のシナリオについて説明します。  

- Shared Access Signature (SAS) またはキーを使用して Azure Event Grid トピックにイベントを発行するクライアントを認証します。 
- Azure Active Directory (Azure AD) または共有シークレットを使用して、Event Grid からイベントを受信するために使用される Webhook エンドポイントをセキュリティで保護します。

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>SAS またはキーを使用した発行クライアントの認証
カスタム トピックは、Shared Access Signature (SAS) またはキー認証を使用します。 SAS が推奨されますが、キー認証はプログラミングが簡単で、既存の多くの webhook 発行元と互換性があります。

認証値は、HTTP ヘッダーに含めます。 SAS の場合は、ヘッダー値に **aeg-sas-token** を使用します。 キー認証の場合は、ヘッダー値に **aeg-sas-key** を使用します。

### <a name="key-authentication"></a>キー認証

キー認証は、最も単純な形式の認証です。 メッセージ ヘッダーで、`aeg-sas-key: <your key>` という形式を使用します。

たとえば、次のようにキーを渡します。

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

クエリ パラメーターとして `aeg-sas-key` を指定することもできます。 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
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
Azure AD を使用して、Event Grid からイベントを受信するために使用される Webhook エンドポイントをセキュリティで保護できます。 Azure AD アプリケーションを作成し、アプリケーションで Event Grid を承認するためのロールとサービス プリンシパルを作成する必要があります。さらに、Azure AD アプリケーションを使用するようにイベント サブスクリプションを構成する必要があります。 Event Grid で Azure Active Directory を構成する方法については、[こちら](secure-webhook-delivery.md)をご覧ください。

### <a name="using-client-secret-as-a-query-parameter"></a>クエリ パラメーターとしてクライアント シークレットを使用する
イベント サブスクリプションの作成の一環として指定したWebhook の送信先 URL にクエリ パラメーターを追加することで、Webhook エンドポイントをセキュリティで保護することもできます。 クエリ パラメーターのいずれかを、[アクセス トークン](https://en.wikipedia.org/wiki/Access_token)などのクライアント シークレットまたは共有シークレットとして設定します。 Event Grid サービスでは、Webhook へのすべてのイベント配信要求にすべてのクエリ パラメーターを含めます。 Webhook サービスはシークレットを取得して検証できます。 クライアント シークレットが更新された場合は、イベント サブスクリプションも更新する必要があります。 このシークレットのローテーション中の配信エラーを回避するために、イベント サブスクリプションを新しいシークレットで更新する前に、一定期間、Webhook で古いシークレットと新しいシークレットの両方を受け入れるようにします。 

クエリ パラメーターにはクライアント シークレットを含めることができるため、特別な注意を払って処理されます。 これらは暗号化されて格納され、サービス オペレーターがアクセスすることはできません。 これらは、サービス ログ/トレースの一部として記録されません。 イベント サブスクリプションのプロパティを取得するときに、送信先クエリ パラメーターは既定では返されません。 たとえば、[--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) パラメーターは、Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 内で使用されます。

Webhook へのイベント配信の詳細については、「[Webhook のイベント配信](webhook-event-delivery.md)」を参照してください。

> [!IMPORTANT]
Azure Event Grid は、**HTTPS** Webhook エンドポイントのみをサポートします。 

## <a name="next-steps"></a>次のステップ

- Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
