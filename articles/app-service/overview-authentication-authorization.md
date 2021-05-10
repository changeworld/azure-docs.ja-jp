---
title: 認証と権限承認
description: Azure App Service および Azure Functions での組み込みの認証と認可のサポート、およびそれが認可されていないアクセスからアプリを保護するのにどのように役立つかについて説明します。
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 03/29/2021
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 1b6e600fcaf32a115af14be2444144fee099d635
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075340"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Azure App Service および Azure Functions での認証と承認

Azure App Service は組み込みの認証と認可の機能 (Easy Auth\(簡単認証)\ と呼ばれることもあります) を提供するので、Web アプリ、RESTful API、モバイル バックエンド、さらには [Azure Functions](../azure-functions/functions-overview.md) でも、最小限のコードを記述するだけで、またはまったく記述せずに、ユーザーのサインインとデータへのアクセスを可能にできます。 この記事では、App Service によりアプリの認証と認可を簡略化する方法について説明します。

## <a name="why-use-the-built-in-authentication"></a>組み込みの認証を使用する理由

認証と認可にこの機能を必ずしも使う必要はありません。 選択した Web フレームワークにバンドルされているセキュリティ機能を使用するか、独自のユーティリティを作成することができます。 ただし、最新のセキュリティ、プロトコル、ブラウザーの更新プログラムを使用して、ソリューションが最新の状態に保たれていることを確認する必要があります。

認証 (サインイン ユーザー) と認可 (セキュリティで保護されたデータへのアクセスの提供) に対して、セキュリティで保護されたソリューションを実装するには、かなりの労力が必要になることがあります。 業界のベスト プラクティスと標準に従って、実装を最新の状態に保つ必要があります。 App Service と Azure Functions 用の組み込みの認証機能では、すぐに使用できる認証をフェデレーション ID プロバイダーに提供することで、時間と労力を節約できます。これにより、アプリケーションの残りの部分に専念できます。

- Azure App Service では、独自に実装せずに、さまざまな認証機能を Web アプリまたは API に統合できます。
- これはプラットフォームに直接組み込まれており、特定の言語、SDK、セキュリティの専門知識、使用するコードも必要ありません。
- 複数のログイン プロバイダーを統合できます。 たとえば、Azure AD、Facebook、Google、Twitter などです。

## <a name="identity-providers"></a>ID プロバイダー

App Service が使用する[フェデレーション ID](https://en.wikipedia.org/wiki/Federated_identity) では、サード パーティの ID プロバイダーが代わりにユーザー ID と認証フローを管理します。 次の ID プロバイダーを既定で利用できます。

| プロバイダー | サインイン エンドポイント | 使用方法に関するガイダンス |
| - | - | - |
| [Microsoft ID プラットフォーム](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [App Service Microsoft ID プラットフォーム ログイン](configure-authentication-provider-aad.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [App Service Facebook ログイン](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [App Service Google ログイン](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [App Service Twitter ログイン](configure-authentication-provider-twitter.md) |
| 任意の [OpenID Connect](https://openid.net/connect/) プロバイダー (プレビュー) | `/.auth/login/<providerName>` | [App Service OpenID Connect ログイン](configure-authentication-provider-openid-connect.md) |

これらのプロバイダーのいずれかで認証と認可を有効にすると、そのプロバイダーのサインイン エンドポイントが、ユーザー認証と、プロバイダーからの認証トークンの検証に使用できるようになります。 任意の数のサインイン オプションを、ユーザーに対して提供できます。

## <a name="considerations-for-using-built-in-authentication"></a>組み込みの認証の使用に関する考慮事項

この機能を有効にすると、HTTPS を適用するための App Service 構成設定に関係なく、アプリケーションへの要求がすべて HTTPS に自動的にリダイレクトされます。 これは、V2 構成の  `requireHttps` 設定で無効にすることができます。 ただし、HTTPS を使用することをお勧めします。セキュリティで保護されていない HTTP 接続でセキュリティ トークンが送信されないようにしてください。

App Service は、サイトのコンテンツと API へのアクセスを制限することなく、認証に使用できます。 認証されたユーザーのみにアプリのアクセスを制限するには、構成された ID プロバイダーのいずれかでログインできるように **[要求が認証されない場合に実行するアクション]** を設定します。 アクセスを制限しないで認証するには、 **[要求が認証されない場合に実行するアクション]** を [匿名要求を許可する (操作不要)] に設定します。

> [!NOTE]
> 各アプリの登録には、独自のアクセス許可と同意が割り当てられるようにしてください。 デプロイ スロットごとに個別のアプリ登録を使用することで、環境間でアクセス許可を共有することを回避します。 新しいコードをテストするとき、このプラクティスは、問題が運用アプリに影響を与えることを回避する上で役立つことがあります。

## <a name="how-it-works"></a>しくみ

[Windows の機能のアーキテクチャ (コンテナー以外のデプロイ)](#feature-architecture-on-windows-non-container-deployment)

[Linux およびコンテナーの機能のアーキテクチャ](#feature-architecture-on-linux-and-containers)

[Authentication flow](#authentication-flow)

[認可の動作](#authorization-behavior)

[ユーザーおよびアプリケーション要求](#user-and-application-claims)

[トークン ストア](#token-store)

[ログとトレース](#logging-and-tracing)

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Windows の機能のアーキテクチャ (コンテナー以外のデプロイ)

認証と認可のモジュールは、アプリケーションのコードと同じサンドボックスで実行します。 有効になっている場合、すべての受信 HTTP 要求は、アプリケーション コードによって処理される前に、認証と認可のモジュールを通過します。

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="デプロイされたサイトへのトラフィックを許可する前に、ID プロバイダーと対話するサイト サンドボックス内のプロセスによってインターセプトされる要求を示すアーキテクチャ図" lightbox="media/app-service-authentication-overview/architecture.png":::

このモジュールは、アプリのためにいくつかの処理を行います。

- 指定されたプロバイダーでユーザーを認証します
- トークンを検証、格納、更新します
- 認証されたセッションを管理します
- 要求ヘッダーに ID 情報を挿入します

このモジュールはアプリケーションのコードとは別に実行され、アプリの設定を使って構成されます。 SDK、特定の言語、またはアプリケーションのコードの変更は必要ありません。 

#### <a name="feature-architecture-on-linux-and-containers"></a>Linux およびコンテナーの機能のアーキテクチャ

認証と承認のモジュールは、アプリケーションのコードから分離された別のコンテナーで実行されます。 [アンバサダー パターン](/azure/architecture/patterns/ambassador)と呼ばれるものを使用して、Windows と同様の機能を実行するために、受信トラフィックと対話します。 インプロセスでは実行されないため、特定の言語フレームワークと直接統合することはできません。ただし、以下で説明するように、アプリに必要な関連情報は、要求ヘッダーを使用して渡されます。

#### <a name="authentication-flow"></a>Authentication flow

認証フローは、プロバイダーによる違いはありませんが、プロバイダーの SDK でサインインするかどうかによって異なります。

- プロバイダーの SDK を使わない場合:アプリケーションは、フェデレーション サインインを App Service に委任します。 これはブラウザー アプリで通常のケースであり、プロバイダーのログイン ページをユーザーに表示することができます。 サーバーのコードがサインイン プロセスを管理するので、"_サーバー主導のフロー_" または "_サーバー フロー_" とも呼ばれます。 このケースはブラウザー アプリに適用されます。 また、Mobile Apps クライアント SDK を使ってユーザーをサインインさせるネイティブ アプリにも適用されます。その場合は、SDK が Web ビューを開いて App Service 認証でユーザーをサインインさせます。
- プロバイダーの SDK を使う場合:アプリケーションは、ユーザーを手動でプロバイダーにサインインさせてから、検証のために App Service に認証トークンを送信します。 これはブラウザーレス アプリで通常のケースであり、プロバイダーのサインイン ページをユーザーに表示することはできません。 アプリケーションのコードがサインイン プロセスを管理するので、"_クライアント主導のフロー_" または "_クライアント フロー_" とも呼ばれます。 このケースは、REST API、[Azure Function](../azure-functions/functions-overview.md)、JavaScript ブラウザー クライアント、およびいっそう柔軟なサインイン プロセスを必要とするブラウザー アプリに適用されます。 また、プロバイダーの SDK を使ってユーザーをサインインさせるネイティブ モバイル アプリにも適用されます。

App Service または [Azure Functions](../azure-functions/functions-overview.md) の別の REST API への App Service 内の信頼されたブラウザー アプリからの呼び出しは、サーバー主導のフローを使って認証することができます。 詳細については、「[Azure App Service での認証と認可のカスタマイズ](app-service-authentication-how-to.md)」を参照してください。

次の表では、認証フローの手順を示します。

| 手順 | プロバイダーの SDK を使わない場合 | プロバイダーの SDK を使う場合 |
| - | - | - |
| 1.ユーザーをサインインさせる | クライアントを `/.auth/login/<provider>` にリダイレクトします。 | クライアント コードはプロバイダーの SDK でユーザーを直接サインインさせ、認証トークンを受け取ります。 詳しくは、プロバイダーのドキュメントをご覧ください。 |
| 2.認証をポストする | プロバイダーはクライアントを `/.auth/login/<provider>/callback` にリダイレクトします。 | クライアント コードは検証のために[プロバイダーからのトークンを `/.auth/login/<provider>` にポスト](app-service-authentication-how-to.md#validate-tokens-from-providers)します。 |
| 3.認証済みのセッションを確立する | App Service は認証された Cookie を応答に追加します。 | App Service は独自の認証トークンをクライアント コードに返します。 |
| 4.認証済みのコンテンツを提供する | クライアントは以降の要求に認証クッキーを含めます (ブラウザーによって自動的に処理されます)。 | クライアント コードは `X-ZUMO-AUTH` ヘッダーで認証トークンを提示します (Mobile Apps クライアント SDK によって自動的に処理されます)。 |

クライアント ブラウザーの場合、App Service は認証されていないすべてのユーザーを `/.auth/login/<provider>` に自動的に送ることができます。 また、ユーザーが選んだプロバイダーを使ってアプリにサインインするための 1 つまたは複数の `/.auth/login/<provider>` リンクをユーザーに表示することもできます。

<a name="authorization"></a>

#### <a name="authorization-behavior"></a>認可の動作

[Azure portal](https://portal.azure.com) では、受信要求が認証されていない場合、複数の動作で App Service を構成することができます。 以下の見出しではそれらのオプションを説明します。

**認証されていない要求を許可する**

このオプションでは、認証されていないトラフィックの認証がアプリケーション コードに委ねられます。 認証された要求について、App Service は HTTP ヘッダーで認証情報も渡します。

このオプションでは、匿名要求をいっそう柔軟に処理できます。 たとえば、ユーザーに[複数のサインイン プロバイダーを提示する](app-service-authentication-how-to.md#use-multiple-sign-in-providers)ことができます。 ただし、コードを記述する必要があります。

**認証を必須にする**

このオプションでは、アプリケーションへの認証されていないトラフィックを拒否します。 この拒否は、構成されているいずれかの ID プロバイダーへのリダイレクト操作になります。 このような場合は、選択したプロバイダーの `/.auth/login/<provider>` にブラウザー クライアントがリダイレクトされます。 匿名要求がネイティブ モバイル アプリからのものである場合、返される応答は `HTTP 401 Unauthorized` です。 すべての要求に対して `HTTP 401 Unauthorized` または `HTTP 403 Forbidden` になるように拒否を構成することもできます。

このオプションを使用すると、アプリで認証コードを記述する必要はまったくありません。 役割固有の認可などのさらに細かい認可は、ユーザーの要求を調べることで処理できます (「[ユーザー要求へのアクセス](app-service-authentication-how-to.md#access-user-claims)」をご覧ください)。

> [!CAUTION]
> この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホーム ページを必要とするアプリには適切でない場合があります。

> [!NOTE]
> 既定では、Azure AD テナント内のすべてのユーザーが Azure AD にアプリケーションのトークンを要求できます。 定義されている一連のユーザーに対してアプリへのアクセスを制限する場合は、[Azure AD でアプリケーションを構成](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)できます。


#### <a name="user-and-application-claims"></a>ユーザーおよびアプリケーション要求

すべての言語フレームワークにおいて、App Service は、受信トークン (認証されたエンド ユーザーまたはクライアント アプリケーションからなど) 内のクレームを要求ヘッダーに挿入することにより、コードで使用できるようにします。 ASP.NET 4.6 アプリの場合、App Service は認証されたユーザーの要求で [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) を設定するので、標準の .NET コード パターン (`[Authorize]` 属性など) に従うことができます。 同様に、PHP アプリの場合、App Service は `_SERVER['REMOTE_USER']` 変数を設定します。 Java アプリの場合、要求には [Tomcat サーブレットからアクセスできます](configure-language-java.md#authenticate-users-easy-auth)。

[Azure Functions](../azure-functions/functions-overview.md) では、.NET コードの `ClaimsPrincipal.Current` は設定されていませんが、要求ヘッダーでユーザーの要求を検索することも、要求コンテキストまたはバインディング パラメーターを使用して `ClaimsPrincipal` オブジェクトを取得することもできます。 詳細については、「[クライアント ID の操作](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)」を参照してください。

詳しくは、「[ユーザー要求へのアクセス](app-service-authentication-how-to.md#access-user-claims)」をご覧ください。

現時点では、現在のユーザーに Authentication/Authorization 機能を設定することは ASP.NET Core ではサポートされていません。 ただし、このギャップを埋めるのに役立つ[サードパーティのオープン ソースのミドルウェア コンポーネント](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)は存在します。

#### <a name="token-store"></a>トークン ストア

App Service が提供する組み込みのトークン ストアは、Web アプリ、API、またはネイティブ モバイル アプリのユーザーに関連付けられているトークンのリポジトリです。 いずれかのプロバイダーで認証を有効にすると、このトークン ストアはアプリですぐに使用できるようになります。 次のような場合、アプリケーション コードはユーザーに代わってこれらのプロバイダーのデータにアクセスする必要があります。

- 認証されたユーザーの Facebook タイムラインに投稿する
- Microsoft Graph API を使用してユーザーの会社のデータを読み取る

通常、アプリケーションでこれらのトークンを収集、格納、更新するには、コードを記述する必要があります。 トークン ストアに関しては、トークンが必要になったら[トークンを取得](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)し、トークンが無効になったら[トークンを更新するよう App Service に指示する](app-service-authentication-how-to.md#refresh-identity-provider-tokens)だけです。 

ID トークン、アクセス トークン、更新トークンは認証されたセッションに対してキャッシュされ、関連付けられているユーザーだけがアクセスできます。  

お使いのアプリでトークンを使う必要がない場合は、お使いのアプリの **認証と承認** のページでトークン ストアを無効にできます。

#### <a name="logging-and-tracing"></a>ログとトレース

[アプリケーション ログを有効にする](troubleshoot-diagnostic-logs.md)と、認証と認可のトレースをログ ファイルで直接見ることができます。 予期しない認証エラーが発生した場合は、既存のアプリケーション ログを参照して、すべての詳細を簡単に確認できます。 [失敗した要求トレース](troubleshoot-diagnostic-logs.md)を有効にしてある場合は、失敗した要求で認証および認可モジュールが演じていた役割を正確に確認できます。 トレース ログでは、`EasyAuthModule_32/64` という名前のモジュールへの参照を探します。

## <a name="more-resources"></a>その他のリソース

- [方法: Azure AD ログインを使用するように App Service または Azure Functions アプリを構成する](configure-authentication-provider-aad.md)
- [Azure App Service 上での認証と承認の高度な使用方法](app-service-authentication-how-to.md)

サンプル:
- [チュートリアル:Azure App Service で実行されている Web アプリに認証を追加する](scenario-secure-app-authentication-app-service.md)
- [チュートリアル: Azure App Service (Windows または Linux) でユーザーをエンド ツー エンドで認証および認可する](tutorial-auth-aad.md)
- [Azure AppService EasyAuth の .NET Core 統合 (サードパーティ)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [.NET Core を使用した Azure App Service 認証の取得 (サードパーティ)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)
