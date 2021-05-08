---
title: Azure AD B2C カスタム ドメインを有効にする
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のリダイレクト URL でカスタム ドメインを有効にする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2de419885938b27ebce4a934db5ef966965b3dbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580166"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Azure Active Directory B2C のカスタム ドメインを有効にする

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) のリダイレクト URL でカスタム ドメインを有効にする方法について説明します。 アプリケーションでカスタム ドメインを使用すると、よりシームレスなユーザー エクスペリエンスが得られます。 ユーザーの視点では、サインイン プロセス中、ユーザーは Azure AD B2C の既定ドメイン *<tenant-name>.b2clogin.com* にリダイレクトするのでなく、ドメインにとどまります。

![カスタム ドメインのユーザー エクスペリエンス](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>カスタム ドメインの概要

[Azure Front Door](https://azure.microsoft.com/services/frontdoor/) を使用して Azure AD B2C のカスタム ドメインを有効にできます。 Azure Front Door は、Microsoft グローバル エッジ ネットワークを使用してセキュリティで保護された高速でスケーラビリティの高い Web アプリを作成するための、スケーラブルなグローバル エントリ ポイントです。 Azure Front Door の背後にある Azure AD B2C コンテンツをレンダリングして、Azure Front Door のオプションを構成し、アプリケーションの URL でカスタム ドメインを介してコンテンツを配信できます。

次の図は、Azure Front Door の統合を示しています。

1. ユーザーは、アプリケーションからサインイン ボタンをクリックすると、Azure AD B2C サインイン ページに移動します。 このページでカスタム ドメイン名が指定されます。
1. Web ブラウザーによって、カスタム ドメイン名は Azure Front Door の IP アドレスに解決されます。 DNS 解決時に、カスタム ドメイン名の正規名 (CNAME) レコードが Front Door の既定フロントエンド ホスト (例: `contoso.azurefd.net`) を指します。 
1. カスタム ドメイン (例: `login.contoso.com`) 宛てのトラフィックは、指定された Front Door の既定のフロントエンド ホスト (`contoso.azurefd.net`) にルーティングされます。
1. Azure Front Door が Azure AD B2C の `<tenant-name>.b2clogin.com` 既定ドメインを使用して Azure AD B2C コンテンツを呼び出します。 Azure AD B2C エンドポイントに対する要求に、元のカスタム ドメイン名を含むカスタム HTTP ヘッダーが含まれます。
1. Azure AD B2C は、関連するコンテンツと元のカスタム ドメインを表示して、要求に応答します。

![カスタム ドメインのネットワーク図](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> ブラウザーと Azure Front Door 間の接続は、IPv6 でなく、常に IPv4 を使用する必要があります。

カスタム ドメインを使用するときは、次の点を考慮してください。

- 複数のカスタム ドメインを設定できます。 サポートされているカスタム ドメインの最大数については、Azure AD B2C の「[Azure AD サービスの制限と制約](../active-directory/enterprise-users/directory-service-limits-restrictions.md)」、および Azure Front Door の「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)」を参照してください。
- Azure Front Door は別個の Azure サービスであるため、追加料金が発生します。 詳細については、「[Front Door の価格](https://azure.microsoft.com/pricing/details/frontdoor)」を参照してください。
- Azure Front Door の [Web Application Firewall](../web-application-firewall/afds/afds-overview.md) を使用するには、ファイアウォールの構成とルールが Azure AD B2C ユーザー フローで正しく動作することを確認する必要があります。
- カスタム ドメインを構成した後も、ユーザーは Azure AD B2C の既定ドメイン名 *<tenant-name>.b2clogin.com* にアクセスできます (カスタム ポリシーを使用して [アクセスがブロック](#block-access-to-the-default-domain-name)されている場合を除く)。
- 複数のアプリケーションがある場合は、それらすべてをカスタム ドメインに移行してください (ブラウザーが、現在使用されているドメイン名の下に Azure AD B2C セッションを格納するため)。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>カスタム ドメイン名をテナントに追加する

[Azure AD でカスタム ドメインを追加および検証](../active-directory/fundamentals/add-custom-domain.md)する方法に関するガイダンスに従ってください。 ドメインが検証された後で、作成した DNS TXT レコードを削除します。

> [!IMPORTANT]
> これらの手順では、必ず **Azure AD B2C** テナントにサインインして **Azure Active Directory** サービスを選択してください。

使用する予定の各サブドメインを検証します。 最上位ドメインを検証するだけでは不十分です。 たとえば、*login.contoso.com* と *account.contoso.com* でサインインできるようになるためには、最上位ドメイン *contoso.com* だけでなく、両方のサブドメインを検証する必要があります。  

## <a name="create-a-new-azure-front-door-instance"></a>新しい Azure Front Door インスタンスを作成する

[アプリケーションの Front Door を作成する](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application)手順に従い、フロントエンド ホストとルーティング規則の既定の設定を使用します。 

> [!IMPORTANT]
> これらの手順では、手順 1 で Azure portal にサインインした後、 **[ディレクトリ + サブスクリプション]** を選択し、Azure Front Door に使用したい Azure サブスクリプションが含まれているディレクトリを選択します。 これを、Azure AD B2C テナントが含まれているディレクトリに *しないで* ください。 

**[バックエンドの追加]** 手順で、次の設定を使用します。

* **[バックエンド ホストの種類]** で **[カスタム ホスト]** を選択します。  
* **[バックエンド ホスト名]** で、Azure AD B2C エンドポイント (<tenant-name>.b2clogin.com) のホスト名を選択します。 たとえば、「contoso.b2clogin.com」とします。 
* **[バックエンド ホスト ヘッダー]** で、 **[バックエンド ホスト名]** で選択したものと同じ値を選択します。

![バックエンドの追加](./media/custom-domain/add-a-backend.png)

**バックエンド** を **バックエンド プール** に追加した後で、**正常性プローブ** を無効にします。

![バックエンド プールを追加する](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Azure Front Door でカスタム ドメインを設定する

[カスタム ドメインを Front Door に追加する](../frontdoor/front-door-custom-domain.md)手順に従います。 `CNAME` レコードをカスタム ドメインに作成するとき、先ほど[Azure AD にカスタム ドメイン名を追加する](#add-a-custom-domain-name-to-your-tenant)手順で検証したカスタム ドメイン名を使用します。 

カスタム ドメイン名を検証した後で、 **[Custom domain name HTTPS]\(カスタム ドメイン名 HTTPS\)** を選択します。 **[証明書の管理の種類]** で、[[Front Door management]\(Front Door 管理\)](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door) または [[自分の証明書を使用する]](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate) を選択します。 

次のスクリーンショットは、カスタム ドメインを追加して Azure Front Door 証明書を使用して HTTPS を有効にする方法を示しています。

![Azure Front Door カスタム ドメインを設定する](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>CORS を構成する

HTML テンプレートを使用して [Azure AD B2C ユーザー インターフェイスをカスタマイズ](customize-ui-with-html.md)する場合は、カスタム ドメインを使用して [CORS を構成](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors)する必要があります。

Azure Blob Storage のクロスオリジン リソース共有を、次の手順で構成します。

1. [Azure portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. メニューで **[CORS]** を選択します。
1. **[許可されるオリジン]** には、`https://your-domain-name` を入力します。 `your-domain-name` を自分のドメイン名に置き換えます。 たとえば、「 `https://login.contoso.com` 」のように入力します。 テナント名を入力するときは、すべて小文字を使用します。
1. **[許可されたメソッド]** に、`GET` と `OPTIONS` を両方選択します。
1. **[許可されたヘッダー]** に、アスタリスク (*) を入力します。
1. **[公開されるヘッダー]** に、アスタリスク (*) を入力します。
1. **[最長有効期間]** には「200」と入力します。
1. **[保存]** を選択します。

## <a name="configure-your-identity-provider"></a>ID プロバイダーを構成する

ユーザーがソーシャル ID プロバイダーでのサインインを選択すると、Azure AD B2C が承認要求を開始し、ユーザーは選択した ID プロバイダーに移動してサインイン プロセスを完了します。 承認要求では、`redirect_uri` が Azure AD B2C の既定ドメイン名で指定されます。 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

外部 ID プロバイダーを使ってサインインできるようポリシーを構成した場合は、カスタム ドメインを使用して OAuth リダイレクト URI を更新します。 ほとんどの ID プロバイダーでは、複数のリダイレクト URI を登録できます。 リダイレクト URI を置き換えるのでなく追加して、Azure AD B2C の既定のドメイン名を使用するアプリケーションに影響を与えることなくカスタム ポリシーをテストできるようにすることをお勧めします。 

次のリダイレクト URI に対して、以下を行います。

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- **<custom-domain-name>** を自分のカスタム ドメイン名に置き換えます。
- **<tenant-name>** を自分のテナント名、またはテナント ID に置き換えます。

次の例は、有効な OAuth リダイレクト URI を示しています。

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

[テナント ID](#optional-use-tenant-id)を使用した場合、有効な OAuth リダイレクト URI は次のようになります。

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

[SAML ID プロバイダー](saml-identity-provider-technical-profile.md)のメタデータは、次のようになります。

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>カスタム ドメインのテスト

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[ユーザー フロー (ポリシー)]** を選択します。
1. ユーザー フローを選択して、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[クリップボードにコピー]** をクリックします。

    ![承認要求 URI をコピーします](./media/custom-domain/user-flow-run-now.png)

1. **[ユーザー フロー エンドポイントを実行]** URLで、Azure AD B2C ドメイン (<tenant-name>.b2clogin.com) を自分のカスタム ドメインに置き換えます。  
    たとえば、次の表記の代わりに、

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    を使う代わりに、

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. **[ユーザー フローを実行します]** を選択します。 Azure AD B2C ポリシーが読み込まれます。
1. ローカルおよびソーシャルのアカウントでサインインします。
1. 残りのポリシーでテストを繰り返します。

## <a name="configure-your-application"></a>アプリケーションの作成 

カスタム ドメインを構成してテストしたら、Azure AD B2C ドメインでなくカスタム ドメインをホスト名として指定する URL を読み込むように、アプリケーションを更新できます。 

カスタム ドメイン統合は、ユーザーを認証するために Azure AD B2C ポリシー (ユーザー フローまたはカスタム ポリシー) を使用する認証エンドポイントに適用されます。 これらのエンドポイントは次のようになります。

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

置換前のコード:
- **custom-domain** を自分のカスタム ドメインに置換
- **tenant-name** を自分のテナント名またはテナント ID に置換
- **policy-name** を自分のポリシー名に置換 [Azure AD B2C ポリシーの詳細については、こちらを参照してください](technical-overview.md#identity-experiences-user-flows-or-custom-policies)。 


[SAML サービス プロバイダー](./saml-service-provider.md)のメタデータは、次のようになります。 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>(省略可能) テナント ID を使用する

URL の B2C テナント名を自分のテナント ID GUID に置き換えて、URL 内の "b2c" へのすべての参照が削除されるようにできます。 テナント ID GUID は、Azure portal の「B2C の概要」ページにあります。
たとえば、`https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` を `https://account.contosobank.co.uk/<tenant ID GUID>/` に変更します

テナント名の代わりにテナント ID を使用する場合は、ID プロバイダーの **OAuth リダイレクト URI** をそれに合わせて更新してください。 詳しくは、「[ID プロバイダーを構成する](#configure-your-identity-provider)」をご覧ください。

### <a name="token-issuance"></a>トークン発行

トークン発行者名 (iss) 要求は、使用しているカスタム ドメインに基づいて変わります。 次に例を示します。

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>既定のドメイン名へのアクセスをブロックする

カスタム ドメインを追加してアプリケーションを構成した後も、ユーザーは <tenant-name>.b2clogin.com ドメインにアクセスできます。 アクセスできないようにするために、承認要求の「ホスト名」を許可されているドメイン一覧と照合するように、ポリシーを構成できます。 ホスト名は、URL に表示されるドメイン名です。 ホスト名は `{Context:HostName}` [要求リゾルバー](claim-resolver-overview.md)を通じて入手できます。 その後に、カスタム エラー メッセージを提示できます。 

1. [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name) からホスト名を調べる条件付きアクセス ポリシーの例を取得します。
1. 各ファイル内で、文字列 `yourtenant` を、使用している Azure AD B2C テナントの名前に置き換えます。 たとえば、B2C テナントの名前が *contosob2c* であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosob2c.onmicrosoft.com` になります。
1. ポリシー ファイルは、`B2C_1A_TrustFrameworkExtensions_HostName.xml`、`B2C_1A_signup_signin_HostName.xml` の順序でアップロードします。

::: zone-end

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C で「ページが見つかりません」エラーが返される

- **現象** - カスタム ドメインを構成した後で、カスタム ドメインでサインインしようとすると、HTTP 404 エラー メッセージが表示されます。
- **考えられる原因** - この問題は、DNS 構成または Azure Front Door バックエンド構成に関連している可能性があります。 
- **解決方法**:  
    1. カスタム ドメインが Azure AD B2C テナントに[登録されていて正常に検証されている](#add-a-custom-domain-name-to-your-tenant)ことを確認します。
    1. [カスタム ドメイン](../frontdoor/front-door-custom-domain.md)が正しく構成されていることを確認します。 カスタム ドメインの `CNAME` レコードは、Azure Front Door の既定のフロントエンド ホスト (例: contoso.azurefd.net) をポイントしていなければなりません。
    1. [Azure Front Door バックエンド プール構成](#set-up-your-custom-domain-on-azure-front-door)が、カスタム ドメイン名が設定されていてユーザー フローまたはカスタム ポリシーが格納されているテナントをポイントしていることを確認します。

### <a name="identify-provider-returns-an-error"></a>ID プロバイダーがエラーを返す

- **現象** -カスタム ドメインを構成した後に、ローカル アカウントでサインインできるようになります。 しかし、外部の[ソーシャルまたはエンタープライズ ID プロバイダー](add-identity-provider.md)から入手した資格情報を使用してサインインすると、ID プロバイダーでエラー メッセージが表示されます。
- **考えられる原因** - Azure AD B2C でユーザーがフェデレーション ID プロバイダーを使用してサインインすると、リダイレクト URI が指定されます。 リダイレクト URI は、ID プロバイダーがトークンを返す場所のエンドポイントです。 リダイレクト URI は、アプリケーションが承認要求で使用するものと同じドメインです。 リダイレクト URI がまだ ID プロバイダーに登録されていない場合は、新しいリダイレクト URI は信頼されない可能性があり、その結果としてエラー メッセージが表示されます。 
- **解決方法** - 「[ID プロバイダーを構成する](#configure-your-identity-provider)」の手順に従って、新しいリダイレクト URI を追加します。 


## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Azure Front Door の高度な構成、たとえば "*Web アプリケーション ファイアウォール規則*" を使用できますか。 
  
Azure Front Door の高度な構成設定は正式にサポートされていないため、ご自分の責任でご使用ください。 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>[今すぐ実行] を使用して自分のポリシーを実行しようとすると、カスタム ドメインが表示されないのはなぜですか。

URL をコピーし、ドメイン名を手動で変更して、ブラウザーに貼り付けてください。

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Azure AD B2C に提示される IP アドレスはどちらですか。 ユーザーの IP アドレスですか。それとも Azure Front Door の IP アドレスですか。

Azure Front Door は、ユーザーの元の IP アドレスを渡します。 これは、監査レポートまたはカスタム ポリシーに表示される IP アドレスです。

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>サードパーティ製の Web アプリケーション ファイアウォール (WAF) を B2C で使用できますか。

ご自分の Web アプリケーション ファイアウォールを Azure Front Door の手前で使用するには、すべてが Azure AD B2C のユーザー フローで正しく動作するよう構成して検証する必要があります。

## <a name="next-steps"></a>次のステップ

[OAuth 承認要求](protocols-overview.md)について学習します。