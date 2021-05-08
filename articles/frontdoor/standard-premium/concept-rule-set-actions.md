---
title: Azure Front Door Standard または Premium のルール セットのアクションを構成する
description: この記事では、Azure Front Door のルール セットを使用して実行できるさまざまなアクションの一覧を示します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e4698a1c1576d15042dd050e0123b83dba39a3e3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064753"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-actions"></a>Azure Front Door Standard/Premium (プレビュー) のルール セットのアクション

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

Azure Front Door Standard/Premium の[ルール セット](concept-rule-set.md)は、一致条件とアクションを組み合わせたルールで構成されます。 この記事では、Azure Front Door Standard/Premium のルール セットで使用できるアクションについて詳しく説明します。 アクションには、一致条件によって識別された要求の種類に適用される動作が定義されています。 Azure Front Door (Standard/Premium) のルール セットでは、1 つのルールに最大 5 つのアクションを含めることができます。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Front Door のルール セットでは、次のアクションを使用できます。  

## <a name="cache-expiration"></a><a name="CacheExpiration"></a> キャッシュの有効期限

ルールの一致条件で指定された要求のエンドポイントの Time to Live (TTL) の値を上書きするには、**キャッシュの有効期限** アクションを使用します。

> [!NOTE]
> 配信元では、値が `no-cache`、`private`、`no-store` の `Cache-Control` ヘッダーを使用して、特定の応答をキャッシュしないように指定できます。 このような状況では、フロント ドアによってコンテンツがキャッシュされることはなく、このアクションによる影響はありません。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-------|------------------|
| キャッシュの動作 | <ul><li>**キャッシュのバイパス:** コンテンツをキャッシュしないようにします。 ARM テンプレートで、`cacheBehavior` プロパティを `BypassCache` に設定します。</li><li>**オーバーライド:** 配信元から返された TTL 値は、アクションに指定された値で上書きされます。 この動作は、応答がキャッシュ可能な場合にのみ適用されます。 ARM テンプレートで、`cacheBehavior` プロパティを `Override` に設定します。</li><li>**見つからない場合に設定:** 配信元から TTL 値が返されない場合、TTL は、アクションに指定された値に設定されます。 この動作は、応答がキャッシュ可能な場合にのみ適用されます。 ARM テンプレートで、`cacheBehavior` プロパティを `SetIfMissing` に設定します。</li></ul> |
| Cache duration (キャッシュ期間) | _キャッシュ動作_ が `Override` または `Set if missing` に設定されている場合、これらのフィールドでは、使用するキャッシュ期間を指定する必要があります。 最大期間は 366 日です。<ul><li>Azure portal: 曜日、時間、分、秒を指定します。</li><li>ARM テンプレート: 形式 `d.hh:mm:ss` で期間を指定します。 |

### <a name="example"></a>例

この例では、キャッシュ期間がまだ指定されていない一致する要求の場合、キャッシュの有効期限を 6 時間に上書きします。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-expiration.png" alt-text="キャッシュの有効期限アクションを示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a><a name="CacheKeyQueryString"></a> キャッシュ キーのクエリ文字列

クエリ文字列に基づいてキャッシュ キーを変更するには、**キャッシュ キーのクエリ文字列** アクションを使用します。 キャッシュ キーは、キャッシュに対する一意の要求をフロント ドアで識別する方法です。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-------|------------------|
| 動作 | <ul><li>**含む:** キャッシュ キーが生成されるとき、パラメーターに指定されたクエリ文字列が含まれます。 ARM テンプレートで、`queryStringBehavior` プロパティを `Include` に設定します。</li><li>**一意の URL をすべてキャッシュ:** 一意の各 URL には、それぞれ独自のキャッシュ キーがあります。 ARM テンプレートでは、`IncludeAll` の `queryStringBehavior` を使用します。</li><li>**除外:** キャッシュ キーが生成されるとき、パラメーターに指定されたクエリ文字列が除外されます。 ARM テンプレートで、`queryStringBehavior` プロパティを `Exclude` に設定します。</li><li>**クエリ文字列を無視:** キャッシュ キーが生成されるとき、クエリ文字列は考慮されません。 ARM テンプレートで、`queryStringBehavior` プロパティを `ExcludeAll` に設定します。</li></ul>  |
| パラメーター | コンマで区切られた、クエリ文字列パラメーターの名前の一覧。 |

### <a name="example"></a>例

この例では、キャッシュ キーを変更して、`customerId` という名前のクエリ文字列パラメーターを含めます。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-key-query-string.png" alt-text="キャッシュ キーのクエリ文字列アクションを示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a><a name="ModifyRequestHeader"></a> 要求ヘッダーの変更

**要求ヘッダーの変更** アクションを使用して、配信元に送信されるときに、ヘッダーを変更します。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-------|------------------|
| 演算子 | <ul><li>**追加:** 指定されたヘッダーが、指定された値で要求に追加されます。 ヘッダーが既に存在する場合は、文字列の連結を使用して、既存のヘッダー値に値が追加されます。 区切り記号は追加されません。 ARM テンプレートでは、`Append` の `headerAction` を使用します。</li><li>**上書き:** 指定されたヘッダーが、指定された値で要求に追加されます。 ヘッダーが既に存在する場合、指定された値によって既存の値が上書きされます。 ARM テンプレートでは、`Overwrite` の `headerAction` を使用します。</li><li>**削除:** ルールに指定されたヘッダーがある場合、ヘッダーは要求から削除されます。 ARM テンプレートでは、`Delete` の `headerAction` を使用します。</li></ul> |
| ヘッダー名 | 変更するヘッダーの名前。 |
| ヘッダー値 | 追加または上書きする値。 |

### <a name="example"></a>例

この例では、`MyRequestHeader` 要求ヘッダーに値 `AdditionalValue` を追加します。 配信元で応答ヘッダーの値を `ValueSetByClient` に設定した場合、このアクションが適用されると、要求ヘッダーの値は `ValueSetByClientAdditionalValue` になります。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-request-header.png" alt-text="要求ヘッダーの変更アクションを示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a><a name="ModifyResponseHeader"></a> 応答ヘッダーの変更

**応答ヘッダーの変更** アクションを使用して、応答に存在するヘッダーをクライアントに返す前に変更します。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|-------|------------------|
| 演算子 | <ul><li>**追加:** 指定されたヘッダーが、指定された値で応答に追加されます。 ヘッダーが既に存在する場合は、文字列の連結を使用して、既存のヘッダー値に値が追加されます。 区切り記号は追加されません。 ARM テンプレートでは、`Append` の `headerAction` を使用します。</li><li>**上書き:** 指定されたヘッダーが、指定された値で応答に追加されます。 ヘッダーが既に存在する場合、指定された値によって既存の値が上書きされます。 ARM テンプレートでは、`Overwrite` の `headerAction` を使用します。</li><li>**削除:** ルールに指定されたヘッダーがある場合、ヘッダーは応答から削除されます。  ARM テンプレートでは、`Delete` の `headerAction` を使用します。</li></ul> |
| ヘッダー名 | 変更するヘッダーの名前。 |
| ヘッダー値 | 追加または上書きする値。 |

### <a name="example"></a>例

この例では、クライアントに返される前に、名前が `X-Powered-By` の持つヘッダーを応答から削除します。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-response-header.png" alt-text="応答ヘッダーの変更アクションを示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a><a name="UrlRedirect"></a> URL リダイレクト

**URL リダイレクト** アクションを使用して、クライアントを新しい URL にリダイレクトします。 クライアントには、フロント ドアからのリダイレクト応答が送信されます。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|----------|------------------|
| リダイレクトの種類 | 要求元に返される応答タイプ。 <ul><li>Azure portal: 検出済み (302)、移動済み (301)、一時リダイレクト (307)、永続的なリダイレクト (308)。</li><li>ARM テンプレート: `Found`、`Moved`、`TemporaryRedirect`、`PermanentRedirect`</li></ul> |
| プロトコルをリダイレクトします | <ul><li>Azure portal: `Match Request`、`HTTP`、`HTTPS`</li><li>ARM テンプレート: `MatchRequest`、`Http`、`Https`</li></ul> |
| 宛先ホスト | 要求のリダイレクト先のホスト名。 受信ホストを保持するには、空のままにします。 |
| 宛先のパス | リダイレクトで使用するパス。 先頭に `/` を含めます。 受信パスを保持するには、空のままにします。 |
| クエリ文字列 | リダイレクトで使用されるクエリ文字列。 先頭に `?` を含めないでください。 受信クエリ文字列を保持するには、空のままにします。 |
| 宛先フラグメント | リダイレクトで使用するフラグメント。 受信フラグメントを保持するには、空のままにします。 |

### <a name="example"></a>例

この例では、フラグメントを保持しながら、要求を `https://contoso.com/exampleredirection?clientIp={client_ip}` にリダイレクトします。 HTTP 一時リダイレクト (307) が使用されます。 `client_ip` [サーバー変数](#server-variables)を使用すると、URL 内の `{client_ip}` トークンの代わりに、クライアントの IP アドレスが使用されます。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-redirect.png" alt-text="URL リダイレクト アクションを示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a><a name="UrlRewrite"></a> URL 書き換え

**URL 書き換え** アクションを使用して、配信元に送信中の要求のパスを書き換えます。

### <a name="properties"></a>Properties

| プロパティ | サポート状況の値 |
|----------|------------------|
| ソース パターン | 置換する URL パス内のソース パターンを定義します。 現在、ソース パターンではプレフィックスに基づく一致が使用されます。 すべての URL パスを一致させるには、ソース パターン値としてスラッシュ ( `/` ) を使用します。 |
| 宛先 | 書き換えに使用する宛先パスを定義します。 宛先のパスは、ソース パターンを上書きします。 |
| 一致しないパスを保持する | _[はい]_ に設定された場合、ソース パターンの後の残りのパスが新しい宛先パスに追加されます。 |

### <a name="example"></a>例

この例では、すべての要求をパス `/redirection` に書き換え、パスの残りの部分は保持しません。

# <a name="portal"></a>[ポータル](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-rewrite.png" alt-text="URL 書き換えアクションを示すポータルのスクリーンショット。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="server-variables"></a>サーバー変数

ルール セット サーバー変数では、要求に関する構造化情報にアクセスできます。 新しいページが読み込まれるときや、フォームがポストされるときなどに、要求または応答ヘッダーや URL 書き換えパスまたはクエリ文字列を動的に変更するためのサーバー変数を使用できます。

### <a name="supported-variables"></a>サポートされる変数

| 変数名    | 説明                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | Azure Front Door エッジへの直接接続の IP アドレス。 クライアントが HTTP プロキシまたはロード バランサーを使用して要求を送信した場合、`socket_ip` の値はプロキシまたはロード バランサーの IP アドレスです。                                                                      |
| `client_ip`      | 元の要求を行ったクライアントの IP アドレス。 要求に `X-Forwarded-For` ヘッダーがあった場合、ヘッダーからクライアント IP が選択されます。                                                                                                               |
| `client_port`    | 要求を行ったクライアントの IP アドレス。                                                                                                                                                                                                                                          |
| `hostname`       | クライアントからの要求に含まれるホスト名。                                                                                                                                                                                                                                             |
| `geo_country`    | 国および地域コードによって配信元の要求者の国および地域を示します。                                                                                                                                                                                                       |
| `http_method`    | URL 要求を行うために使用されたメソッド (`GET` や `POST` など)。                                                                                                                                                                                                                         |
| `http_version`   | 要求プロトコル。 通常は `HTTP/1.0`、`HTTP/1.1`、`HTTP/2.0` です。                                                                                                                                                                                                                      |
| `query_string`   | 要求された URL 内で "?" の後にある、変数と値のペアから成る一覧。<br />例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` では、`query_string` 値は `id=123&title=fabrikam` になります。                                                      |
| `request_scheme` | 要求スキーム: `http` または `https`。                                                                                                                                                                                                                                                    |
| `request_uri`    | 完全な元の要求 URI (引数を含む)。<br />例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` では、`request_uri` 値は `/article.aspx?id=123&title=fabrikam` になります。                                                                     |
| `ssl_protocol`   | 確立された TLS 接続のプロトコル。                                                                                                                                                                                                                                            |
| `server_port`    | 要求を受け付けたサーバーのポート。                                                                                                                                                                                                                                           |
| `url_path`       | Web クライアントがアクセスする必要があるホスト内の特定のリソースを識別します。 これは、引数を含まない要求 URI の部分です。<br />例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` では、`uri_path` 値は `/article.aspx` になります。 |

### <a name="server-variable-format"></a>サーバー変数の形式    

サーバー変数は、次の形式を使用して指定できます。

* `{variable}`: サーバー変数全体を含めます。 たとえば、クライアントの IP アドレスが `111.222.333.444` の場合、`{client_ip}` トークンは `111.222.333.444` に評価されます。
* `{variable:offset}`: 特定のオフセットの後から、変数の末尾まで、サーバー変数を含めます。 オフセットは 0 から始まります。 たとえば、クライアントの IP アドレスが `111.222.333.444` の場合、`{client_ip:3}` トークンは `.222.333.444` に評価されます。
* `{variable:offset:length}`: 特定のオフセットの後から、指定した長さまで、サーバー変数を含めます。 オフセットは 0 から始まります。 たとえば、クライアントの IP アドレスが `111.222.333.444` の場合、`{client_ip:4:3}` トークンは `222` に評価されます。

### <a name="supported-actions"></a>サポートされているアクション

サーバー変数は、次のアクションでサポートされています。

* キャッシュ キーのクエリ文字列
* 要求ヘッダーの変更
* 応答ヘッダーの変更
* URL リダイレクト
* URL 書き換え

## <a name="next-steps"></a>次のステップ

* [Azure Front Door Standard/Premium ルール セットについて](concept-rule-set.md)説明します。
* [ルート セットの一致条件](concept-rule-set-match-conditions.md)の詳細について説明します。
