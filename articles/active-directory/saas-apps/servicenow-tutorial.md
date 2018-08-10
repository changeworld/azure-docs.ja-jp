---
title: 'チュートリアル: Azure Active Directory と ServiceNow の統合 | Microsoft Docs'
description: Azure Active Directory と ServiceNow の間のシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 5d5c4d5e26fa21488dd637805a4c22bd3ed18a7f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421085"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>チュートリアル: Azure Active Directory と ServiceNow の統合

このチュートリアルでは、ServiceNow と Azure Active Directory (Azure AD) を統合する方法について説明します。

ServiceNow と Azure AD の統合には、次の利点があります。

- ServiceNow にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで ServiceNow に自動的にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ServiceNow と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ServiceNow の場合は、Calgary バージョン以降の ServiceNow のインスタンスまたはテナント
- ServiceNow Express の場合は、Helsinki バージョン以降の ServiceNow Express のインスタンス
- ServiceNow のテナントで [Multiple Provider Single Sign On プラグイン](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)が有効になっている必要があります。 このプラグインを有効にするには、[サービス要求を送信してください](https://hi.service-now.com)。
- 自動構成のために、ServiceNow の Multi-Provider プラグインを有効にします。

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ServiceNow の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-servicenow-from-the-gallery"></a>ギャラリーからの ServiceNow の追加
Azure AD への ServiceNow の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ServiceNow を追加する必要があります。

**ギャラリーから ServiceNow を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**ServiceNow**」と入力し、結果ウィンドウで **[ServiceNow]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の ServiceNow](./media/servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ServiceNow で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ServiceNow ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ServiceNow の関連ユーザーの間で、リンク関係が確立されている必要があります。

ServiceNow で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

ServiceNow で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[ServiceNow 向け Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on-for-servicenow)** - ユーザーがこの機能を使用できるようにします。
1. **[ServiceNow Express 向け Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on-for-servicenow-express)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[ServiceNow テスト ユーザーの作成](#create-a-servicenow-test-user)** - ServiceNow で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>ServiceNow 向け Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、ServiceNow アプリケーションでシングル サインオンを構成します。

**ServiceNow で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **ServiceNow** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

1. **[ServiceNow のドメインと URL]** セクションで、次の手順に従います。

    ![[ServiceNow のドメインと URL] のシングル サインオン情報](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. **[サインオン URL]** ボックスに、`https://<instance-name>.service-now.com/navpage.do` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<instance-name>.service-now.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値を、このチュートリアルの後半で説明する実際のサインオン URL と識別子に更新する必要があります。

1. **[SAML 署名証明書]** セクションで、次の手順を実行します。 

    ![証明書のダウンロードのリンク](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に貼り付けます。このアプリのフェデレーション メタデータ URL はこのチュートリアルで後で使用されます。

    b. **[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/servicenow-tutorial/tutorial_general_400.png)

1. ServiceNow アプリケーションに管理者としてサインオンします。

1. **[Integration - Multiple Provider Single Sign-On Installer (統合 - 複数プロバイダーのシングル サインオン インストーラー)]** プラグインをアクティブ化するには、次の手順に従います。

    a. 左側のナビゲーション ウィンドウの検索バーで **}[System Definition]\(システム定義\)** セクションを検索し、**[Plugins]\(プラグイン\)** をクリックします。

    ![プラグインをアクティブにする](./media/servicenow-tutorial/tutorial_servicenow_03.png "プラグインをアクティブにする")

     b. "**Integration - Multiple Provider Single Sign-On Installer**" を検索します。

     ![プラグインをアクティブにする](./media/servicenow-tutorial/tutorial_servicenow_04.png "プラグインをアクティブにする")

    c. プラグインを選択します。 右クリックして **[Activate/Upgrade]** を選択します。

    d. **[Activate (アクティブ化)]** ボタンをクリックします。

1. **ServiceNow** は自動と手動の 2 つの方法で構成できます。

1. **ServiceNow** を自動的に構成するには、以下の手順に従います。

    a. Azure Portal で **ServiceNow** の [シングル サインオン] ページに戻ります。

    b. SAML ベースの認証に対応するため、Azure AD による ServiceNow の自動構成を行う ServiceNow 用のワン クリック構成サービスが提供されています。 このサービスを有効にするには、**[ServiceNow 構成]** セクションに移動し、**[ServiceNow を構成する]** をクリックして [サインオンの構成] ウィンドウを開きます。

    ![Configure single sign-on](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    c. ServiceNow のインスタンス名、管理者のユーザー名、および管理者パスワードを **[サインオンの構成]** フォームに入力し、**[今すぐ構成]** をクリックします。 ここで指定する管理者のユーザー名には、ServiceNow で **security_admin** ロールが割り当てられている必要があります。 これとは別に、ServiceNow を手動で構成して Azure AD を SAML ID プロバイダーとして使用させる方法があります。**[シングル サインオンを手動で構成する]** をクリックして、[クイック リファレンス] セクションから**サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** をコピーします。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/configure.png "Configure app URL")

    d. ServiceNow アプリケーションに管理者としてサインオンします。

    e. 自動構成では、必要なすべての設定が **ServiceNow** 側で構成されますが、**[X.509 証明書]** は既定で無効です。 ServiceNow でご使用の ID プロバイダーに手動でマップする必要があります。 同様に次の手順に従ってください。
    
    * 左側のナビゲーション ウィンドウで、**[Multi-Provider SSO]** の下の **[ID プロバイダー]** をクリックします。

      ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configure single sign-on")

    * 自動的に生成された ID プロバイダーをクリックします。

      ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configure single sign-on")

    * **[X.509 証明書]** セクションまで下方向にスクロールします。 **[編集]** を選択します。

      ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configure single sign-on")
    
    * 証明書を選択し、右矢印のアイコンをクリックして証明書を追加します。

      ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configure single sign-on")

    * **[Save]** をクリックします。

    * ページの右上隅にある **[アクティブ化]** をクリックします。

1. **ServiceNow** を手動で構成するには、以下の手順に従います。

1. ServiceNow アプリケーションに管理者としてサインオンします。

1. 左側のナビゲーション ウィンドウの検索バーで **[Multi-Provider SSO]** セクションを検索し、**[Properties]** をクリックします。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configure app URL")

1. **[Multiple Provider SSO Properties]** ダイアログで、次の手順を実行します。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/ic7694981.png "Configure app URL")

    a. **[Enable multiple provider SSO]** で **[Yes]** を選択します。

    b. **[Enable Auto Importing of users from all identity providers into the user table]** で、**[Yes]** を選択します。

    c. **[Enable debug logging for the multiple provider SSO integration]** で **[Yes]** を選択します。

    d. **[The field on the user table that...]** ボックスに、「**user_name**」と入力します。

    e. **[Save]** をクリックします。

1. 左側のナビゲーション ウィンドウの検索バーで **[Multi-Provider SSO]** セクションを検索し、**[x509 Certificates]** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_05.png "Configure single sign-on")

1. **[x509 Certificates]** ダイアログで、**[New]** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694974.png "Configure single sign-on")

1. **[X.509 Certificates]** ダイアログで、次の手順を実行します。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694975.png "Configure single sign-on")

    a. **[Name]** ボックスに、構成の名前を入力します (例: **TestSAML2.0**)。

    b. **[アクティブ]** を選択します。

    c. **[Format]** で **[PEM]** を選択します。

    d. **[Type]** で **[Trust Store Cert]** を選択します。

    e. Azure からダウンロードした Base64 エンコードの証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[PEM Certificate]** ボックスに貼り付けます。

     f. **[送信]** をクリックします。

1. 左側のナビゲーション ウィンドウで、 **[Identity Providers]** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configure single sign-on")

1. **[Identity Providers]\(ID プロバイダー\)** ダイアログ ボックスで、**[New]\(新規\)** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694977.png "Configure single sign-on")

1. **[Identity Providers]\(ID プロバイダー\)** ダイアログ ボックスで、**[SAML2 Update1?]** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694978.png "Configure single sign-on")

1. [SAML2 Update1 Properties] ダイアログで、次の手順を実行します。

    ![シングル サインオンの構成](./media/servicenow-tutorial/idp.png "Configure single sign-on")

    a. **[Import Identity Provider Metadata]** ダイアログ ボックスで **[URL]** オプションを選択します。

    b. Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** を貼り付けます。

    c. **[インポート]** をクリックします。

1. IdP メタデータ URL が読み取られ、すべてのフィールド情報が設定されます。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694982.png "Configure single sign-on")

    a. **[Name]** ボックスに、構成の名前を入力します (例: **SAML 2.0**)。
    
    b. **[ServiceNow Homepage]** の値をコピーして、Azure Portal の **[ServiceNow のドメインと URL]** セクションにある **[サインオン URL]** ボックスに貼り付けます。

    > [!NOTE]
    > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

    c. **[Entity ID / Issuer]** の値をコピーして、Azure Portal の **[ServiceNow のドメインと URL]** セクションにある **[識別子]** ボックスに貼り付けます。

    d. **[詳細設定]** をクリックします。 ServiceNow のデプロイでユーザーを一意に識別するためのフィールドに応じて、**[User Field]** ボックスに「**email**」または「**user_id**」と入力します。

    > [!NOTE]
    > SAML トークンの一意の識別子として Azure AD ユーザー ID (ユーザーのプリンシパル名) か電子メール アドレスを出力するように Azure AD を構成できます。そのためには、Azure Portal で **[ServiceNow]、[属性]、[シングル サインオン]** セクションの順に移動し、目的のフィールドを **nameidentifier** 属性にマッピングします。 Azure AD に格納される選択した属性 (ユーザー プリンシパル名など) の値と、ServiceNow に格納される入力したフィールド (user_name など) の値が一致している必要があります。

    e. **[x509 Certificate]** で、前の手順で作成した証明書を一覧表示します。

    > [!NOTE]
    > ServiceNow では、テスト接続ボタンをクリックしないで Idp をアクティブ化することはできません。これをオーバーライドするには、以下の手順に従います。

1. 構成の一環として作成した新しい ID プロバイダーのメニュー アイコンをクリックし、一覧から **[copy sys_id]\(sys_id をコピー\)** を選択します。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694992.png "Configure single sign-on")

1. 左上の検索ボックスに「**sys_properties.list**」を入力して、Enter キーを押します。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694993.png "Configure single sign-on")

1. **[新規]** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694994.png "Configure single sign-on")

1. **[システムのプロパティ]** セクションで、次の手順を実行します。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694995.png "Configure single sign-on")

    a. [名前] テキスト ボックスに値 `glide.authenticate.sso.redirect.idp` を入力します。

    b. **[値]** テキスト ボックスに、前の手順でコピーした copy sys_id 値を貼り付けます。

    c. **[プライベート]** を選択します。

    d. **[送信]** をクリックします。

1. **[新規]** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694994.png "Configure single sign-on")

1. **[システムのプロパティ]** セクションで、次の手順を実行します。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694996.png "Configure single sign-on")

    a. [名前] テキスト ボックスに値 `glide.authenticate.multisso.test.connection.mandatory` を入力します。

    b. **[値]** テキスト ボックスに「**false**」を入力します。

    c. **[送信]** をクリックします。

1. 上記の手順を実行すると、新しい ID プロバイダーをアクティブ化できるようになり、SSO が作動します。

> [!NOTE]
> また、新たに匿名でウィンドウを使用して新しい Idp 構成をテストする必要があることに注意してください。

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>ServiceNow Express 向け Azure AD シングル サインオンの構成

1. Azure Portal の **ServiceNow** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

1. **[ServiceNow のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. **[サインオン URL]** ボックスに、`https://<instance-name>.service-now.com/navpage.do` のパターンを使用して値を入力します。

    b. **[識別子]** ボックスに、`https://<instance-name>.service-now.com` の形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[ServiceNow クライアント サポート チーム](https://www.servicenow.com/support/contact-support.html)に問い合わせてください。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/servicenow-tutorial/tutorial_general_400.png)

1. SAML ベースの認証に対応するため、Azure AD による ServiceNow の自動構成を行う ServiceNow 用のワン クリック構成サービスが提供されています。 このサービスを有効にするには、**[ServiceNow 構成]** セクションに移動し、**[ServiceNow を構成する]** をクリックして [サインオンの構成] ウィンドウを開きます。

    ![Configure single sign-on](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

1. ServiceNow のインスタンス名、管理者のユーザー名、および管理者パスワードを **[サインオンの構成]** フォームに入力し、**[今すぐ構成]** をクリックします。 ここで指定する管理者のユーザー名には、ServiceNow で **security_admin** ロールが割り当てられている必要があります。 これとは別に、ServiceNow を手動で構成して Azure AD を SAML ID プロバイダーとして使用させる方法があります。**[シングル サインオンを手動で構成する]** をクリックして、[クイック リファレンス] セクションから**サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** をコピーします。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/configure.png "Configure app URL")

1. ServiceNow Express アプリケーションに管理者としてサインオンします。

1. 左側にあるナビゲーション ウィンドウで、**[Single Sign-On (シングル サインオン)]** をクリックします。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/ic7694980ex.png "Configure app URL")

1. **[Single Sign-On (シングル サインオン)]** ダイアログで右上の構成アイコンをクリックし、次のプロパティを設定します。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/ic7694981ex.png "Configure app URL")

    a. **[Enable multiple provider SSO (複数プロバイダー SSO を有効にする)]** を右に切り替えます。
    
    b. **[Enable debug logging for the multiple provider SSO integration (複数プロバイダー SSO 統合でのデバッグ ログの有効化)]** を右に切り替えます。
    
    c. **[The field on the user table that...]** ボックスに、「**user_name**」と入力します。

1. **[Single Sign-On (シングル サインオン)]** ダイアログで、**[Add New Certificate (新しい証明書の追加)]** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694973ex.png "Configure single sign-on")

1. **[X.509 Certificates]** ダイアログで、次の手順を実行します。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694975.png "Configure single sign-on")

    a. **[Name]** ボックスに、構成の名前を入力します (例: **TestSAML2.0**)。

    b. **[アクティブ]** を選択します。

    c. **[Format]** で **[PEM]** を選択します。

    d. **[Type]** で **[Trust Store Cert]** を選択します。

    e. Azure Portal からダウンロードした Base64 エンコードの証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[PEM Certificate]** ボックスに貼り付けます。

    f. **[Update]\(更新\)** をクリックします。

1. **[Single Sign-On (シングル サインオン)]** ダイアログで、**[Add New IdP (新しい IdP の追加)]** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694976ex.png "Configure single sign-on")

1. **[Add New Identity Provider (新しい ID プロバイダーの追加)]** ダイアログの **[Configure Identity Provider (ID プロバイダーの構成)]** で、次の手順に従います。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694982ex.png "Configure single sign-on")

    a. **[Name]** ボックスに、構成の名前を入力します (例: **SAML 2.0**)。

    b. **[Identity Provider URL]\(ID プロバイダー URL\)** フィールドに、Azure Portal からコピーした **ID プロバイダー ID** の値を貼り付けます。
    
    c. **[Identity Provider's AuthnRequest]\(ID プロバイダーの AuthnRequest\)** フィールドに、Azure Portal からコピーした**認証要求 URL** の値を貼り付けます。

    d. **[Identity Provider's SingleLogoutRequest]\(ID プロバイダーの SingleLogoutRequest\)** フィールドに、Azure Portal からコピーした**シングル サインアウト サービス URL** の値を貼り付けます。

    e. **[Identity Provider Certificate (ID プロバイダー証明書)]** で、前の手順で作成した証明書を選択します。

1. **[Advanced Settings (詳細設定)]** をクリックし、**[Additional Identity Provider Properties (追加の ID プロバイダーのプロパティ)]** で、次の手順に従います。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694983ex.png "Configure single sign-on")

    a. **[Protocol Binding for the IDP's SingleLogoutRequest]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**」と入力します。

    b. **[NameID Policy (NameID ポリシー)]** ボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**」と入力します。

    c. **[AuthnContextClassRef Method (AuthnContextClassRef メソッド)]** に「`http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`」と入力します。

    d. **[Create an AuthnContextClass]** をオフにします。

1. **[Additional Service Provider Properties (追加のサービス プロバイダーのプロパティ)]** で、次の手順に従います。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694984ex.png "Configure single sign-on")

    a. **[ServiceNow Homepage]** ボックスに ServiceNow インスタンス ホームページの URL を入力します。

    > [!NOTE]
    > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

    b. **[Entity ID / Issuer]** ボックスに、ServiceNow テナントの URL を入力します。

    c. **[Audience URI (対象ユーザー URI)]** ボックスに、ServiceNow テナントの URL を入力します。

    d. **[Clock Skew]** ボックスに「**60**」と入力します。

    e. ServiceNow のデプロイでユーザーを一意に識別するためのフィールドに応じて、**[User Field]** ボックスに「**email**」または「**user_id**」と入力します。

    > [!NOTE]
    > SAML トークンの一意の識別子として Azure AD ユーザー ID (ユーザーのプリンシパル名) か電子メール アドレスを出力するように Azure AD を構成できます。そのためには、Azure Portal で **[ServiceNow]、[属性]、[シングル サインオン]** セクションの順に移動し、目的のフィールドを **nameidentifier** 属性にマッピングします。 Azure AD に格納される選択した属性 (ユーザー プリンシパル名など) の値と、ServiceNow に格納される入力したフィールド (user_name など) の値が一致している必要があります。

    f. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/servicenow-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/servicenow-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/servicenow-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/servicenow-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-servicenow-test-user"></a>ServiceNow テスト ユーザーの作成

このセクションの目的は、ServiceNow で Britta Simon というユーザーを作成することです。 ServiceNow では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](servicenow-provisioning-tutorial.md)を参照してください。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合、[ServiceNow クライアント サポート チーム](https://www.servicenow.com/support/contact-support.html)に問い合わせる必要があります

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ServiceNow へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を ServiceNow に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[ServiceNow]** を選択します。

    ![アプリケーションの一覧の ServiceNow リンク](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ServiceNow] タイルをクリックすると、自動的に ServiceNow アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](servicenow-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/servicenow-tutorial/tutorial_general_01.png
[2]: ./media/servicenow-tutorial/tutorial_general_02.png
[3]: ./media/servicenow-tutorial/tutorial_general_03.png
[4]: ./media/servicenow-tutorial/tutorial_general_04.png

[100]: ./media/servicenow-tutorial/tutorial_general_100.png

[200]: ./media/servicenow-tutorial/tutorial_general_200.png
[201]: ./media/servicenow-tutorial/tutorial_general_201.png
[202]: ./media/servicenow-tutorial/tutorial_general_202.png
[203]: ./media/servicenow-tutorial/tutorial_general_203.png
