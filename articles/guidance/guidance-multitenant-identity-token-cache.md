<properties
   pageTitle="マルチテナント アプリケーションでアクセス トークンをキャッシュする | Microsoft Azure"
   description="バックエンド Web API を呼び出すために使用されるアクセス トークンのキャッシュ"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>


# マルチテナント アプリケーションでアクセス トークンをキャッシュする

この記事は[シリーズの一部]です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

トークン エンドポイントに対する HTTP 要求が必要なので、OAuth アクセス トークンの取得は比較的コストが高い処理です。そのため、可能な限りトークンをキャッシュすることをお勧めします。[Azure AD Authentication Library][ADAL] (ADAL) は、更新トークンなど、Azure AD から取得したトークンを自動的にキャッシュします。

ADAL には、既定のトークン キャッシュ機能が実装されています。ただし、このトークン キャッシュは、ネイティブ クライアント アプリ用なので、Web アプリには_適していません_。

-	また、静的インスタンスなので、スレッド セーフではありません。
-	すべてのユーザーのトークンは同じディレクトリに保存されるので、多数のユーザーに合わせて拡張されません。
-	フォーム内の Web サーバー全体で共有することはできません。

代わりに、ADAL `TokenCache` クラスから派生したカスタムのトークン キャッシュを実装する必要がありますが、サーバー環境に適しており、異なるユーザーのトークン間を適度なレベルで分離できます。

`TokenCache` クラスはトークンのディクショナリを保存します。このディクショナリは、発行者、リソース、クライアント ID、ユーザーでインデックスが作成されます。カスタム トークン キャッシュは、Redis Cache などのバッキング ストアにこのディクショナリを書き込む必要があります。

Tailspin Surveys アプリケーションでは、`DistributedTokenCache` クラスがトークン キャッシュを実装します。この実装では、ASP.NET Core 1.0 の [IDistributedCache][distributed-cache] 抽象化を使用します。このように、`IDistributedCache` 実装はバッキング ストアとして使用できます。

-	Surveys アプリの既定では、Redis Cache を使用します。
-	単一インスタンスの Web サーバーの場合、ASP.NET Core 1.0 の [in-memory cache][in-memory-cache] を使用できます(この方法は、開発時にローカルでアプリを実行する場合にもお勧めです)。

> [AZURE.NOTE] 現在、.NET Core は Redis Cache をサポートしていません。

`DistributedTokenCache` は、キー/値ペアとしてキャッシュ データをバッキング ストアに保存します。キーはユーザー ID + クライアント ID なので、ユーザー/クライアントの一意の組み合わせごとに異なるキャッシュ データがバッキング ストアに保存されます。

![トークンのキャッシュ](media/guidance-multitenant-identity/token-cache.png)

バッキング ストアはユーザーごとにパーティション分割されます。各 HTTP 要求について、そのユーザーのトークンがバッキング ストアから読み取られ、`TokenCache` ディクショナリに読み込まれます。Redis をバッキング ストアとして使用する場合、サーバー ファームの各サーバー インスタンスは、同じキャッシュに対して読み取り/書き込みを行います。このアプローチは多数のユーザーに合わせて拡張できます。

## キャッシュされたトークンを暗号化する

トークンは、ユーザーのリソースに対するアクセス権を付与するため、機密性の高いデータです(さらに、ユーザーのパスワードとは異なり、トークンのハッシュを単純に保存することはできません)。 したがって、トークンが漏えいしないように保存することが重要です。Redis でサポートされるキャッシュはパスワードで保護されますが、誰かがパスワードを入手すると、キャッシュされたアクセス トークンもすべて入手できる可能性があります。そのため、`DistributedTokenCache` はバッキング ストアに書き込まれたすべてのデータを暗号化します。暗号化は ASP.NET Core 1.0 の[データ保護][data-protection] API を使用して行われます。

> [AZURE.NOTE] Azure Web サイトにデプロイすると、暗号化キーはネットワーク ストレージにバックアップされ、すべてのコンピューターで同期されます ([キー管理][key-management]に関するページを参照してください)。既定では、Azure Web サイトで実行するときにキーは暗号化されませんが、[X.509 証明書を使用する暗号化を有効にする][x509-cert-encryption]ことはできます。


## DistributedTokenCache の実装

[DistributedTokenCache][DistributedTokenCache] クラスは、ADAL [TokenCache][tokencache-class] クラスから派生します。

コンストラクターの `DistributedTokenCache` クラスによって、現在のユーザーのキーが作成され、バッキング ストアからキャッシュが読み込まれます。

```csharp
public DistributedTokenCache(
    ClaimsPrincipal claimsPrincipal,
    IDistributedCache distributedCache,
    ILoggerFactory loggerFactory,
    IDataProtectionProvider dataProtectionProvider)
    : base()
{
    _claimsPrincipal = claimsPrincipal;
    _cacheKey = BuildCacheKey(_claimsPrincipal);
    _distributedCache = distributedCache;
    _logger = loggerFactory.CreateLogger<DistributedTokenCache>();
    _protector = dataProtectionProvider.CreateProtector(typeof(DistributedTokenCache).FullName);
    AfterAccess = AfterAccessNotification;
    LoadFromCache();
}
```

キーは、ユーザー ID とクライアント ID を連結して作成されます。この両方の ID は、ユーザーの `ClaimsPrincipal` で見つかった要求から取得されます。

```csharp
private static string BuildCacheKey(ClaimsPrincipal claimsPrincipal)
{
    string clientId = claimsPrincipal.FindFirstValue("aud", true);
    return string.Format(
        "UserId:{0}::ClientId:{1}",
        claimsPrincipal.GetObjectIdentifierValue(),
        clientId);
}
```

キャッシュ データを読み込むには、バッキング ストアからシリアル化された BLOB を読み取り、`TokenCache.Deserialize` を呼び出して BLOB をキャッシュ データに変換します。

```csharp
private void LoadFromCache()
{
    byte[] cacheData = _distributedCache.Get(_cacheKey);
    if (cacheData != null)
    {
        this.Deserialize(_protector.Unprotect(cacheData));
    }
}
```

ADAL がキャッシュにアクセスするたびに、`AfterAccess` イベントが発生します。キャッシュ データが変更されると、`HasStateChanged` プロパティは true になります。その場合、バッキング ストアを更新して変更を反映し、`HasStateChanged` を false に設定します。

```csharp
public void AfterAccessNotification(TokenCacheNotificationArgs args)
{
    if (this.HasStateChanged)
    {
        try
        {
            if (this.Count > 0)
            {
                _distributedCache.Set(_cacheKey, _protector.Protect(this.Serialize()));
            }
            else
            {
                // There are no tokens for this user/client, so remove the item from the cache.
                _distributedCache.Remove(_cacheKey);
            }
            this.HasStateChanged = false;
        }
        catch (Exception exp)
        {
            _logger.WriteToCacheFailed(exp);
            throw;
        }
    }
}
```

TokenCache からは、他にも 2 つのイベントが送信されます。

- `BeforeWrite`ADAL がキャッシュに書き込む直前に呼び出されます。これを利用して、同時実行戦略を実装できます。
- `BeforeAccess`ADAL がキャッシュから読み取る直前に呼び出されます。ここでキャッシュを再読み込みして、最新バージョンを取得することができます。

この例では、これら 2 つのイベントを処理していません。

- 同時実行の場合、最後の書き込みが有効になりますが、問題ありません。ユーザー + クライアントごとにトークンは独立して保存されるので、同一ユーザーが同時にログイン セッションを実行した場合にのみ、競合が発生します。
- 読み取りについては、要求ごとにキャッシュを読み込みます。要求の存続期間は短期です。存続期間内にキャッシュが変更された場合、次の要求は新しい値を選択します。

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[data-protection]: https://docs.asp.net/en/latest/security/data-protection/index.html
[distributed-cache]: https://docs.asp.net/en/latest/fundamentals/distributed-cache.html
[DistributedTokenCache]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.TokenStorage/DistributedTokenCache.cs
[key-management]: https://docs.asp.net/en/latest/security/data-protection/configuration/default-settings.html
[in-memory-cache]: https://docs.asp.net/en/latest/fundamentals/caching.html
[tokencache-class]: https://msdn.microsoft.com/library/azure/microsoft.identitymodel.clients.activedirectory.tokencache.aspx
[x509-cert-encryption]: https://docs.asp.net/en/latest/security/data-protection/implementation/key-encryption-at-rest.html#x-509-certificate
[シリーズの一部]: guidance-multitenant-identity.md
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->