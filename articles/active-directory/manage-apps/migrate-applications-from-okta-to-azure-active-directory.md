---
title: アプリケーションを Okta から Azure Active Directory に移行するチュートリアル
titleSuffix: Active Directory
description: アプリケーションを Okta から Azure Active Directory に移行する方法について説明します
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: c46410a6998998ace9bc1a9e9809262970a131f2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791728"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>チュートリアル: アプリケーションを Okta から Azure Active Directory に移行する

このチュートリアルでは、アプリケーションを Okta から Azure Active Directory (Azure AD) に移行する方法について説明します。

## <a name="create-an-inventory-of-current-okta-applications"></a>現在の Okta アプリケーションのインベントリを作成する

Okta アプリケーションを Azure AD に変換する場合は、まず移行前に現在の環境とアプリケーションの設定をドキュメント化することが推奨されます。

Okta には、この情報を一元化された場所から収集するために使用できる API が用意されています。 API を使用するには [Postman](https://www.postman.com/) などの API エクスプローラー ツールが必要です。

次の手順に従って、アプリケーション インベントリを作成します。

1. Postman アプリをインストールしていること。 インストール後、Okta 管理コンソールから API トークンを生成します。

2. [セキュリティ] セクションで API ダッシュボードに移動し、 **[トークン]**  >  **[トークンの作成]** を選択します

   ![トークンの作成を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

3. トークン名を挿入し、 **[トークンの作成]** を選択します。

   ![作成されたトークンを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

4. **[トークンの作成]** の選択後、値を記録して保存します。 **[了解しました]** を選択した後はそれにアクセスできなくなるためです。

   ![作成されたレコードを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

5. API トークンを記録したら、Postman アプリに戻り、ワークスペースの **[インポート]** を選択します。

   ![API のインポートを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

6. [インポート] ページで、 **[リンク]** を選択し、次のリンクを使用して API をインポートします。<https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment>

   ![インポートするリンクを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

>[!NOTE]
>テナント値でリンクを変更しないでください。

7. **[インポート]** を選択して、次のメニューに進みます。

   ![次のインポート メニューを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

8. インポートされたら、環境の選択を **[{yourOktaDomain}]** に変更します

   ![環境の変更を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png)

9. 環境の選択を変更したら、目を選択し、次に **[編集]** を選択して Okta 環境を編集します。

   ![環境の編集を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

10. **[初期値]** フィールドと **[現在値]** フィールドの両方で URL と API キーの値を更新し、環境を反映するように名前も変更してから、値を保存します。

    ![API の値の更新を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

11. API キーの保存後、[アプリ API を Postman に読み込みます](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17)。

12. API が Postman に読み込まれたら、 **[アプリ]** ドロップダウンを選択し、次に **[アプリの一覧の取得]** を選択し、 **[送信]** を選択します。

これで、Okta テナント内のすべてのアプリケーションを JSON 形式に出力できます。

![アプリケーションの一覧を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

<https://konklone.io/json/> または [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) や [ConvertTo-CSV.](/powershell/module/microsoft.powershell.utility/convertto-csv) を使用した PowerShell などのパブリック コンバーターを使用して、この JSON リストをコピーして CSV に変換することが推奨されます。

CSV をダウンロードしたら、Okta テナント内のアプリケーションは、後で参照するために正常に記録されています。

## <a name="migrate-a-saml-application-to-azure-ad"></a>SAML アプリケーションを Azure AD に移行する

SAML 2.0 アプリケーションを Azure AD に移行するには、まず、アプリケーション アクセスのために、Azure AD テナントにアプリケーションを構成します。 この例では、Salesforce インスタンスを変換します。 [このチュートリアル](../saas-apps/salesforce-tutorial.md)に従って、アプリケーションをオンボードします。

移行プロセスを完了するには、Okta テナントで検出されたすべてのアプリケーションについて構成手順を繰り返します。

1. [Azure AD ポータル](https://aad.portal.azure.com)に移動し、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[新しいアプリケーション]** の順に選択します。

   ![新しいアプリケーションの一覧を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

2. Salesforce は、Azure AD ギャラリーにあります。 Salesforce を検索し、アプリケーションを選択して **[作成]** を選択します。

   ![Salesforce アプリケーションを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

3. アプリケーションが作成されたら、 **[シングル サインオン]** (SSO) タブに移動し、 **[SAML]** を選択します。

   ![SAML アプリケーションを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

4. SAML を選択後、Salesforce にインポートするための **[フェデレーション メタデータ XML] と [証明書 (未加工)]** をダウンロードします。

   ![フェデレーション メタデータのダウンロードを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

5. XML がキャプチャされたら、Salesforce 管理コンソールに移動し、 **[ID]**  >  **[シングル サインオンの設定]**  >  **[メタデータ ファイルから新規]** の順に選択します

   ![Salesforce 管理コンソールを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

6. Azure AD ポータルからダウンロードした XML ファイルをアップロードし、次に **[作成]** を選択します。

   ![XML ファイルのアップロードを示す 画像](media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png)

7. Azure からダウンロードした証明書をアップロードして、続行し、次のメニューで **[保存]** を選択して、Salesforce に SAML プロバイダーを作成します。

   ![SAML プロバイダーの作成を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

8. Azure で使用するために値 (**エンティティ ID**、**ログイン URL**、**ログアウト URL**) を記録し、 **[メタデータのダウンロード]** オプションを選択します。

   ![Azure の値の記録を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

9. Azure AD [エンタープライズ アプリケーション] メニューに戻り、SAML SSO 設定で、Azure AD ポータルに **メタデータ ファイルをアップロード** します。 保存する前に、インポートされた値が記録された値と一致することを確認します。

   ![Azure AD のメタデータ ファイルのアップロードを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

10. SSO 構成が保存されたら、Salesforce 管理コンソールに戻り、 **[会社の設定]**  >  **[マイ ドメイン]** の順に選択します。 **[認証の構成]** に移動し、 **[編集]** を選択します。

    ![会社の設定の編集のアップロードを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

11. 前の手順で構成した新しい SAML プロバイダーを使用可能なサインイン オプションとして選択し、 **[保存]** を選択します。

    ![SAML プロバイダー オプションの保存を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

12. Azure AD のエンタープライズ アプリケーションに戻り、 **[ユーザーとグループ]** を選択し、**テスト ユーザー** を追加します。

    ![テスト ユーザーの追加を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

13. テストするには、いずれかのテスト ユーザーとしてサインインし、<https://aka.ms/myapps> に移動して、 **[Salesforce]** タイルを選択します。

    ![テスト ユーザーとしてのサインインを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

14. [Salesforce] タイルを選択した後に、新しく構成した ID プロバイダー (IdP) を選択してサインインします。

    ![新しい ID プロバイダーの選択を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

15. すべてが正しく構成されていれば、ユーザーは Salesforce ホームページに到達します。 何らかの問題がある場合は、[デバッグ ガイド](../manage-apps/debug-saml-sso-issues.md)に従ってください。

16. Azure から SSO 接続をテストした後に、エンタープライズ アプリケーションに戻り、残りのユーザーを正しいロールで Salesforce アプリケーションに割り当てます。

>[!NOTE]
>残りのユーザーを Azure AD アプリケーションに追加したら、次の手順に進む前に、ユーザーに接続をテストさせて、アクセスに問題がないことを確認することが推奨されます。

17. ユーザーがサインインで問題がないことを確認したら、Salesforce 管理コンソールに戻り、 **[会社の設定]**  >  **[マイ ドメイン]** を選択します。

18. **[認証の構成]** に移動し、 **[編集]** を選択して、認証サービスとして Okta の選択を解除します。

    ![認証サービスとしての okta の選択解除を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

SSO 用に Azure AD に対して Salesforce が正常に構成されました。 Okta ポータルをクリーンアップする手順については、このドキュメントの後半で説明します。

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>OIDC/OAuth 2.0 アプリケーションを Azure AD アプリケーションに移行する

まず、アプリケーション アクセスのために、Azure AD テナントにアプリケーションを構成します。 この例では、カスタム OIDC アプリを変換します。

移行プロセスを完了するには、Okta テナントで検出されたすべてのアプリケーションについて構成手順を繰り返します。

1. [Azure AD ポータル](https://aad.portal.azure.com)に移動し、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。 **[すべてのアプリケーション]** メニューで、 **[新しいアプリケーション]** を選択します。

2. **[独自のアプリケーションの作成]** を選択します。 ポップアップ表示されるサイド メニューで OIDC アプリに名前を付け、 **[操作中のアプリケーションを登録して Azure AD と統合します]** のオプション ボタンを選択し、 **[作成]** を選択します。

   ![新しい OIDC アプリケーションを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png)

3. 次のページでは、アプリケーションの登録のテナントに関する選択肢が表示されます。 詳細については、[こちらの記事](../develop/single-and-multi-tenant-apps.md)をご覧ください。

この例では、 **[任意の組織ディレクトリ内のアカウント]** 、任意の Azure AD ディレクトリ、 **[マルチテナント]** 、次に **[登録]** を選択します。

![Azure AD ディレクトリ マルチテナントを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

4. アプリケーションを登録したら、 **[Azure Active Directory]** の下の **[アプリの登録]** ページに移動し、新しく作成した登録を開きます。

   [アプリケーションのシナリオ](../develop/authentication-flows-app-scenarios.md)によっては、さまざまな構成アクションが必要になる場合があります。 ほとんどのシナリオではアプリ クライアント シークレットが必要であり、それらの例を取り上げます。

5. **[概要]** ページで、後でアプリケーションで使用するためにアプリケーション (クライアント) ID を記録します。

   ![アプリケーション クライアント ID を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

6. アプリケーション ID の記録後、左メニューの **[証明書とシークレット]** を選択します。 **[新しいクライアント シークレット]** を選択し、名前を付け、適切に有効期限を設定します。

   ![新しいクライアント シークレットを示す画像](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

7. このページを離れる前に、シークレットの値と ID を記録します。

>[!NOTE]
>この情報は後で記録できないので、紛失した場合はシークレットを再生成する必要があります。

8. 上記の手順で情報を記録したら、左側の **[API のアクセス許可]** を選択し、OIDC スタックへのアクセス許可をアプリケーションに付与します。

9. **[アクセス許可の追加]** 、次に **[Microsoft Graph]** および **[委任されたアクセス許可]** を選択します。

10. **[OpenID のアクセス許可]** セクションで、メール、OpenID、およびプロファイルを追加し、 **[アクセス許可の追加]** を選択します。

    ![openid アクセス許可の追加を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png)

11. アクセス許可を追加したら、ユーザー エクスペリエンスを向上させ、ユーザーの同意プロンプトを表示しないようにするため、 **[テナント ドメイン名に管理者の同意を与えます]** オプションを選択し、 **[許可]** 状態が表示されるまで待ちます。

    ![管理者の同意の付与を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

12. アプリケーションにリダイレクト URI または応答 URL がある場合は、 **[認証]** タブ、次に **[プラットフォームの追加]** と **[Web]** の順に移動して、適切な URL を入力し、下部にある [アクセス トークン] と [ID トークン] を選択してから、 **[構成]** を選択します。

    ![トークンの構成を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png)

    必要に応じて、[認証] メニューの **[詳細設定]** 設定で、 **[パブリック クライアント フローを許可する]** を [はい] に反転します。

    ![パブリック クライアント フローの許可を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

13. OIDC で構成されたアプリケーションに戻り、テストの前にアプリケーション ID とクライアント シークレットをアプリケーションにインポートします。 ClientID、シークレット、スコープなどの上記の構成を使用するようにアプリケーションを構成します。

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>カスタム承認サーバーを Azure AD に移行する

Okta 承認サーバーは [API を公開する](../develop/quickstart-configure-app-expose-web-apis.md#add-a-scope)アプリケーション登録に 1 対 1 でマップされます。

既定の Okta 承認サーバーは Microsoft Graph スコープ/アクセス許可にマップされる必要があります。

![既定の Okta の認可を示す画像](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>次のステップ 

- [Okta フェデレーションを Azure AD に移行する](migrate-okta-federation-to-azure-active-directory.md)

- [Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行する](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Okta サインオン ポリシーを Azure AD の条件付きアクセスに移行する](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)