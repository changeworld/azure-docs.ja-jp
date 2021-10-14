---
title: Okta フェデレーションを Azure Active Directory マネージド認証に移行するためのチュートリアル
titleSuffix: Active Directory
description: Okta フェデレーション アプリケーションを Azure AD マネージド認証に移行する方法について学習します。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 28759e1f53199aca866a6b073c9e05ffd31f3d1e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355910"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>チュートリアル: Okta フェデレーションを Azure Active Directory マネージド認証に移行する

このチュートリアルでは、既存の Office 365 テナントを Okta for Single Sign-on (SSO) 機能とフェデレーションする方法について学習します。

フェデレーションを Azure Active Directory (Azure AD) に段階的に移行することで、確実にユーザーの良好な認証エクスペリエンスを得ることができます。 段階的な移行では、残りの Okta SSO アプリケーションへの逆フェデレーション アクセスをテストすることもできます。

## <a name="prerequisites"></a>前提条件

- Okta for SSO にフェデレーションされた Office 365 テナント
- Azure AD へのユーザー プロビジョニング用に構成された Azure AD Connect サーバーまたは Azure AD Connect クラウド プロビジョニング エージェント

## <a name="1-configure-azure-ad-connect-for-authentication"></a>1 - 認証用に Azure AD Connect を構成する

Office 365 ドメインを Okta とフェデレーションしたお客様は、現在、Azure AD で有効な認証方法を構成していない場合があります。 マネージド認証に移行する前に、Azure AD Connect を検証し、ユーザーのサインインを許可するように構成します。

ご利用の環境に最も適したサインイン方法を設定します。

- **パスワード ハッシュ同期**: [パスワード ハッシュ同期](../hybrid/whatis-phs.md)は、Azure AD Connect サーバーまたはクラウドプロビジョニング エージェントによって実装されるディレクトリ同期の拡張機能です。 この機能を使用して、Microsoft 365 などの Azure AD サービスにサインインすることができます。 このサービスにサインインするときに使用するパスワードは、オンプレミスの Active Directory インスタンスにサインインするときに使うものと同じです。

- **パススルー認証**: Azure AD [パススルー認証](../hybrid/how-to-connect-pta.md)を使用すると、ユーザーは同じパスワードを使用して、オンプレミスとクラウドベースのアプリケーションの両方にサインインできます。 ユーザーが Azure AD を使用してサインインすると、パススルー認証エージェントにより、オンプレミスの Active Directory に対して直接パスワードが検証されます。

- **シームレス SSO**: [Azure AD シームレス SSO](../hybrid/how-to-connect-sso.md) により、ユーザーは企業ネットワークにつながっている会社のデスクトップを使用するときに自動でサインインできます。 シームレス SSO により、ユーザーは、他のオンプレミス コンポーネントを必要とすることなく、クラウドベースのアプリケーションに簡単にアクセスできるようになります。

シームレス SSO は、Azure AD 内のユーザーのシームレスな認証エクスペリエンスを作成するためにパスワード ハッシュ同期またはパススルー認証にデプロイできます。

[デプロイ ガイド](../hybrid/how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)に従って、シームレス SSO に必要なすべての前提条件をエンド ユーザーに確実にデプロイするようにしてください。

この例では、パスワード ハッシュ同期とシームレス SSO を構成します。

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>パスワード ハッシュ同期およびシームレス SSO 用に Azure AD Connect を構成する

これらの手順に従って、パスワード ハッシュ同期用に Azure AD Connect を構成します。

1. Azure AD Connect サーバーで、**Azure AD Connect** アプリを開いてから、 **[構成]** を選択します。

   ![Azure A D アイコンと [構成] ボタンを示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

2. **[ユーザー サインインの変更]**  >  **[次へ]** を選択します。

   ![ユーザー サインインを変更するためのページを示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

3. グローバル管理者の資格情報を入力します。

   ![グローバル管理者の資格情報を入力する場所を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

4. 現在、サーバーは Okta とのフェデレーション用に構成されています。 選択内容を **[パスワード ハッシュ同期]** に変更します。 その後、 **[シングル サインオンを有効にする]** を選択します。

5. **[次へ]** を選択します。

これらの手順に従って、シームレス SSO を有効にします。

1. ローカル オンプレミス システムのドメイン管理者の資格情報を入力します。 **[次へ]** を選択します。

   ![ユーザー サインインの設定を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

2. 最後のページで、 **[構成]** を選択して Azure AD Connect サーバーを更新します。

   ![構成ページを示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

3. ここでは、Hybrid Azure AD Join の警告は無視します。 Okta からのフェデレーションを無効にした後に、デバイス オプションを再構成します。

   ![デバイス オプションを構成するためのリンクを示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="2-configure-staged-rollout-features"></a>2. 段階的ロールアウト機能を構成する

Azure AD では、ドメイン全体のフェデレーション解除をテストする前に、[クラウド認証の段階的ロールアウト](../hybrid/how-to-connect-staged-rollout.md)を使用してユーザーのフェデレーション解除をテストできます。 デプロイする前に、[前提条件](../hybrid/how-to-connect-staged-rollout.md#prerequisites)をご確認ください。

Azure AD Connect サーバーでパスワード ハッシュ同期とシームレス SSO を有効にした後、これらの手順に従って段階的ロールアウトを構成します。

1. [Azure portal](https://portal.azure.com/#home) で、 **[表示]** または **[Azure Active Directory の管理]** を選択します。

   ![Azure portal を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

2. **[Azure Active Directory]** メニューで、 **[Azure AD Connect]** を選択します。 その後、テナントで **[パスワード ハッシュの同期]** が有効になっていることを確認します。

3. **[マネージド ユーザー サインインの段階的ロールアウトを有効にする]** を選択します。

   ![段階的ロールアウトを有効にするためのオプションを示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

4. サーバーが構成された後、 **[パスワード ハッシュの同期]** の設定が **[オン]** に変更された可能性があります。 その設定が有効になっていない場合は、ここで有効にします。 

   **[シームレス SSO]** が **[オフ]** に設定されていることに注目してください。 それを有効にしようとすると、エラーが表示されます。これは、テナント内のユーザーに対して既に有効になっているためです。

5. **[グループの管理]** を選択します。

   ![グループを管理するためのボタンを示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

パスワード ハッシュ同期ロールアウトにグループを追加する手順に従います。 次の例では、10 人のメンバーを持つセキュリティ グループで始まります。

![セキュリティ グループの例を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

グループを追加した後、テナントで機能が有効になるまで約 30 分待ちます。 この機能が有効になると、お客様のユーザーは、Office 365 サービスへのアクセスを試みたときに、Okta にリダイレクトされなくなります。

段階的ロールアウト機能にはサポートされないシナリオがいくつかあります。  

- POP3 や SMTP などのレガシ認証はサポートされていません。

- Hybrid Azure AD Join を Okta と一緒に使用するように構成した場合、ドメインのフェデレーションが解除されるまで、すべての Hybrid Azure AD Join フローは Okta に移動します。 サインオン ポリシーは、Hybrid Azure AD Join Windows クライアントのレガシ認証を許可するために Okta に残しておく必要があります。

## <a name="3-create-an-okta-app-in-azure-ad"></a>3. Azure AD で Okta アプリを作成する

マネージド認証に変換したユーザーは引き続き、Okta のアプリケーションへのアクセスが必要な場合があります。 ユーザーがこれらのアプリケーションに簡単にアクセスできるようにするために、Okta ホーム ページにリンクする Azure AD アプリケーションを登録できます。

Okta のエンタープライズ アプリケーションの登録を構成するには、次のようにします。 
1. [Azure portal](https://portal.azure.com/#home) の **[Azure Active Directory の管理]** で、 **[表示]** を選択します。

2. 左側のメニューの **[管理]** で、 **[エンタープライズ アプリケーション]** を選択します。

   ![[エンタープライズ アプリケーション] の選択を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

3. **[すべてのアプリケーション]** メニューで、 **[新しいアプリケーション]** を選択します。

   ![[新しいアプリケーション] の選択を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

4. **[独自のアプリケーションの作成]** を選択します。 開かれたメニューで、Okta アプリに名前を付け、 **[操作中のアプリケーションを登録して Azure AD と統合します]** を選択します。 **[作成]** を選択します。

   :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/register-application.png" alt-text="アプリケーションの登録方法を示すスクリーンショット。" lightbox="media/migrate-okta-federation-to-azure-active-directory/register-application.png":::

5. **[任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント)]**  >  **[登録]** の順に選択します。

   ![アプリケーションを登録し、アプリケーション アカウントを変更する方法を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

6. Azure AD のメニューで、 **[アプリの登録]** を選択します。 その後、新しく作成された登録を開きます。

   ![新しいアプリの登録を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

7. ご利用のテナント ID とアプリケーション ID を記録します。

   >[!Note]
   >Okta で ID プロバイダーを構成するために、そのテナント ID とアプリケーション ID が必要になります。

   ![テナント ID とアプリケーション ID を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

8. 左側のメニューで **[証明書とシークレット]** を選択します。 次に、 **[新しいクライアント シークレット]** を選択します。 シークレットに一般的な名前を付けて、その有効期限を設定します。

9. シークレットの値と ID を記録します。

   >[!NOTE]
   >この値と ID は、以後表示されません。 この情報をここで記録できない場合は、シークレットの再生成が必要になります。

   ![シークレットの値と ID を記録する場所を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

10. 左側のメニューで、 **[API のアクセス許可]** を選択します。 OpenID Connect (OIDC) スタックへのアクセス許可をアプリケーションに付与します。

11. **[アクセス許可の追加]**  >  **[Microsoft Graph]**  >  **[委任されたアクセス許可]** の順に選択します。

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png" alt-text="委任されたアクセス許可を示すスクリーンショット。" lightbox="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png":::

12. [OpenID のアクセス許可] セクションで、**メール**、**OpenID**、**プロファイル** を追加します。 **[アクセス許可の追加]** を選択します。

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png" alt-text="アクセス許可を追加する方法を示すスクリーンショット。" lightbox="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png":::

13. **[Grant admin consent for \<tenant domain name>]\(<テナント ドメイン名> に管理者の同意を付与する\)** を選択し、 **[許可する]** 状態が表示されるまで待ちます。

    ![付与された同意を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

14. 左側のメニューで、 **[ブランド]** を選択します。 **[ホーム ページ URL]** で、ユーザーのアプリケーション ホーム ページを追加します。

    ![ブランドを追加する方法を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

15. Okta 管理ポータルで、 **[セキュリティ]**  >  **[ID プロバイダー]** の順に選択して新しい ID プロバイダーを追加します。 **[Add Microsoft]\(Microsoft の追加\)** を選択します。

    ![ID プロバイダーを追加する方法を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

16. **[ID プロバイダー]** ページで、アプリケーション ID を **[クライアント ID]** フィールドにコピーします。 クライアント シークレットを **[クライアント シークレット]** フィールドにコピーします。

17. **[詳細設定の表示]** を選択します。 既定では、この構成により、Okta のユーザー プリンシパル名 (UPN) が、逆フェデレーション アクセスのために Azure AD の UPN に関連付けられます。

    >[!IMPORTANT]
    >Okta と Azure AD の UPN が一致しない場合は、ユーザー間で共通の属性を選択します。

18. 自動プロビジョニングの選択を完了します。 既定では、Okta でユーザーが一致しない場合、システムによって、Azure AD でのユーザーのプロビジョニングが試行されます。 Okta からプロビジョニングを移行した場合は、 **[Okta サインイン ページにリダイレクトする]** を選択します。

    ![Okta サインイン ページにリダイレクトするためのオプションを示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    これで ID プロバイダー (IDP) を作成したので、ユーザーを正しい IDP に送信する必要があります。

19. **[ID プロバイダー]** メニューで、 **[ルーティング規則]**  >  **[ルーティング規則の追加]** の順に選択します。 Okta プロファイルで使用できる属性のいずれかを使用します。

20. すべてのデバイスと IP から Azure AD に直接サインインするには、次の図に示されているようにポリシーを設定します。

    この例では、すべての Okta プロファイルで **Division** 属性が使用されていないので、IDP ルーティングに適しています。

    ![IDP ルーティングの division 属性を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

21. これでルーティング規則を追加したので、リダイレクト URI を記録して、アプリケーション登録に追加できるようにします。

    ![リダイレクト URI の場所を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

22. アプリケーションの登録で、左側のメニューの **[認証]** を選択します。 その後、 **[プラットフォームの追加]**  >  **[Web]** の順に選択します。

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-platform.png" alt-text="Web プラットフォームを追加する方法を示すスクリーンショット。" lightbox="media/migrate-okta-federation-to-azure-active-directory/add-platform.png":::

23. Okta の IDP に記録したリダイレクト URI を追加します。 その後、 **[アクセス トークン]** と **[ID トークン]** を選択します。

    ![Okta アクセス トークンと ID トークンを示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

24. 管理コンソールで、 **[ディレクトリ]**  >  **[ユーザー]** の順に選択します。 プロファイルを編集する最初のテスト ユーザーを選択します。

25. プロファイルで、次の図のように **ToAzureAD** を追加します。 次に、 **[保存]** を選択します。

    ![プロファイルを編集する方法を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

26. 変更されたユーザーとして、[Microsoft 356 ポータル](https://portal.office.com)にサインインしてみてください。 ユーザーがマネージド認証パイロットの一部でない場合、アクションがループすることがわかります。 ループを終了するには、マネージド認証エクスペリエンスにユーザーを追加します。

## <a name="4-test-okta-app-access-on-pilot-members"></a>4. パイロット メンバーでの Okta アプリのアクセスをテストする

Azure AD で Okta アプリを構成し、Okta ポータルで ID プロバイダーを構成した後、アプリケーションをユーザーに割り当てる必要があります。

1. Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** を選択します。

2. 先ほど作成したアプリ登録を選択し、 **[ユーザーとグループ]** に移動します。 マネージド認証パイロットと関連するグループを追加します。

   >[!NOTE]
   >ユーザーとグループを追加できるのは、 **[エンタープライズ アプリケーション]** ページからのみとなります。 **[アプリの登録]** メニューからユーザーを追加することはできません。

   ![グループを追加する方法を示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

3. 約 15 分後に、マネージド認証パイロット ユーザーの 1 人としてサインインし、[[マイ アプリ]](https://myapplications.microsoft.com) に移動します。

   ![[マイ アプリ] ギャラリーを示すスクリーンショット。](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

4. **[Okta アプリケーション アクセス]** タイルを選択して、ユーザーを Okta ホーム ページに戻します。

## <a name="5-test-managed-authentication-on-pilot-members"></a>5. パイロット メンバーでのテストマネージド認証

Okta 逆フェデレーション アプリを構成した後、マネージド認証エクスペリエンスに対してユーザーが完全なテストを行うようにします。 ユーザーがサインインするテナントを認識するのに役立つように、会社のブランドを設定することをお勧めします。 詳細については、[組織の Azure AD サインイン ページへのブランドの追加](../fundamentals/customize-branding.md)に関するページを参照してください。

>[!IMPORTANT]
>Okta からドメインのフェデレーションを完全に解除する前に、必要になる可能性のある追加の条件付きアクセス ポリシーを特定します。 完全にカットオフする前に環境をセキュリティで保護する場合は、[Azure AD 条件付きアクセスへの Okta サインオン ポリシーの移行](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)に関するページを参照してください。

## <a name="6-defederate-office-365-domains"></a>6. Office 365 ドメインのフェデレーションを解除する

組織がマネージド認証エクスペリエンスに慣れたら、Okta からドメインのフェデレーションを解除することができます。 まず、次のコマンドを使用して MSOnline PowerShell に接続します。 MSOnline PowerShell モジュールがまだない場合は、「`install-module MSOnline`」と入力してダウンロードすることができます。

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

ドメインをマネージド認証に設定した後、Okta ホーム ページへのユーザー アクセスを維持したまま、Okta から Office 365 テナントのフェデレーションを正常に解除できました。 

## <a name="next-steps"></a>次のステップ

- [Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行する](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Okta サインオン ポリシーを Azure AD の条件付きアクセスに移行する](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)

- [Okta から Azure AD にアプリケーションを移行する](migrate-applications-from-okta-to-azure-active-directory.md)
