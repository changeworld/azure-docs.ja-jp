---
title: Azure AD 認証を構成する
description: App Service または Azure Functions アプリの ID プロバイダーとして Azure Active Directory 認証を構成する方法を学びます。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: b1254e7db0e62d08ea2a3d6d30f2abd379675c55
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078317"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Azure AD ログインを使用するように App Service または Azure Functions アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、アプリで [Microsoft ID プラットフォーム](../active-directory/develop/v2-overview.md) (Azure AD) を認証プロバイダーとして使用してユーザーがサインインするように、Azure App Service または Azure Functions の認証を構成する方法について説明します。

App Service 認証機能では、Microsoft ID プラットフォームを使用してアプリの登録を自動的に作成できます。 また、ユーザーまたはディレクトリ管理者が個別に作成した登録を使用することもできます。

- [新しいアプリ登録を自動作成する](#express)
- [個別に作成された既存の登録を使用する](#advanced)

> [!NOTE]
> 新しい登録を作成するオプションは、政府機関向けクラウドでは使用できません。 代わりに、 [登録を個別に定義](#advanced)します。

## <a name="create-a-new-app-registration-automatically"></a><a name="express"> </a>新しいアプリ登録を自動作成する

このオプションでは、認証を簡単にするように設計されていて、必要なのは数回のクリックのみです。

1. [Azure portal] にサインインし、アプリに移動します。
1. 左側のメニューで **[認証]** を選択します。 **[ID プロバイダーの追加]** をクリックします。
1. [ID プロバイダー] ドロップダウンで **[Microsoft]** を選択します。 既定では、新しい登録を作成するオプションが選択されています。 登録の名前またはサポートされているアカウントの種類を変更できます。

    クライアント シークレットが作成され、`MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` という名前のスロット固定の[アプリケーション設定](./configure-common.md#configure-app-settings) として保存され ます。 Azure Key Vault でシークレットを管理する場合は、[Key Vault 参照](./app-service-key-vault-references.md)を使用するように、この設定を後で更新することができます。

1. これがアプリケーション用に構成された最初の ID プロバイダーである場合は、「**App Service 認証設定**」のセクションも表示されます。 それ以外の場合は、次の手順に進むことができます。
    
    これらのオプションは、アプリケーションが認証されていない要求にどのように応答するかを決定し、既定の選択によって、この新しいプロバイダーにログインするためのすべての要求がリダイレクトされます。 **[認証設定]** の横にある **[編集]** を選択して、この動作を今すぐカスタマイズするか、後でメインの **[認証]** 画面からこれらの設定を調整することができます。 これらのオプションの詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」を参照してください。

1. (オプション) **[次へ: アクセス許可]** をクリックし、アプリケーションで必要なスコープを追加します。 これらはアプリの登録に追加されますが、後で変更することもできます。
1. **[追加]** をクリックします。

これで、アプリケーションで認証に Microsoft ID プラットフォームを使用する準備ができました。 **[認証]** 画面にプロバイダーが一覧表示されます。 そこから、このプロバイダーの構成を編集または削除できます。

Azure Storage と Microsoft Graph にアクセスする Web アプリの Azure AD ログインを構成する例については、[こちらのチュートリアル](scenario-secure-app-authentication-app-service.md)を参照してください。

## <a name="use-an-existing-registration-created-separately"></a><a name="advanced"> </a>個別に作成された既存の登録を使用する

また、アプリケーションを Microsoft ID プラットフォームに手動で登録し、登録をカスタマイズして、登録の詳細を使用して App Service 認証を構成することもできます。 これは、たとえば App Service が含まれているものとは異なる Azure AD テナントからアプリケーションの登録を使用する場合に便利です。

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>App Service アプリに対するアプリ登録を Azure AD で作成する

まず、アプリの登録を作成します。 この場合は、後で App Service アプリで認証を構成するときに必要になる次の情報を収集します。

- クライアント ID
- テナント ID
- クライアント シークレット (省略可能)
- アプリケーション ID URI

アプリを登録するには、次の手順に従います。

1. [Azure portal] にサインインし、 **[App Services]** を探して選択してから、アプリを選択します。 アプリの **URL** をメモしておきます。 Azure Active Directory アプリの登録を構成するときにそれを使用します。
1. ポータル メニューから **[Azure Active Directory]** を選択し、 **[アプリの登録]** タブにアクセスして、 **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページで、アプリの登録の **[名前]** を入力します。
1. **[リダイレクト URI]** で、 **[Web]** を選択し、「`<app-url>/.auth/login/aad/callback`」と入力します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/aad/callback` 」のように入力します。
1. **[登録]** を選択します。
1. アプリの登録が作成されたら、後のために **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** をコピーします。
1. **[認証]** を選択します。 **[暗黙的な許可]** で **[ID トークン]** を有効にして、App Service からの OpenID Connect ユーザーのサインインを許可します。
1. (省略可能) **[ブランド]** を選択します。 **[ホーム ページ URL]** に App Service アプリの URL を入力し、 **[保存]** を選択します。
1. **[API の公開]**  >  **[設定]** を選択します。 シングルテナント アプリの場合は、App Service アプリの URL を貼り付けて **[保存]** を選択します。マルチテナント アプリの場合は、テナントで検証済みのいずれかのドメインに基づく URL を貼り付けて、 **[保存]** を選択します。

   > [!NOTE]
   > この値は、アプリの登録の **アプリケーション ID の URI** です。 Web アプリでクラウドの API にアクセスする必要がある場合、クラウド App Service リソースの構成時、**Web アプリのアプリケーション ID URI** が必要になります。 たとえば、クラウド サービスで Web アプリにアクセスを明示的に与える場合にこれを利用できます。

1. **[Scope の追加]** を選択します。
   1. **[スコープ名]** に、「*user_impersonation*」と入力します。
   1. 同意ページでユーザーに表示する同意スコープの名前と説明をテキスト ボックスに入力します。 たとえば、「*Access my app*」と入力します。
   1. **[スコープの追加]** を選択します。
1. (省略可能) クライアント シークレットを作成するには、 **[Certificates & secrets]\(証明書とシークレット)**  >  **[New client secret]\(新しいクライアント シークレット)**  >  **[追加]** を選択します。 ページに表示されるクライアント シークレットの値をコピーします。 二度と表示されることはありません。
1. (省略可能) 複数の **応答 URL** を追加するには、 **[認証]** を選択します。

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>App Service アプリで Azure Active Directory を有効にする

1. [Azure portal] にサインインし、アプリに移動します。
1. 左側のメニューで **[認証]** を選択します。 **[ID プロバイダーの追加]** をクリックします。
1. [ID プロバイダー] ドロップダウンで **[Microsoft]** を選択します。
1. **[アプリの登録の種類]** では、 **[このディレクトリ内の既存アプリの登録を選択]** を選択して、必要なアプリ情報を自動的に収集することができます。 別のテナントからの登録の場合、または登録オブジェクトを表示する権限がない場合は、 **[既存アプリの登録の詳細を提供します]** を選択します。 このオプションでは、次の構成の詳細を入力する必要があります。

    |フィールド|説明|
    |-|-|
    |アプリケーション (クライアント) ID| アプリの登録の **アプリケーション (クライアント) ID** を使用します。 |
    |クライアント シークレット (省略可能)| アプリの登録で生成したクライアント シークレットを使用します。 クライアント シークレットを使用すると、ハイブリッド フローが使用され、App Service によってアクセス トークンと更新トークンが返されます。 クライアント シークレットが設定されていない場合は、暗黙的なフローが使用され、ID トークンのみが返されます。 これらのトークンはプロバイダーによって送信され、EasyAuth トークン ストアに格納されます。|
    |発行者の URL| `<authentication-endpoint>/<tenant-id>/v2.0` を使用し、 *\<authentication-endpoint>* を [クラウド環境の認証エンドポイント](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (たとえば、グローバル Azure の場合、"https://login.microsoftonline.com") に置き換え、さらに、 *\<tenant-id>* を、アプリ登録が作成された **ディレクトリ (テナント) ID** に置き換えます。 この値は、ユーザーを正しい Azure AD テナントにリダイレクトするため、および適切なメタデータをダウンロードして、適切なトークン署名キーやトークン発行者のクレーム値などを特定するために使用されます。 Azure AD v1 を使用するアプリケーションと Azure Functions アプリの場合は、URL の `/v2.0` を省略します。|
    |許可されるトークン対象ユーザー| これがクラウドまたはサーバー アプリで、Web アプリからの認証トークンを許可する場合は、Web アプリの **アプリケーション ID URI** をここに追加します。 構成された **[クライアント ID]** は、*常に*、許可された対象ユーザーであると暗黙的に見なされます。|

    クライアント シークレットが、`MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` という名前のスロット固定の[アプリケーション設定](./configure-common.md#configure-app-settings) として保存され ます。 Azure Key Vault でシークレットを管理する場合は、[Key Vault 参照](./app-service-key-vault-references.md)を使用するように、この設定を後で更新することができます。

1. これがアプリケーション用に構成された最初の ID プロバイダーである場合は、「**App Service 認証設定**」のセクションも表示されます。 それ以外の場合は、次の手順に進むことができます。
    
    これらのオプションは、アプリケーションが認証されていない要求にどのように応答するかを決定し、既定の選択によって、この新しいプロバイダーにログインするためのすべての要求がリダイレクトされます。 **[認証設定]** の横にある **[編集]** を選択して、この動作を今すぐカスタマイズするか、後でメインの **[認証]** 画面からこれらの設定を調整することができます。 これらのオプションの詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」を参照してください。

1. **[追加]** をクリックします。

これで、アプリケーションで認証に Microsoft ID プラットフォームを使用する準備ができました。 **[認証]** 画面にプロバイダーが一覧表示されます。 そこから、このプロバイダーの構成を編集または削除できます。

## <a name="configure-client-apps-to-access-your-app-service"></a>App Service にアクセスするようにクライアント アプリを構成する

前のセクションでは、ユーザーを認証するために App Service または Azure 関数を登録しました。 このセクションでは、ネイティブ クライアントまたはデーモン アプリを登録して、ユーザーまたはユーザーの代わりに App Service によって公開される API へのアクセスを要求できるようにする方法について説明します。 ユーザーを認証するだけの場合は、このセクションの手順を完了する必要はありません。

### <a name="native-client-application"></a>ネイティブ クライアント アプリケーション

サインインしているユーザーの代わりに App Service アプリの API へのアクセスを要求するようにネイティブ クライアントを登録できます。

1. [Azure portal] で、 **[Active Directory]** 、 **[アプリの登録]** 、 **[新規登録]** の順に選択します。
1. **[アプリケーションの登録]** ページで、アプリの登録の **[名前]** を入力します。
1. **[リダイレクト URI]** で、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択し、URL「`<app-url>/.auth/login/aad/callback`」を入力します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/aad/callback` 」のように入力します。

    > [!NOTE]
    > Microsoft Store アプリケーションの場合は、代わりに[パッケージ SID](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) を URI として使用します。
1. **［作成］** を選択します
1. アプリの登録が作成されたら、 **[アプリケーション (クライアント) ID]** の値をコピーします。
1. **[API のアクセス許可]** 、 **[アクセス許可の追加]** 、 **[自分の API]** の順に選択します。
1. App Service アプリ用に以前に作成したアプリの登録を選択します。 アプリの登録が表示されない場合は、「[App Service アプリに対するアプリ登録を Azure AD で作成する](#register)」で **user_impersonation** スコープを追加したことを確認します。
1. **[委任されたアクセス許可]** で、 **[user_impersonation]** を選択し、 **[アクセス許可の追加]** を選択します。

これで、ユーザーに代わって App Service アプリにアクセスを要求できるネイティブ クライアント アプリケーションが構成されました。

### <a name="daemon-client-application-service-to-service-calls"></a>デーモン クライアント アプリケーション (サービス間の呼び出し)

アプリケーションは、(ユーザーの代わりではなく) それ自体の代わりに App Service または関数アプリでホストされる Web API を呼び出すトークンを取得できます。 このシナリオは、ログイン ユーザーなしでタスクを実行する非対話型デーモン アプリケーションに役立ちます。 これには標準の OAuth 2.0 [クライアント資格情報](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)の付与が使用されます。

1. [Azure portal] で、 **[Active Directory]** 、 **[アプリの登録]** 、 **[新規登録]** の順に選択します。
1. **[アプリケーションの登録]** ページで、デーモン アプリの登録の **[名前]** を入力します。
1. デーモン アプリケーションの場合、リダイレクト URI は不要であるため、空のままでかまいません。
1. **［作成］** を選択します
1. アプリの登録が作成されたら、 **[アプリケーション (クライアント) ID]** の値をコピーします。
1. **[証明書とシークレット]**  >  **[新しいクライアント シークレット]**  >  **[追加]** を選択します。 ページに表示されるクライアント シークレットの値をコピーします。 二度と表示されることはありません。

これで、`resource` パラメーターをターゲット アプリの **アプリケーション ID URI** に設定することで、[クライアント ID とクライアント シークレットを使用してアクセス トークンを要求](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)できます。 その後、標準の [OAuth 2.0 Authorization ヘッダー](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource)を使用して、結果として得られたアクセス トークンをターゲット アプリに提示できます。また、App Service の認証/承認によって、通常どおりトークンが検証および使用され、呼び出し元 (この場合はユーザーではなくアプリケーション) が認証されたことが示されます。

現時点で、これによって Azure AD テナント内の _すべて_ のクライアント アプリケーションがアクセス トークンを要求し、ターゲット アプリに対して認証を行うことができます。 また、"_承認_" を実施して特定のクライアント アプリケーションのみを許可する場合は、追加の構成を行う必要があります。

1. 保護する App Service または関数アプリを表すアプリ登録のマニフェストで、[アプリ ロールを定義](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)します。
1. 承認する必要があるクライアントを表すアプリの登録で、 **[API のアクセス許可]**  >  **[アクセス許可の追加]**  >  **[自分の API]** を選択します。
1. 前に作成したアプリの登録を選択します。 アプリの登録が表示されない場合は、[アプリ ロールを追加](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)したことを確認してください。
1. **[アプリケーションのアクセス許可]** で、前に作成したアプリ ロールを選択し、 **[アクセス許可の追加]** を選択します。
1. クライアント アプリケーションがアクセス許可を要求することを承認するために、必ず **[管理者の同意を与える]** をクリックしてください。
1. (ロールを追加する前の) 前述のシナリオと同様に、これで、同じターゲット `resource` の[アクセス トークンを要求](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)できます。アクセス トークンには、クライアント アプリケーションのために承認されたアプリ ロールを含む `roles` 要求が含まれます。
1. これで、ターゲット App Service または関数アプリのコード内で、必要なロールがトークンに存在しているか検証できます (これは App Service 認証/承認では実行されません)。 詳しくは、「[ユーザー要求へのアクセス](app-service-authentication-how-to.md#access-user-claims)」をご覧ください。

これで、独自の ID を使用して App Service アプリにアクセスできるデーモン クライアント アプリケーションが構成されました。

## <a name="best-practices"></a>ベスト プラクティス

認証の設定に使用する構成に関係なく、次のベスト プラクティスによってテナントとアプリケーションのセキュリティが強化されます。

- App Service アプリごとに独自のアクセス許可と同意を付与します。
- それぞれの App Service アプリを独自の登録で構成します。
- デプロイ スロットごとに個別のアプリ登録を使用することで、環境間でアクセス許可を共有することを回避します。 新しいコードをテストするとき、このプラクティスは、問題が運用アプリに影響を与えることを回避する上で役立つことがあります。

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [チュートリアル: Azure Storage と Microsoft Graph にアクセスする Web アプリでユーザーを認証および認可する](scenario-secure-app-authentication-app-service.md)
* [チュートリアル:Azure App Service でユーザーをエンド ツー エンドで認証および承認する](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
