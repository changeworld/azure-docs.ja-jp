---
title: アプリケーションを Okta から Azure Active Directory に移行するチュートリアル
titleSuffix: Active Directory
description: アプリケーションを Okta から Azure Active Directory に移行する方法について説明します。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 25724ab674879d460c11e52a8360cb2c76a23a26
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451618"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>チュートリアル: アプリケーションを Okta から Azure Active Directory に移行する

このチュートリアルでは、アプリケーションを Okta から Azure Active Directory (Azure AD) に移行する方法について説明します。

## <a name="create-an-inventory-of-current-okta-applications"></a>現在の Okta アプリケーションのインベントリを作成する

移行を開始する前に、現在の環境とアプリケーション設定を文書化する必要があります。 これらの情報は、集中管理された場所から Okta API を使用して収集できます。 この API を使用するには、[Postman](https://www.postman.com/) などの API エクスプローラー ツールが必要になります。

次の手順に従って、アプリケーション インベントリを作成します。

1. Postman アプリをインストールしていること。 その後、Okta 管理コンソールから API トークンを生成します。

1. API ダッシュボードの **[セキュリティ]** で、 **[トークン]**  >  **[トークンの作成]** の順に選択します。

   ![トークンを作成するためのボタンを示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

1. トークン名を挿入し、 **[トークンの作成]** を選択します。

   ![トークンに名前を付ける場所を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

1. トークン値を記録し、それを保存します。 **[了解しました]** を選択した後は、これにアクセスできません。

   ![[Token Value]\(トークン値\) ボックスを示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

1. Postman アプリのワークスペースで、 **[インポート]** を選択します。

   ![[インポート] の API を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

1. **[インポート]** ページで、 **[リンク]** を選択します。 次に、`https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment` というリンクを挿入して API をインポートします。

   ![インポートするためのリンクを示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

   >[!NOTE]
   >テナント値でリンクを変更しないでください。

1. **[インポート]** を選択して続行します。

   ![次の [インポート] ページを示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

1. API がインポートされたら、 **[環境]** の選択を **[{yourOktaDomain}]** に変更します。

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png" alt-text="環境を変更する方法を示すスクリーンショット。" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png":::

1. 目のアイコンを選択して Okta 環境を編集します。 次に、 **[編集]** を選択します。

   ![Okta 環境を編集する方法を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

1. **[初期値]** および **[現在値]** フィールドで URL と API キーの値を更新します。 環境が反映されるように名前を変更します。 その後、これらの値を保存します。

    ![API の値を更新する方法を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

1. [API を Postman に読み込みます](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17)。

1. **[アプリ]**  >  **[アプリの一覧の取得]**  >  **[送信]** の順に選択します。

   これで、Okta テナント内のすべてのアプリケーションを出力できるようになりました。 この一覧は、JSON 形式になっています。

    ![Okta テナント内のアプリケーションの一覧を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

この JSON 一覧をコピーし、CSV 形式に変換することをお勧めします。 [Konklone](https://konklone.io/json/) などのパブリック コンバーターを使用できます。 または、PowerShell の場合は、[ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) と [ConvertTo-CSV](/powershell/module/microsoft.powershell.utility/convertto-csv) を使用します。

後で参照できるように、この CSV をダウンロードして Okta テナント内のアプリケーションのレコードを保持します。

## <a name="migrate-a-saml-application-to-azure-ad"></a>SAML アプリケーションを Azure AD に移行する

SAML 2.0 アプリケーションを Azure AD に移行するには、まず、アプリケーション アクセスのために、Azure AD テナントにアプリケーションを構成します。 この例では、Salesforce インスタンスを変換します。 [このチュートリアル](../saas-apps/salesforce-tutorial.md)に従って、アプリケーションを構成します。

移行を完了するには、Okta テナント内で検出されたすべてのアプリケーションに対して次の構成手順を繰り返します。

1. [Azure AD ポータル](https://aad.portal.azure.com)で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[新しいアプリケーション]** の順に選択します。

   ![新しいアプリケーションの一覧を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

1. **Azure AD ギャラリー** で、**Salesforce** を検索し、そのアプリケーションを選択して **[作成]** を選択します。

   ![Azure AD ギャラリー内の Salesforce アプリケーションを示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

1. アプリケーションが作成されたら、 **[シングル サインオン]** (SSO) タブで **[SAML]** を選択します。

   ![SAML アプリケーションを示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

1. **[証明書 (未加工)]** と **[フェデレーション メタデータ XML]** をダウンロードして Salesforce にインポートします。

   ![フェデレーション メタデータをダウンロードする場所を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

1. Salesforce 管理コンソールで、 **[ID]**  >  **[シングル サインオンの設定]**  >  **[メタデータ ファイルから新規]** の順に選択します。

   ![Salesforce 管理コンソールを示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

1. Azure AD ポータルからダウンロードした XML ファイルをアップロードします。 **[作成]** を選択します。

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png" alt-text="XML ファイルをアップロードする場所を示すスクリーンショット。" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png":::

1. Azure からダウンロードした証明書をアップロードします。 次に、 **[保存]** を選択して、Salesforce で SAML プロバイダーを作成します。

   ![Salesforce で SAML プロバイダーを作成する方法を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

1. 次のフィールドの値を記録します。 これらの値は、後で Azure で使用します。
   * **エンティティ ID**
   * **ログイン URL**
   * **ログアウト URL**

   次に、 **[メタデータのダウンロード]** を選択します。

   ![Azure で使用するために記録する必要がある値を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

1. Azure AD の **[エンタープライズ アプリケーション]** ページの SAML SSO 設定で、 **[メタデータ ファイルをアップロードする]** を選択して、そのファイルを Azure AD ポータルにアップロードします。 保存する前に、インポートされた値が記録された値に一致していることを確認してください。

   ![Azure AD でメタデータ ファイルをアップロードする方法を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

1. Salesforce 管理コンソールで、 **[会社の設定]**  >  **[マイ ドメイン]** の順に選択します。 **[認証の構成]** に移動し、 **[編集]** を選択します。

    ![会社の設定を編集する方法を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

1. サインイン オプションで、前に構成した新しい SAML プロバイダーを選択します。 次に、 **[保存]** を選択します。

    ![SAML プロバイダー オプションを保存する場所を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

1. Azure AD の **[エンタープライズ アプリケーション]** ページで、 **[ユーザーとグループ]** を選択します。 次に、テスト ユーザーを追加します。

    ![追加されたテスト ユーザーを示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

1. 構成をテストするには、いずれかのテスト ユーザーとしてサインインします。 Microsoft の [アプリ ギャラリー](https://aka.ms/myapps)に移動し、 **[Salesforce]** を選択します。

    ![アプリ ギャラリーから Salesforce を開く方法を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

1. 新しく構成された ID プロバイダー (IdP) を選択してサインインします。

    ![サインインする場所を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

    すべてが正しく構成されている場合は、Salesforce ホーム ページにテスト ユーザーが表示されます。 トラブルシューティングのヘルプについては、[デバッグ ガイド](../manage-apps/debug-saml-sso-issues.md)を参照してください。

1. **[エンタープライズ アプリケーション]** ページで、適切なロールを使用して残りのユーザーを Salesforce アプリケーションに割り当てます。

    >[!NOTE]
    >残りのユーザーを Azure AD アプリケーションに追加したら、これらのユーザーは接続をテストして、アクセス権があることを確認する必要があります。 次の手順に進む前に、接続をテストしてください。

1. Salesforce 管理コンソールで、 **[会社の設定]**  >  **[マイ ドメイン]** の順に選択します。

1. **[認証の構成]** で、 **[編集]** を選択します。 認証サービスとしての **[Okta]** の選択を解除します。

    ![認証サービスとしての [Okta] の選択を解除する場所を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

これで、Salesforce が SSO 用に Azure AD で正常に構成されました。

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>OIDC/OAuth 2.0 アプリケーションを Azure AD アプリケーションに移行する

OpenID Connect (OIDC) または OAuth 2.0 アプリケーションを Azure AD に移行するには、Azure AD テナントで、まずアクセス用にアプリケーションを構成します。 この例では、カスタム OIDC アプリを変換します。

移行を完了するには、Okta テナント内で検出されたすべてのアプリケーションに対して次の構成手順を繰り返します。

1. [Azure AD ポータル](https://aad.portal.azure.com)で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。 **[すべてのアプリケーション]** で、 **[新しいアプリケーション]** を選択します。

1. **[独自のアプリケーションの作成]** を選択します。 表示されるメニューで、OIDC アプリに名前を付け、 **[操作中のアプリケーションを登録して Azure AD と統合します]** を選択します。 **[作成]** を選択します。

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png" alt-text="OIDC アプリケーションを作成する方法を示すスクリーンショット。" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png":::

1. 次のページで、アプリケーション登録のテナントを設定します。 詳細については、「[Azure Active Directory のテナント](../develop/single-and-multi-tenant-apps.md)」をご覧ください。

   この例では、 **[任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント)]**  >  **[登録]** の順に選択します。

   ![Azure AD ディレクトリのマルチテナントを選択する方法を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

1. **[アプリの登録]** ページの **[Azure Active Directory]** で、新しく作成された登録を開きます。

   [アプリケーションのシナリオ](../develop/authentication-flows-app-scenarios.md)によっては、さまざまな構成アクションが必要になることがあります。 ほとんどのシナリオにはアプリのクライアント シークレットが必要なため、それらのシナリオについて説明します。

1. **[概要]** ページで、 **[アプリケーション (クライアント) ID]** を記録します。 この ID は、後でアプリケーションで使用します。

   ![アプリケーション クライアント ID を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

1. 左側で、 **[証明書とシークレット]** を選択します。 次に、 **[新しいクライアント シークレット]** を選択します。 クライアント シークレットに名前を付け、その有効期限を設定します。

   ![新しいクライアント シークレットを示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

1. シークレットの値と ID を記録します。

   >[!NOTE]
   >クライアント シークレットを失った場合、それを取得することはできません。 代わりに、シークレットを再生成する必要があります。

1. 左側で、 **[API のアクセス許可]** を選択します。 次に、そのアプリケーションに OIDC スタックへのアクセス権を付与します。

1. **[アクセス許可の追加]**  >  **[Microsoft Graph]**  >  **[委任されたアクセス許可]** の順に選択します。

1. **[OpenId アクセス許可]** セクションで、 **[email]** 、 **[openid]** 、 **[profile]** を選択します。 **[アクセス許可の追加]** を選択します。

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png" alt-text="OpenId アクセス許可を追加する場所を示すスクリーンショット。" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png":::

1. ユーザー エクスペリエンスを向上させ、ユーザーの同意プロンプトを抑制するには、 **[テナント ドメイン名に管理者の同意を与えます]** を選択します。 その後、 **[許可]** 状態が表示されるまで待ちます。

    ![管理者の同意を付与する場所を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

1. アプリケーションにリダイレクト URI がある場合は、適切な URI を入力します。 応答 URL が **[認証]** タブに続けて、 **[プラットフォームの追加]** と **[Web]** をターゲットとしている場合は、適切な URL を入力します。 **[アクセス トークン]** と **[ID トークン]** を選択します。 次に、 **[構成]** を選択します。

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png" alt-text="トークンを構成する方法を示すスクリーンショット。" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png":::
    
    **[認証]** メニューの **[詳細設定]** および **[パブリック クライアント フローを許可する]** で、必要に応じて **[はい]** を選択します。

    ![パブリック クライアント フローを許可する方法を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

1. OIDC で構成されたアプリケーションで、テストする前に、アプリケーション ID とクライアント シークレットをインポートします。 前の手順に従って、そのアプリケーションをクライアント ID、シークレット、スコープなどの設定で構成します。

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>カスタム承認サーバーを Azure AD に移行する

Okta 承認サーバーは [API を公開する](../develop/quickstart-configure-app-expose-web-apis.md#add-a-scope)アプリケーション登録に 1 対 1 でマップされます。

既定の Okta 認可サーバーを Microsoft Graph のスコープまたはアクセス許可にマップする必要があります。

![既定の Okta 認可を示すスクリーンショット。](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>次のステップ

- [Okta フェデレーションを Azure AD に移行する](migrate-okta-federation-to-azure-active-directory.md)
- [Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行する](migrate-okta-sync-provisioning-to-azure-active-directory.md)
- [Okta サインオン ポリシーを Azure AD の条件付きアクセスに移行する](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
