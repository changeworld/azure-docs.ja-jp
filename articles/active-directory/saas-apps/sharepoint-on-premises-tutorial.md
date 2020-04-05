---
title: チュートリアル:Azure Active Directory と SharePoint オンプレミスの統合 | Microsoft Docs
description: Azure Active Directory と SharePoint オンプレミスの間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 22b673ef481593247b6ee1007c13390a498c66be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048628"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SharePoint オンプレミスの統合

このチュートリアルでは、SharePoint オンプレミスと Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SharePoint オンプレミスを統合すると、次のことができます。

* SharePoint オンプレミスにアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SharePoint オンプレミスに自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SharePoint オンプレミスでのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SharePoint オンプレミスでは、**SP** によって開始される SSO がサポートされます
* SharePoint オンプレミスを構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。
* SharePoint オンプレミスのユーザー プロファイルを Azure AD と同期させる方法については、こちらの[リンク](https://docs.microsoft.com/archive/blogs/kaevans/sharepoint-2013-user-profile-sync-for-claims-users)を参照してください。

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>ギャラリーからの SharePoint オンプレミスの追加

Azure AD への SharePoint オンプレミスの統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SharePoint オンプレミスを追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。

    > [!NOTE]
    > この要素を使用できない場合は、左側のナビゲーション ウィンドウの上部にある固定された **[すべてのサービス]** リンクを通して開くこともできます。 以下の概要では、 **[Azure Active Directory]** リンクは **[ID]** セクションに配置されています。または、フィルター テキスト ボックスを使用して検索できます。

1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SharePoint オンプレミス**」と入力します。
1. 結果のパネルから **[SharePoint オンプレミス]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharepoint-on-premises"></a>SharePoint オンプレミスの Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SharePoint オンプレミスに対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと SharePoint オンプレミスの関連ユーザーの間で、リンク関係が確立されている必要があります。

SharePoint オンプレミスで Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SharePoint オンプレミスでのシングル サインオンの構成](#configure-sharepoint-on-premises-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure portal での Azure AD セキュリティ グループの作成](#create-an-azure-ad-security-group-in-the-azure-portal)** - シングル サインオンのため Azure AD で新しいセキュリティ グループを有効にします。
5. **[セキュリティ グループにオンプレミスの SharePoint へのアクセスを許可](#grant-access-to-sharepoint-on-premises-security-group)** - 特定のグループに Azure AD へのアクセスを許可します。
6. **[Azure portal での Azure AD セキュリティ グループの割り当て](#assign-the-azure-ad-security-group-in-the-azure-portal)** - 認証のために特定のグループを Azure AD に割り当てます。
7. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SharePoint オンプレミスで Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **SharePoint オンプレミス** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。


    a. **[サインオン URL]** ボックスに、`https://<YourSharePointServerURL>/_trust/default.aspx` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`urn:sharepoint:federation` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<YourSharePointServerURL>/_trust/default.aspx` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[SharePoint オンプレミス クライアント サポート チーム](https://support.office.com/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

    > [!Note]
    > 証明書ファイルをダウンロードしたファイル パスは、構成時に PowerShell スクリプトで後から使用する必要があるため、書き留めておいてください。

6. **[Set up SharePoint on-premises]\(SharePoint オンプレミスのセットアップ\)** セクションで、要件どおりの適切な URL をコピーします。 **[シングル サインオン サービス URL]** に次のパターンの値を使用します。`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ は Azure Ad サブスクリプションのテナント ID です。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    > [!NOTE]
    > Sharepoint オンプレミス アプリケーションは SAML 1.1 トークンを使用するため、Azure AD では認証の後に SharePoint サーバーからの WS 取り込み要求が必要で、SAML 1.1 トークンを発行します。

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>SharePoint オンプレミスでのシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、SharePoint オンプレミスの企業サイトに管理者としてサインインします。

2. **SharePoint Server 2016 で信頼できる ID プロバイダーを新しく構成する**

    SharePoint Server 2016 サーバーにサインインし、SharePoint 2016 管理シェルを開きます。 Azure Portal から $realm (Azure Portal の [SharePoint オンプレミスのドメインと URL] からの [識別子] の値)、$wsfedurl ([シングル サインオン サービス URL])、および $filepath (証明書ファイルをダウンロードしたファイル パス) の値を入力し、次のコマンドを実行して信頼済みの新しい ID プロバイダーを構成します。

    > [!TIP]
    > PowerShell を使用した経験がない場合、または PowerShell の動作の詳細については、[SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps) に関する記事をご覧ください。

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    次に、これらの手順を実行して、アプリケーションの信頼できる ID プロバイダーを有効にします。

    a. [サーバーの全体管理] で **[Web アプリケーションの管理]** に移動し、Azure AD を使用してセキュリティ保護する Web アプリケーションを選択します。

    b. リボンで、 **[認証プロバイダー]** をクリックして、使用するゾーンを選択します。

    c. **[信頼できる ID プロバイダー]** を選択し、 *[AzureAD]* という名前で先ほど登録した ID プロバイダーを選択します。

    d. サインイン ページの URL 設定で、 **[カスタム サインイン ページ]** を選択し、値 "/_trust/" を指定します。

    e. **[OK]** をクリックします。

    ![認証プロバイダーを構成する](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > 外部ユーザーの一部では、UPN が `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com` のような値を分割したために、このシングル サインオン統合を使用できません。 近いうちに、顧客がユーザーの種類に応じた UPN の処理方法をアプリ構成できるようにする予定です。 アプリ構成が可能になった後は、すべてのゲスト ユーザーが組織の従業員としてシームレスに SSO を使用できます。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure portal で Azure AD セキュリティ グループを作成する

1. **[Azure Active Directory] > [すべてのグループ]** をクリックします。

    ![Azure AD セキュリティ グループを作成する](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. **[新しいグループ]** をクリックします。

    ![Azure AD セキュリティ グループを作成する](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. **[グループの種類]** 、 **[グループ名]** 、 **[グループの説明]** 、 **[メンバーシップの種類]** を入力します。 矢印をクリックしてメンバーを選択し、グループに追加するメンバーを検索またはクリックします。 **[選択]** をクリックして選択したメンバーを追加した後、 **[作成]** をクリックします。

    ![Azure AD セキュリティ グループを作成する](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Azure Active Directory セキュリティ グループをオンプレミスの SharePoint に割り当てるには、[AzureCP](https://yvand.github.io/AzureCP/) をオンプレミスの SharePoint ファームにインストールして構成するか、代わりの SharePoint 用カスタム クレーム プロバイダーを開発して構成する必要があります。  AzureCP を使用しない場合、独自のカスタム クレーム プロバイダーを作成する方法について詳しくは、ドキュメントの末尾にある詳細情報セクションをご覧ください。

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>オンプレミスの SharePoint へのアクセスをセキュリティ グループに許可する

**アプリの登録でセキュリティ グループとアクセス許可を構成する**

1. Azure portal で、 **[Azure Active Directory]** を選択し、 **[アプリの登録]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. 検索ボックスに「**SharePoint on-premises**」と入力して選択します。

    ![結果リストの SharePoint オンプレミス](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. **[マニフェスト]** をクリックします。

    ![マニフェストのオプション](./media/sharepoint-on-premises-tutorial/manifest.png)

4. `groupMembershipClaims`: `NULL` を、`groupMembershipClaims`: `SecurityGroup` に変更します。 その後、[保存] をクリックします

    ![マニフェストの編集](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. **[設定]** をクリックし、 **[必要なアクセス許可]** をクリックします。

    ![必要なアクセス許可](./media/sharepoint-on-premises-tutorial/settings.png)

6. **[追加]** をクリックし、 **[API を選択します]** をクリックします。

    ![API アクセス](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. **Windows Azure Active Directory** と **Microsoft Graph API** の両方を追加しますが、選択できるのは一度に 1 つだけです。

    ![API の選択](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Windows Azure Active Directory を選択し、[ディレクトリ データの読み取り] をオンにして、[選択] をクリックします。 戻って Microsoft Graph を追加し、その [ディレクトリ データの読み取り] もオンにします。  [選択]、[完了] の順にクリックします。

    ![アクセスの有効化](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. 次に、[必要なアクセス許可] で、 **[アクセス許可の付与]** をクリックしてから、[はい] をクリックしてアクセス許可を付与します。

    ![権限の許可](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > 通知を調べて、アクセス許可が正常に付与されたかどうかを確認します。  されていない場合、AzureCP は正しく機能せず、Azure Active Directory セキュリティ グループでオンプレミスの SharePoint を構成することはできません。

10. オンプレミスの SharePoint ファームまたは代わりのカスタム クレーム プロバイダー ソリューションで、AzureCP を構成します。  この例では、AzureCP を使用しています。

    > [!NOTE]
    > AzureCP は Microsoft 製品ではないか、または Microsoft のテクニカル サポートでサポートされていないことに注意してください。 [https://yvand.github.io/AzureCP/](https://yvand.github.io/AzureCP/) に従って、AzureCP をダウンロードし、オンプレミスの SharePoint ファームにインストールして構成します 

11. **オンプレミス SharePoint で Azure Active Directory セキュリティ グループにアクセス許可を付与する**: グループは、オンプレミスの SharePoint 内のアプリケーションへのアクセス許可を付与される必要があります。  次の手順を使用して、Web アプリケーションにアクセスするためのアクセス許可を設定します。

12. サーバーの全体管理で、[アプリケーション構成の管理]、[Web アプリケーションの管理] の順にクリックしてから、Web アプリケーションを選択してリボンをアクティブ化し、[ユーザー ポリシー] をクリックします。

    ![サーバーの全体管理](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. [Web アプリケーションのポリシー] で、[ユーザーの追加] をクリックし、ゾーンを選択して、[次へ] をクリックします。  アドレス帳をクリックします。

    ![Web アプリケーションのポリシー](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. 次に、Azure Active Directory セキュリティ グループを探して追加し、[OK] をクリックします。

    ![セキュリティ グループの追加](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. アクセス許可を選択して、[完了] をクリックします。

    ![セキュリティ グループの追加](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. [Web アプリケーションのポリシー] の下を見ると、Azure Active Directory グループが追加されています。  グループの要求では、ユーザー名に対する Azure Active Directory セキュリティ グループ オブジェクト ID が示されています。

    ![セキュリティ グループの追加](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. SharePoint サイト コレクションを参照し、そこにもグループを追加します。 [サイトの設定] をクリックし、[サイトの権限] と [アクセス許可​​の付与] をクリックします。  グループ ロール要求を検索し、アクセス許可レベルを割り当てて、[共有] をクリックします。

    ![セキュリティ グループの追加](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>複数の Web アプリケーションに対して 1 つの信頼できる ID プロバイダーを構成する

構成は 1 つの Web アプリケーションに機能しますが、複数の Web アプリケーションに対して同じ信頼できる ID プロバイダーを使用する場合は、追加の構成が必要です。 たとえば、URL `https://portal.contoso.local` を使用するように Web アプリケーションを拡張し、`https://sales.contoso.local` にユーザーを認証する必要があるとします。 このためには、WReply パラメーターを有効にするように ID プロバイダーを更新し、Azure AD でアプリケーションの登録を更新して応答 URL を追加する必要があります。

1. Azure portal で Azure AD ディレクトリを開きます。 **[アプリの登録]** をクリックし、 **[アプリケーションをすべて表示]** をクリックします。 以前に作成したアプリケーションをクリックします (SharePoint SAML 統合)。

2. **[設定]** をクリックします。

3. [設定] ブレードで **[応答 URL]** をクリックします。

4. URL に `/_trust/default.aspx` を追加して追加の Web アプリケーションの URL を追加して (`https://sales.contoso.local/_trust/default.aspx` など)、 **[保存]** をクリックします。

5. SharePoint サーバーで **SharePoint 2016 管理シェル**を開き、以前に使用した信頼できる ID トークンの発行者の名前を使用して、次のコマンドを実行します。

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```

6. 全体管理では、Web アプリケーションに移動し、既存の信頼できる ID プロバイダーを有効にします。 カスタム サインイン ページ `/_trust/` としてサインイン ページ の URL を構成する必要もあります。

7. 全体管理で、Web アプリケーションをクリックして、 **[ユーザー ポリシー]** を選択します。 この記事で説明済みの適切なアクセス許可を使用してユーザーを追加します。

### <a name="fixing-people-picker"></a>メンバー選択の修正

Azure AD からの ID を使用して SharePoint 2016 にユーザーがサインインできるようになりましたが、ユーザー エクスペリエンスを改善する余地がまだあります。 たとえば、ユーザーを検索すると、ユーザーの選択ウィンドウに複数の検索結果が表示されます。 要求のマッピングで作成された 3 種類の要求ごとに検索結果が表示されています。 ユーザーの選択ウィンドウを使用してユーザーを選択するには、ユーザー名を正確に入力し、**名前**要求結果を選択する必要があります。

![要求検索結果](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

検索条件値の検証機能がないため、スペル ミスや、ユーザーが誤って正しくない種類の要求を選択して、たとえば **[姓]** 要求を割り当てる可能性があります。 これにより、ユーザーがリソースにアクセスできない可能性があります。

このシナリオのために、[AzureCP](https://yvand.github.io/AzureCP/) と呼ばれるオープン ソース ソリューションがあり、SharePoint 2016 のカスタム クレーム プロバイダーを提供します。 このソリューションは、Microsoft Graph API を使用してユーザー入力を解決し、検証します。 詳細については [AzureCP](https://yvand.github.io/AzureCP/) を参照してください。

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Azure portal で Azure AD セキュリティ グループを割り当てる

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[SharePoint on-premises]\(オンプレミスの SharePoint\)** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で「**SharePoint on-premises**」と入力して選択します。

    ![アプリケーションの一覧内の SharePoint オンプレミスのリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックします。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. 使用するセキュリティ グループを検索してから、グループをクリックして [メンバーの選択] セクションに追加します。 **[選択]** をクリックし、 **[割り当て]** をクリックします。

    ![セキュリティ グループの検索](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > メニュー バーの通知で、グループが Azure portal のエンタープライズ アプリケーションに正常に割り当てられたことを確認します。

### <a name="create-sharepoint-on-premises-test-user"></a>SharePoint オンプレミスのテスト ユーザーを作成する

このセクションでは、SharePoint オンプレミスで Britta Simon というユーザーを作成します。 [SharePoint オンプレミス サポート チーム](https://support.office.com/)と連携しながら、SharePoint オンプレミス プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで SharePoint オンプレミスのタイルをクリックすると、SSO を設定した SharePoint オンプレミスに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)