---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Confluence SAML SSO by Microsoft の統合 | Microsoft Docs
description: Azure Active Directory と Confluence SAML SSO by Microsoft の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2020
ms.author: jeedes
ms.openlocfilehash: 34365a8bd7a15f502aa89a966adb14807e802cc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737001"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Confluence SAML SSO by Microsoft の統合

このチュートリアルでは、Confluence SAML SSO by Microsoft と Azure Active Directory (Azure AD) を統合する方法について説明します。 Confluence SAML SSO by Microsoft と Azure AD を統合すると、次のことができます。

* Confluence SAML SSO by Microsoft にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Confluence SAML SSO by Microsoft に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。


## <a name="description"></a>説明:

Microsoft Azure Active Directory アカウントと Atlassian Confluence サーバーを使用して、シングル サインオンを有効にします。 これにより、組織のすべてのユーザーが、Azure AD の資格情報を使用して Confluence アプリケーションにサインインできます。 このプラグインは、フェデレーションに SAML 2.0 を使用します。

## <a name="prerequisites"></a>前提条件

Confluence SAML SSO by Microsoft と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 64 ビットの Windows サーバー (オンプレミスまたはクラウド IaaS インフラストラクチャ) にインストールされている Confluence サーバー アプリケーション
- Confluence サーバーの HTTPS が有効になっていること
- Confluence プラグインがサポートされているバージョンであること (下記のセクションをご覧ください)
- Confluence サーバーが認証のためにインターネット、特に Azure AD ログイン ページにアクセスでき、Azure AD からトークンを受け取れること
- 管理者の資格情報が Confluence に設定されていること
- WebSudo が Confluence で無効になっていること
- テスト ユーザーが Confluence サーバー アプリケーションで作成されていること

> [!NOTE]
> このチュートリアルの手順をテストする場合、Confluence の運用環境を使用しないことをお勧めします。 最初にアプリケーションの開発環境またはステージング環境で統合をテストし、そのあとに運用環境を使用してください。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

開始するには、次が必要です。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Confluence SAML SSO by Microsoft でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="supported-versions-of-confluence"></a>サポートされている Confluence のバージョン

現時点では、次のバージョンの Confluence がサポートされています。

- Confluence: 5.0 から 5.10
- Confluence: 6.0.1 から 6.15.9
- Confluence: 7.0.1 から 7.9.3

> [!NOTE]
> Confluence プラグインは、Ubuntu Version 16.04 でも動作することに注意してください

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Confluence SAML SSO by Microsoft では、**SP** によって開始される SSO がサポートされます

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>ギャラリーからの Confluence SAML SSO by Microsoft の追加

Azure AD への Confluence SAML SSO by Microsoft の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Confluence SAML SSO by Microsoft を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Confluence SAML SSO by Microsoft**」と入力します。
1. 結果のパネルから **[Confluence SAML SSO by Microsoft]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-confluence-saml-sso-by-microsoft"></a>Confluence SAML SSO by Microsoft の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Confluence SAML SSO by Microsoft に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Confluence SAML SSO by Microsoft の関連ユーザーの間で、リンク関係を確立する必要があります。

Confluence SAML SSO by Microsoft で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Confluence SAML SSO by Microsoft の SSO の構成](#configure-confluence-saml-sso-by-microsoft-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Confluence SAML SSO by Microsoft テスト ユーザーの作成](#create-confluence-saml-sso-by-microsoft-test-user)** - Confluence SAML SSO by Microsoft で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Confluence SAML SSO by Microsoft** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<domain:port>/` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 名前付き URL である場合は、ポートは省略できます。 これらの値は Confluence プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に Confluence SAML SSO by Microsoft へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Confluence SAML SSO by Microsoft]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Confluence SAML SSO by Microsoft の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Confluence インスタンスに管理者としてサインインします。

1. 歯車をポイントし、 **[Add-ons]\(アドオン\)** をクリックします。

    ![選択されている "歯車" アイコンと、ドロップダウン メニューで強調表示されている [Add-ons]\(アドオン\) を示すスクリーンショット。](./media/confluencemicrosoft-tutorial/addon1.png)

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=56503)からプラグインをダウンロードします。 **[Upload add-on]\(アドオンのアップロード\)** メニューを使用して、Microsoft が提供しているプラグインを手動でアップロードします。 プラグインのダウンロードは、[Microsoft サービス規約](https://www.microsoft.com/servicesagreement/)の対象です。

    ![[Upload add-on]\(アドオンのアップロード\) アクションが選択された [アドオンの管理] ページを示すスクリーンショット。](./media/confluencemicrosoft-tutorial/addon12.png)

1. Confluence のリバース プロキシ シナリオまたはロード バランサー シナリオを実行するには、次の手順を実行します。

    > [!NOTE]
    > 以下の手順に従って最初にサーバーを構成した後、プラグインをインストールする必要があります。

    a. JIRA サーバー アプリケーションの **server.xml** ファイルの **connector** ポートに以下の属性を追加します。

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    !["connector" ポートに属性が追加された "server.xml" ファイルを示すスクリーンショット。](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. プロキシ/ロード バランサーに従って、 **[システム設定]** の **[ベース URL]** を変更します。

    ![[ベース URL] が強調表示された [管理] - [設定] ページを示すスクリーンショット。](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. プラグインがインストールされると、 **[アドオンの管理]** セクションの **[User Installed]\(ユーザー インストール\)** アドオン セクションに表示されます。 **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![[Configure]\(構成\) ボタンが強調表示された [User Installed]\(ユーザー インストール\) セクションを示すスクリーンショット。](./media/confluencemicrosoft-tutorial/addon15.png)

1. 構成ページで次の手順を実行します。

    ![シングル サインオンの構成ページを示すスクリーンショット。](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > メタデータの解決でエラーが発生しないように、アプリに対してマップされている証明書が 1 つしかないようにします。 証明書が複数ある場合は、メタデータの解決の際に管理者に対してエラーが表示されます。

    1. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付け、 **[解決]** ボタンをクリックします。 IdP メタデータ URL が読み取られ、すべてのフィールド情報が設定されます。

    1. **識別子、応答 URL、サインオン URL** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]、[応答 URL]、[サインオン URL]** ボックスにそれぞれ貼り付けます。

    1. ユーザーのログイン画面に表示するボタン名を **[Login Button Name]\(ログイン ボタン名\)** に入力します。

    1. ユーザーのログイン画面に表示するボタンの説明を **[Login Button Description]\(ログイン ボタンの説明\)** に入力します。

    1. **[SAML User ID Locations]\(SAML ユーザー ID の場所\)** で、 **[User ID is in the NameIdentifier element of the Subject statement]\(Subject ステートメントの NameIdentifier 要素内のユーザー ID\)** 、または **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択します。  この ID は Confluence ユーザー ID である必要があります。 ユーザー ID が一致しない場合、システムはユーザーのサインインを許可しません。 

       > [!Note]
       > 既定の SAML ユーザー ID の場所は、名前識別子です。 属性オプションでこれを変更して、適切な属性名を入力できます。

    1. **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択する場合は、ユーザー ID が格納されている属性の名前を **[Attribute name]\(属性名\)** ボックスに入力します。 

    1. Azure AD でフェデレーション ドメイン (ADFS など) を使用している場合は、 **[Enable Home Realm Discovery]\(ホーム領域の検出を有効にする\)** をクリックして **[Domain Name]\(ドメイン名\)** を構成します。

    1. ADFS ベースでログインする場合は、 **[Domain Name]\(ドメイン名\)** にドメイン名を入力します。

    1. ユーザーが Confluence からサインアウトしたときに Azure AD からもサインアウトさせる場合は、 **[Enable Single Sign out]\(シングル サインアウトを有効にする\)** をオンにします。 

    1. Azure AD 資格情報でのみサインインする場合は、 **[Force Azure Login]\(Azure ログインを強制する)** チェックボックスを有効にします。

       > [!Note]
       > Azure ログインの強制を有効にしたときにログイン ページで管理ログインの既定のログイン フォームを有効にするには、ブラウザー URL にクエリ パラメーターを追加します。
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. **[Save (保存)]** ボタンをクリックして、設定を保存します。

       > [!NOTE]
       > インストールとトラブルシューティングについて詳しくは、[MS Confluence SSO コネクタ管理者ガイド](./ms-confluence-jira-plugin-adminguide.md)のページをご覧ください。 [FAQ](./ms-confluence-jira-plugin-adminguide.md) もご利用いただけます。

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Confluence SAML SSO by Microsoft のテスト ユーザーの作成

オンプレミス サーバーで Azure AD ユーザーの Confluence へのサインインを有効にするには、そのユーザーを Confluence SAML SSO by Microsoft にプロビジョニングする必要があります。 Confluence SAML SSO by Microsoft では、手動でプロビジョニングします。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として、オンプレミス サーバーの Confluence にサインインします。

1. 歯車をポイントし、 **[User management]\(ユーザー管理\)** をクリックします。

    ![従業員の追加](./media/confluencemicrosoft-tutorial/user1.png)

1. [Users]\(ユーザー\) セクションで、 **[Add users]\(ユーザーの追加\)** タブをクリックします。 **[Add a User]\(ユーザーの追加\)** ダイアログ ページで、次の手順に従います。

    ![[Add Users]\(ユーザーの追加\) タブが選択され、[Add a User]\(ユーザーの追加\) に情報が入力された [Confluence administration]\(Confluence の管理\) を示すスクリーンショット。](./media/confluencemicrosoft-tutorial/user2.png)

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザー (B.Simon など) の電子メールを入力します。

    b. **[フル ネーム]** ボックスに、ユーザー (B.Simon など) の氏名を入力します。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (B.Simon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、B.Simon のパスワードを入力します。

    e. **[Confirm Password]\(パスワードの確認\)** をクリックし、パスワードを再入力します。

    f. **[追加]** ボタンをクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Confluence SAML SSO by Microsoft のサインオン URL にリダイレクトされます。 

* Confluence SAML SSO by Microsoft のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリの [Confluence SAML SSO by Microsoft] タイルをクリックすると、Confluence SAML SSO by Microsoft のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Confluence SAML SSO by Microsoft を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。