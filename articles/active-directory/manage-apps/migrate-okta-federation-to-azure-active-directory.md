---
title: Okta フェデレーションを Azure AD マネージド認証に移行するためのチュートリアル
titleSuffix: Active Directory
description: Okta フェデレーション アプリケーションを Azure AD マネージド認証に移行する方法の説明
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: b2486f4be20d2347f0cadff04ef8d0aa776ccdc5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791690"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>チュートリアル: Okta フェデレーションを Azure Active Directory マネージド認証に移行する

このチュートリアルでは、既存の Office 365 テナントを Okta for Single sign-on (SSO) 機能とフェデレーションする方法について説明します。

フェデレーションを Azure Active Directory (AD) に移行するには、ユーザーに必要な認証エクスペリエンスが確保される段階的方法で実施します。 残りの Okta SSO アプリケーションへの逆フェデレーション アクセスをテストする方法も同様です。

## <a name="prerequisites"></a>前提条件

- Okta for SSO にフェデレーションされた Office 365 テナント
- Azure AD にユーザーをプロビジョニングするために、Azure Active Directory Connect サーバーまたは Azure AD Connect クラウド プロビジョニング エージェントを構成します。

## <a name="step-1---configure-azure-ad-connect-for-authentication"></a>手順 1 - 認証用の Azure AD Connect を構成する

自社の Office 365 ドメインを Okta とフェデレーションしたお客様は、現在 Azure AD で有効な認証方法が構成されていない場合があります。 マネージド認証に移行する前に、Azure Active Directory Connect は、ユーザーのサインインを許可するために次のいずれかのオプションを使用して検証および構成される必要があります。

環境に最適な方法を判断するには、次の方法を使用します。

- **パスワード ハッシュ同期** - [パスワード ハッシュ同期](../hybrid/whatis-phs.md)は、Azure AD Connect サーバーまたは Azure AD Connect クラウド プロビジョニング エージェントによって実装されるディレクトリ同期の拡張機能です。 この機能を使用して、Microsoft 365 などの Azure AD サービスにサインインできます。 このサービスにサインインするときに使用するパスワードは、オンプレミスの Active Directory インスタンスにサインインするときに使うものと同じです。

- **パススルー認証** - Azure Active Directory (Azure AD) [パススルー認証](../hybrid/how-to-connect-pta.md)を使用すると、ユーザーは同じパスワードを使用して、オンプレミスのアプリケーションとクラウド ベースのアプリケーションの両方にサインインできます。 この機能により、ユーザーが Azure AD を使用してサインインするとき、パススルー認証エージェントを介してユーザーのパスワードが Windows Server Active Directory に対して直接検証されます。

- **シームレス SSO** - [Azure Active Directory (Azure AD) シームレス SSO](../hybrid/how-to-connect-sso.md) により、ユーザーは企業ネットワークにつながっている会社のデスクトップを使用するときに自動でサインインできます。 シームレス SSO により、ユーザーは、他のオンプレミス コンポーネントを必要とすることなく、クラウド ベースのアプリケーションに簡単にアクセスできるようになります。

シームレス SSO は、パスワード ハッシュ同期またはパススルー認証にもデプロイでき、Azure Active Directory 内のユーザーにシームレスな認証エクスペリエンスを作成できます。

[デプロイ ガイド](../hybrid/how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)に従って、シームレス SSO に必要なすべての前提条件をエンド ユーザーにデプロイします。

この例では、パスワード ハッシュ同期とシームレス SSO を構成します。

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>パスワード ハッシュ同期およびシームレス SSO 用に Azure AD Connect を構成する

次の手順に従って、パスワード ハッシュ同期用に Azure AD Connect を構成します。

1. お使いの Azure AD Connect サーバーで、スタート メニューまたはデスクトップ アイコンから **[Azure AD Connect]** アプリを起動し、 **[構成]** を選択します。

   ![Azure Active Directory アイコンと [構成] ボタンを示す画像](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

2. **[ユーザー サインインの変更]**  >  **[次へ]** を選択します。

   ![ユーザー サインインの変更画面を示す画像](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

3. 次のページで、グローバル管理者の資格情報を入力します。

   ![グローバル管理者の資格情報の入力を示す画像](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

4. 現在、サーバーは Okta とのフェデレーション用に構成されています。 選択内容を [パスワード ハッシュ同期] に更新します。 **[Enable Single sign-on]\(シングル サインオンを有効にする\)** チェック ボックスを選択します。

5. 選択を更新した後、 **[次へ]** を選択します。

次の手順に従って、シームレス SSO を有効にします。

1. ローカルのオンプレミスへのドメイン管理者の資格情報を入力し、 **[次へ]** を選択します。

   ![ドメイン管理者の資格情報の入力を示す画像](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

2. 最後のページで、 **[構成]** を選択して Azure AD Connect サーバーを更新します。

   ![Azure AD Connect サーバーの更新を示す画像](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

3. 現在のところ、Hybrid Azure AD 参加の警告は無視しますが、Okta からのフェデレーションを無効にした後、**デバイス オプション** を再構成する必要があります。

   ![デバイス オプションの再構成を示す画像](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="step-2---configure-staged-rollout-features"></a>手順 2 - 段階的ロールアウト機能を構成する

[クラウド認証の段階的ロールアウト](../hybrid/how-to-connect-staged-rollout.md)は、ドメイン全体のフェデレーションを解除する前に、ユーザーのフェデレーション解除をテストするために使用できる Azure Active Directory の機能です。 デプロイの前に、[前提条件](../hybrid/how-to-connect-staged-rollout.md#prerequisites)を確認してください。

Azure AD Connect サーバーでパスワード ハッシュ同期とシームレス SSO を有効にした後、次の手順に従って段階的なロールアウトを構成します。

1. [Azure portal](https://portal.azure.com/#home) に移動し、 **[表示]** または **[Azure Active Directory の管理]** を選択します。

   ![Azure portal を示す画像](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

2. Azure Active Directory メニューの **[Azure AD Connect]** を選択し、[パスワード ハッシュ同期] がテナントで有効として表示されていることを確認します。

3. 確認後、 **[マネージド ユーザー サインインの段階的ロールアウトを有効にする]** を選択します。

   ![段階的ロールアウトの有効化を示す画像](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

4. サーバーを構成した後、パスワード ハッシュ同期設定は **[オン]** に変換されている可能性があります。変換されていない場合は、有効にすると、シームレス SSO が **[オフ]** に設定されることが分かります。 メニューで有効にしようとすると、テナント内のユーザーに対して既に有効になっているというエラーが表示されます。

5. パスワード ハッシュ同期を有効にした後、 **[グループの管理]** を選択します。

   ![パスワード ハッシュ同期の有効化を示す画像](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

パスワード ハッシュ同期ロールアウトにグループを追加する手順に従います。 次の例では、最初に 10 人のメンバーを持つセキュリティ グループを使用します。

![セキュリティ グループの例を示す画像](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

グループに追加した後、機能がテナントで有効になるまで約 30 分待ちます。 この機能が有効になると、お客様のユーザーは、Office 365 サービスへのアクセスを試みたときに、Okta にリダイレクトされなくなります。

段階的ロールアウト機能には、サポートされないシナリオがいくつかあり、これらは次のとおりです。  

- POP3 や SMTP などのレガシ認証はサポートされていません。

- ハイブリッド Azure Active Directory 参加を Okta と一緒に使用するように構成している場合、ドメインがフェデレーション解除されるまで、すべてのハイブリッド Azure Active Directory 参加フローは、引き続き Okta に移動します。 サインオン ポリシーは、Hybrid Azure AD 参加 Windows クライアント用にレガシ認証を許可する Okta に残す必要があります。

## <a name="step-3---create-okta-app-in-azure-ad"></a>手順 3 - Azure Active Directory で Okta アプリを作成する

マネージド認証に変換されたユーザーは、Okta 内にまだアクセスする必要があるアプリケーションがあり、これらのアプリケーションにユーザーが簡単にアクセスできるようにする必要がある可能性があります。 ユーザーの Okta ホーム ページにリンクする Azure Active Directory アプリケーションの登録を構成する方法について学習します。

1. エンタープライズ アプリケーション rRegistration for Okta を構成するには、[Azure portal](https://portal.azure.com/#home) に移動します。 **[表示]** を **[Azure Active Directory の管理]** で選択します。

2. 次に、[管理] セクションのメニューから、 **[エンタープライズ アプリケーション]** を選択します。

   ![エンタープライズ アプリケーションを示す画像](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

3. **[すべてのアプリケーション]** メニューで、 **[新しいアプリケーション]** を選択します。

   ![新しいアプリケーションを示す画像](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

4. **[独自のアプリケーションの作成]** を選択し、ポップアップ表示されるサイド メニューで Okta アプリに名前を付け、 **[操作中のアプリケーションを登録して Azure AD と統合します]** の放射状を選択し、 **[作成]** を選択します。

   ![アプリケーションの登録を示す画像](media/migrate-okta-federation-to-azure-active-directory/register-application.png)

5. アプリケーションを登録した後、任意の Azure AD Directory - マルチテナントなど、任意の組織のディレクトリ内のアカウントにそのアプリケーションを変更し、 **[登録]** を選択します。

   ![アプリケーションの登録とアプリケーション アカウントの変更を示す画像](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

6. 登録を追加した後、Azure Active Directory メニューに戻り、 **[アプリの登録]** を選択し、新しく作成した登録を開きます。

   ![アプリの登録を示す画像](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

7. アプリケーションを開いた後、テナント ID とアプリケーション ID を記録します。

   >[!Note]
   >Okta で ID プロバイダーを構成するために、後でテナント ID とアプリケーション ID が必要になります。

   ![テナント ID とアプリケーション ID の記録を示す画像](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

8. 左側のメニューの **[証明書とシークレット]** を選択します。 **[新しいクライアント シークレット]** を選択し、一般的な名前を付け、有効期限を設定します。

9. このページを離れる前に、シークレットの値と ID を記録します。

   >[!NOTE]
   >この情報は後で記録できないので、紛失した場合はシークレットを再生成する必要があります。

   ![シークレットの記録を示す画像](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

10. 左側のメニューで **[API のアクセス許可]** を選択し、OpenID Connect (OIDC) スタックへのアクセス許可をアプリケーションに付与します。

11. **[アクセス許可の追加]**  >  **[Microsoft Graph]**  >  **[委任されたアクセス許可]** を選択します。

    ![委任されたアクセス許可を示す画像](media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png)

12. [OpenID のアクセス許可] セクションで、 **[メール]** 、 **[OpenID]** 、および **[プロファイル]** を追加し、 **[アクセス許可の追加]** を選択します。

    ![アクセス許可の追加を示す画像](media/migrate-okta-federation-to-azure-active-directory/add-permissions.png)

13. **[Grant admin consent for Tenant Domain Name]\(テナント ドメイン名に管理者の同意を付与する\)** オプションを選択し、[許可する] 状態が表示されるのを待ちます。

    ![同意の付与を示す画像](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

14. アクセス許可に同意したら、ユーザーのアプリケーション ホームページの **[ブランド]** の [ホーム ページ URL] に追加します。

    ![ブランドの追加を示す画像](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

15. アプリケーションを構成した後、Okta 管理ポータルに切り替え、ID プロバイダーとして Microsoft を構成します。 **[セキュリティ]**  >  **[ID プロバイダー]** を選択し、新しい ID プロバイダーを追加し、既定の **[Microsoft]** オプションを追加します。

    ![idp の構成を示す画像](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

16. [ID プロバイダー] ページで、アプリケーション ID を [クライアント ID] フィールドに、クライアント シークレットを [クライアント シークレット] フィールドにコピーします。

17. **[詳細設定の表示]** を選択します。 既定では、これにより、Okta のユーザー プリンシパル名 (UPN) と、逆フェデレーション アクセスの Azure Active Directory が関連付けられます。

    >[!IMPORTANT]
    >UPN が Okta と Azure Active Directory で一致しない場合は、ユーザー間で照合する共通の属性を選択します。

18. 自動プロビジョニングの選択を確定します。 既定では、ユーザーが Okta に一致しない場合は、Azure Active Directory でのプロビジョニングが試行されます。 Okta からプロビジョニングを移行した場合は、 **[Redirect to Okta Sign-in page]\(Okta サインイン ページにリダイレクトする\)** オプションを選択します。

    ![Okta サインイン リダイレクトを示す画像](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    IDP を作成した後は、ユーザーを適切な IDP に送信するために追加の構成が必要になります。

19. [ID プロバイダー] メニューから **[ルーティング規則]** を選択し、Okta プロファイルで使用可能な属性の 1 つを使用して **[ルーティング規則の追加]** を選択します。

20. サインインをすべてのデバイスと IP から Azure Active Directory に向けるように示されているとおりに、ポリシーを構成します。

    この例では、属性の **[除算]** はすべての Okta プロファイルで使用されていないので、IDP ルーティングに使用する候補になります。

    ![idp ルーティングの除算を示す画像](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

21. ルーティング規則を追加した後、リダイレクト URI を記録して、 **[アプリケーションの登録]** に追加します。

    ![アプリケーションの登録を示す画像](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

22. アプリケーションの登録に戻り、認証タブを選択し、 **[プラットフォームを追加]** と **[Web]** を選択します。

    ![プラットフォームを追加を示す画像](media/migrate-okta-federation-to-azure-active-directory/add-platform.png)

23. Okta での IDP からのリダイレクト URI を追加し、 **[Access and ID tokens]\(アクセスと ID トークン\)** を選択します。

    ![Okta アクセスと ID トークンを示す画像](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

24. 管理コンソールで **[ディレクトリ]**  >  **[ユーザー]** を選択します。 最初のテスト ユーザーとそのプロファイルを選択します。

25. プロファイルの編集中に、例に一致するように **ToAzureAD** を追加し、 **[保存]** を選択します。

    ![プロファイルの編集を示す画像](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

26. ユーザー属性を保存した後、変更されたユーザーとして [Microsoft 356 ポータル](https://portal.office.com)へのサインインを試みます。 ユーザーがマネージド認証パイロットの一部でない場合に、これがループすることがわかります。 ユーザーがループを抜けられるようにするには、マネージド認証エクスペリエンスにユーザーを追加します。

## <a name="step-4---test-okta-app-access-on-pilot-members"></a>手順 4 - パイロット メンバーでの Okta アプリのアクセスをテストする

Azure AD で Okta アプリを構成し、Okta ポータルで ID プロバイダーを構成したら、アプリケーションをユーザーに割り当てる必要があります。

1. Azure portal に移動し、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** を選択します。

2. 前の手順で作成したアプリ登録を選択し、 **[ユーザーとグループ]** に移動します。 マネージド認証パイロットに関連付けられたグループを追加します。

>[!NOTE]
>ユーザーとグループを追加できるのは、エンタープライズ アプリケーションの選択からだけです。[アプリの登録] メニューでユーザーを追加することはできません。

   ![グループの追加を示す画像](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

3. 約 15 分後に、マネージド認証パイロット ユーザーの 1 人としてサインインし、[Myapplications](https://myapplications.microsoft.com) にアクセスします。

   ![myapplications へのアクセスを示す画像](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

4. 認証が完了すると、**Okta アプリケーション アクセス** タイルが表示されます。これにより、ユーザーが Okta ホームページにリンクされます。

## <a name="step-5---test-managed-authentication-on-pilot-members"></a>手順 5 - パイロット メンバーでのテストマネージド認証

Okta 逆フェデレーション アプリを構成した後は、マネージド認証エクスペリエンスに対してユーザーが完全なテストを実施するようにします。 ユーザーがサインインする適切なテナントを区別できるように、会社のブランドを設定することをお勧めします。 会社のブランドを設定するための[ガイダンス](../fundamentals/customize-branding.md)を入手します。

>[!IMPORTANT]
>Okta からドメインを全体としてフェデレーション解除する前に、必要になる可能性がある追加の条件付きアクセス ポリシーを判断します。 **完全にカットオフする前に環境をセキュリティで保護するための手順については、Okta サインオン ポリシーの Azure Active Directory への条件付きアクセス移行** について参照してください。

## <a name="step-6---remove-federation-for-office-365-domains"></a>手順 6 - Office 365 ドメインのフェデレーションを削除する

マネージド認証エクスペリエンスを組織が使い慣れたときが、Okta からドメインのフェデレーションを解除するタイミングです。 これを実現するには、次のコマンドを使用して MSOnline PowerShell に接続します。MSOnline PowerShell モジュールをまだ持っていない場合は、まず install-module MSOnline を実行してダウンロードできます。

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

ドメインをマネージド認証に設定すると、Okta ホームページへのユーザー アクセスを維持したまま、Okta から Office 365 テナントのフェデレーションが正常に解除されました。 

## <a name="next-steps"></a>次のステップ

- [Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行する](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Okta サインオン ポリシーを Azure AD の条件付きアクセスに移行する](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)

- [Okta から Azure AD にアプリケーションを移行する](migrate-applications-from-okta-to-azure-active-directory.md)