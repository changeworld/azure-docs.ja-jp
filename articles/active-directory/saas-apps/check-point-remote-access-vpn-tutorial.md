---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Check Point Remote Secure Access VPN の統合 | Microsoft Docs'
description: Azure Active Directory と Check Point Remote Secure Access VPN の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2021
ms.author: jeedes
ms.openlocfilehash: 33bcb007c1cfd4d09c25fb4abbf9c5fcfb15b58f
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988750"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-remote-secure-access-vpn"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Check Point Remote Secure Access VPN の統合

このチュートリアルでは、Check Point Remote Secure Access VPN と Azure Active Directory (Azure AD) を統合する方法について説明します。 Check Point Remote Secure Access VPN と Azure AD を統合すると、次のことができます。

* Check Point Remote Secure Access VPN にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Check Point Remote Secure Access VPN に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Check Point Remote Secure Access VPN でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Check Point Remote Secure Access VPN では、**SP** Initiated SSO がサポートされます。

## <a name="adding-check-point-remote-secure-access-vpn-from-the-gallery"></a>ギャラリーからの Check Point Remote Secure Access VPN の追加

Azure AD への Check Point Remote Secure Access VPN の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Check Point Remote Secure Access VPN を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Check Point Remote Secure Access VPN**」と入力します。
1. 結果のパネルから **[Check Point Remote Secure Access VPN]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-check-point-remote-secure-access-vpn"></a>Check Point Remote Secure Access VPN の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Check Point Remote Secure Access VPN に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Check Point Remote Secure Access VPN の関連ユーザーとの間にリンク関係を確立する必要があります。

Check Point Remote Secure Access VPN に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Check Point Remote Secure Access VPN SSO の構成](#configure-check-point-remote-secure-access-vpn-sso)** - ユーザーがこの機能を使用できるようにします。

    1. **[Check Point Remote Secure Access VPN のテスト ユーザーの作成](#create-check-point-remote-secure-access-vpn-test-user)** - Check Point Remote Secure Access VPN で B.Simon に対応するユーザーを作成し、Azure AD のユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Check Point Remote Secure Access VPN** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    1. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<GATEWAY_IP>/saml-vpn/spPortal/ACS/ID/<IDENTIFIER_UID>`

    1. **[応答 URL]** ボックスに、`https://<GATEWAY_IP>/saml-vpn/spPortal/ACS/Login/<IDENTIFIER_UID>` のパターンを使用して URL を入力します

    1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<GATEWAY_IP>/saml-vpn/`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL で更新してください。 これらの値を取得するには、[Check Point Remote Secure Access VPN クライアント サポート チーム](mailto:support@checkpoint.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Check Point Remote Secure Access VPN のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Check Point Remote Secure Access VPN へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Check Point Remote Secure Access VPN]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-check-point-remote-secure-access-vpn-sso"></a>Check Point Remote Secure Access VPN SSO の構成

### <a name="configure-an-external-user-profile-object"></a>外部ユーザー プロファイル オブジェクトを構成する

> [!NOTE]
> このセクションは、オンプレミス Active Directory (LDAP) を使用しない場合のみ必須です。

**レガシ SmartDashboard で汎用ユーザー プロファイルを構成する**:

1. SmartConsole で、 **[Manage & Settings]\(管理と設定\) > [Blades]\(ブレード\)** に移動します。

1. **[Mobile Access]\(モバイル アクセス\)** セクションの **[Configure in SmartDashboard]\(SmartDashboard で構成\)** をクリックします。 レガシ SmartDashboard が表示されます。

1. **[Network Objects]\(ネットワーク オブジェクト\)** ペインの **[Users]\(ユーザー\)** をクリックします。

1. 空の領域を右クリックし、 **[New]\(新規\) > [External User Profile]\(外部ユーザー プロファイル\) > [Match all users]\(すべてのユーザーを照合する\)** を選択します。

1. **[External User Profile]\(外部ユーザー プロファイル\)** のプロパティを構成します。

    1. **[General Properties]\(全般プロパティ\)** ページで:
        * **外部ユーザー プロファイル** の名前フィールドは、既定の名前 (`generic`*) のままにします
        * **[Expiration Date]\(有効期限\)** フィールドで、適切な日付を設定します

    1. **[認証]** ページで、次の操作を実行します。
        * **[Authentication Scheme]\(認証スキーム\)** ボックスの一覧から [`undefined`]\(未定義\) を選択します
    1. **[Location]\(場所\)** 、 **[Time]\(時刻\)** 、 **[Encryption]\(暗号化\)** の各ページで:
        * その他の関連する設定を構成します
    1. **[OK]** をクリックします。

1. 上部ツール バーの **[Update]\(更新\)** をクリックします (または Ctrl + S キーを押します)。

1. SmartDashboard を閉じます。

1. SmartConsole で、Access Control Policy をインストールします。

### <a name="configure-remote-access-vpn"></a>Remote Access VPN を構成する

1. 適切な Security Gateway のオブジェクトを開きます。

1. [General Properties]\(全般プロパティ\) ページで、**IPSec VPN** Software Blade を有効にします。

1. 左側のツリーで、 **[IPSec VPN]** ページをクリックします。

1. **[This Security Gateway participates in the following VPN communities]\(この Security Gateway は次の VPN コミュニティに参加します\)** セクションの **[Add]\(追加\)** をクリックし、 **[Remote Access Community]\(リモート アクセス コミュニティ\)** を選択します。

1. 左側のツリーから **[VPN clients]\(VPN クライアント\) > [Remote Access]\(リモート アクセス\)** をクリックします。

1. **[Support Visitor Mode]\(ビジター モードのサポート\)** を有効にします。

1. 左側のツリーから **[VPN clients]\(VPN クライアント\) > [Office Mode]\(オフィス モード\)** をクリックします。

1. **[Allow Office Mode]\(オフィス モードを許可する\)** を選択し、適切なオフィス モード メソッドを選択します。

1. 左側のツリーから **[VPN Clients]\(VPN クライアント\) > [SAML Portal Settings]\(SAML ポータル設定\)** をクリックします。

1. [Main URL]\(メイン URL\) に、ゲートウェイの完全修飾ドメイン名が指定されていることを確認します。
このドメイン名の末尾は、自分が所属する組織によって登録された DNS サフィックスであることが必要です。
例: `https://gateway1.company.com/saml-vpn`

1. 証明書が、エンド ユーザーのブラウザーによって信頼されていることを確認します。

1. **[OK]** をクリックします。

### <a name="configure-an-identity-provider-object"></a>ID プロバイダー オブジェクトを構成する

1. Remote Access VPN に参加する各 Security Gateway について、次の手順を実行します。

1. SmartConsole > **[Gateways & Servers]\(ゲートウェイとサーバー\)** ビューで、 **[New]\(新規\) > [More]\(詳細\) > [User/Identity]\(ユーザーまたは ID\) > [Identity Provider]\(ID プロバイダー\)** をクリックします。

    ![新しい ID プロバイダーのスクリーンショット。](./media/check-point-remote-access-vpn-tutorial/identity-provider.png)

1. **[New Identity Provider]\(新しい ID プロバイダー\)** ウィンドウで、次の手順を実行します。

    ![[Identity Provider]\(ID プロバイダー\) セクションのスクリーンショット。](./media/check-point-remote-access-vpn-tutorial/new-identity-provider.png)

    a. **[Gateway]\(ゲートウェイ\)** フィールドで、SAML 認証を実行する必要があるセキュリティ ゲートウェイを選択します。

    b. **[Service]\(サービス\)** フィールドのドロップダウンから **[Remote Access VPN]** を選択します。

    c. **[Identifier(Entity ID)]\(識別子 (エンティティ ID)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** テキスト ボックスにこの値を貼り付けます。

    d. **[Reply URL]\(応答 URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキスト ボックスにこの値を貼り付けます。

    e. **[Import Metadata File]\(メタデータ ファイルのインポート\)** を選択して、Azure portal からダウンロードした **フェデレーション メタデータ XML** をアップロードします。

    > [!NOTE]
    > または、 **[Insert Manually]\(手動で挿入\)** を選択した後、**エンティティ ID** と **ログイン URL** の値を対応するフィールドに手動で貼り付け、Azure portal から取得した **証明書ファイル** をアップロードすることもできます。

    f. **[OK]** をクリックします。

### <a name="configure-the-identity-provider-as-an-authentication-method"></a>認証方法として ID プロバイダーを構成する

1. 適切な Security Gateway のオブジェクトを開きます。

1. **[VPN Clients]\(VPN クライアント\) > [Authentication]\(認証\)** ページで:

    a. **[Allow older clients to connect to this gateway]\(以前のクライアントがこのゲートウェイに接続できるようにする\)** チェック ボックスをオフにします。

    b. 新しいオブジェクトを追加するか、既存の領域を編集します。

    ![新しいオブジェクトを追加する画面のスクリーンショット。](./media/check-point-remote-access-vpn-tutorial/add-new-object.png)

1. 名前と表示名を入力し、認証方法を追加または編集します。MEP に参加する GW でログイン オプションが使用される場合、ユーザー エクスペリエンスを円滑にするために、名前は "SAMLVPN_" プレフィックスで始めるようにしてください。

    ![ログイン オプションに関するスクリーンショット。](./media/check-point-remote-access-vpn-tutorial/login-option.png)

1. **[Identity Provider]\(ID プロバイダー\)** オプションを選択し、緑色の `+` ボタンをクリックして、適切な ID プロバイダー オブジェクトを選択します。

    ![適切な ID プロバイダー オブジェクトを選択する画面のスクリーンショット。](./media/check-point-remote-access-vpn-tutorial/green-button.png)

1. [Multiple Logon Options]\(複数のログオン オプション\) ウィンドウの左ペインで、 **[User Directories]\(ユーザー ディレクトリ\)** を選択し、 **[Manual configuration]\(手動構成\)** を選択します。
2 つのオプションがあります。
    1. オンプレミスの Active Directory (LDAP) を使用したくない場合は、[External User Profiles]\(外部ユーザー プロファイル\) のみを選択して [OK] をクリックします。
    2. オンプレミスの Active Directory (LDAP) を使用したい場合は、[LDAP users]\(LDAP ユーザー\) のみを選択し、[LDAP Lookup Type]\(LDAP ルックアップ タイプ\) で [email]\(メール\) を選択します。 次に、[OK] をクリックします。

    ![手動構成のスクリーンショット。](./media/check-point-remote-access-vpn-tutorial/manual-configuration.png)

1. 管理データベースで必要な設定を構成します。

    1. SmartConsole を閉じます。

    2. GuiDBEdit ツールを使用して管理サーバーに接続します ([sk13009](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails&solutionid=sk13009) を参照)。

    3. 左上のペインで、 **[Edit]\(編集\) > [Network Objects]\(ネットワーク オブジェクト\)** に移動します。

    4. 右上のペインで、 **[Security Gateway object]\(セキュリティ ゲートウェイ オブジェクト\)** を選択します。

    5. 一番下のペインで、 **[realms_for_blades] > [vpn]** に移動します。

    6. オンプレミスの Active Directory (LDAP) を使用したくない場合は、 **[do_ldap_fetch]** を **false** に、 **[do_generic_fetch]** を **true** に設定します。 次に、 **[OK]** をクリックします オンプレミスの Active Directory (LDAP) を使用したい場合は、 **[do_ldap_fetch]** を **true** に、 **[do_generic_fetch]** を **false** に設定します。 次に、 **[OK]** をクリックします

    7. 該当するすべての Security Gateway について、手順 4. から手順 6. を繰り返します。

    8. **[ファイル]**  >  **[保存]** を選択して、すべての変更を保存します。

1. GuiDBEdit ツールを閉じます。

1. 各 Security Gateway と各 Software Blade には個別の設定があります。 各 Security Gateway と各 Software Blade の設定のうち、認証を使用する設定を確認します (VPN、Mobile Access、Identity Awareness)。

    * **[LDAP users]\(LDAP ユーザー\)** オプションは、LDAP を使用する Software Blade でのみ選択してください。

    * **[External user profiles]\(外部ユーザー プロファイル\)** オプションは、LDAP を使用しない Software Blade でのみ選択してください。

1. 各 Security Gateway に Access Control Policy をインストールします。

### <a name="vpn-ra-client-installation-and-configuration"></a>VPN RA クライアントのインストールと構成

1. VPN クライアントをインストールします。

1. ID プロバイダーのブラウザー モード (オプション) を設定します。

    既定では、Windows クライアントはその埋め込みブラウザーを、macOS クライアントは Safari を ID プロバイダーのポータルでの認証に使用します。 Windows クライアントで、この動作を変更して Internet Explorer を使用するには、次の手順を実行します。

    1. クライアント マシンで、プレーン テキスト エディターを管理者として開きます。

    2. テキスト エディターで `trac.defaults` ファイルを開きます。

       * 32 ビット Windows の場合: 

         `%ProgramFiles%\CheckPoint\Endpoint Connect\trac.defaults`

       * 64 ビット Windows の場合: 

         `%ProgramFiles(x86)%\CheckPoint\Endpoint Connect\trac.defaults`

    3. `idp_browser_mode` の値を `embedded` から `IE` に変更します。

    4. ファイルを保存します。

    5. Check Point Endpoint Security VPN クライアント サービスを再起動します。

   管理者として Windows コマンド プロンプトを開き、これらのコマンドを実行します。

   `# net stop TracSrvWrapper`

   `# net start TracSrvWrapper`

1. バックグラウンドで実行中のブラウザーを使用して認証を開始します。

    1. クライアント マシンで、プレーン テキスト エディターを管理者として開きます。

    2. テキスト エディターで `trac.defaults` ファイルを開きます。

        * 32 ビット Windows の場合: 

          `%ProgramFiles%\CheckPoint\Endpoint Connect\trac.defaults`

        * 64 ビット Windows の場合: 

          `%ProgramFiles(x86)%\CheckPoint\Endpoint Connect\trac.defaults`

        * macOS の場合:

          `/Library/Application Support/Checkpoint/Endpoint Security/Endpoint Connect/trac.defaults`

    3. `idp_show_browser_primary_auth_flow` の値を `false` に変更します。

    4. ファイルを保存します。

    5. Check Point Endpoint Security VPN クライアント サービスを再起動します。

       * Windows クライアントで、管理者として Windows コマンド プロンプトを開き、次のコマンドを実行します。

         `# net stop TracSrvWrapper`

         `# net start TracSrvWrapper`

       * macOS クライアントで、次を実行します。

         `sudo launchctl stop com.checkpoint.epc.service`

         `sudo launchctl start com.checkpoint.epc.service`

### <a name="create-check-point-remote-secure-access-vpn-test-user"></a>Check Point Remote Secure Access VPN テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Check Point Remote Secure Access VPN に作成します。 [Check Point Remote Secure Access VPN サポート チーム](mailto:support@checkpoint.com)と連携して、Check Point Remote Secure Access VPN プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

1. VPN クライアントを開き、 **[接続先]** をクリックします。

    ![[接続先] のスクリーンショット。](./media/check-point-remote-access-vpn-tutorial/connect.png)

1. ドロップダウンから **[Site]\(サイト\)** を選択し、 **[Connect]\(接続\)** をクリックします。

    ![サイトを選択する画面のスクリーンショット。](./media/check-point-remote-access-vpn-tutorial/site.png)

1. Azure AD のログイン ポップアップで、「**Azure AD のテスト ユーザーの作成**」セクションで作成した Azure AD の資格情報を使用してサインインします。

## <a name="next-steps"></a>次のステップ

Check Point Remote Secure Access VPN を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
