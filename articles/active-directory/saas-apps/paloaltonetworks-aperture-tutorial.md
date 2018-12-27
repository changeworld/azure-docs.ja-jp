---
title: 'チュートリアル: Azure Active Directory と Palo Alto Networks - Aperture の統合 | Microsoft Docs'
description: Azure Active Directory と Palo Alto Networks - Aperture の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 7bb4782fa390ad2cc324a79a1f544c3db062c921
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421551"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>チュートリアル: Azure Active Directory と Palo Alto Networks - Aperture の統合

このチュートリアルでは、Palo Alto Networks - Aperture と Azure Active Directory (Azure AD) を統合する方法について説明します。

Palo Alto Networks - Aperture と Azure AD の統合には、次のメリットがあります。

- Palo Alto Networks - Aperture にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Palo Alto Networks - Aperture にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Palo Alto Networks - Aperture の統合を構成するには、次のアイテムが必要です。

- Azure AD サブスクリプション
- Palo Alto Networks - Aperture でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Palo Alto Networks - Aperture の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>ギャラリーからの Palo Alto Networks - Aperture の追加
Azure AD への Palo Alto Networks - Aperture の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Palo Alto Networks - Aperture を追加する必要があります。

**ギャラリーから Palo Alto Networks - Aperture を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Palo Alto Networks - Aperture**」と入力し、結果ウィンドウで **[Palo Alto Networks - Aperture]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Palo Alto Networks - Aperture](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Palo Alto Networks - Aperture で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Palo Alto Networks - Aperture ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Palo Alto Networks - Aperture の関連ユーザーの間で、リンク関係が確立されている必要があります。

Palo Alto Networks - Aperture で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Palo Alto Networks - Aperture テスト ユーザーの作成](#create-a-palo-alto-networks---aperture-test-user)** - Palo Alto Networks - Aperture で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Palo Alto Networks - Aperture アプリケーションでシングル サインオンを構成します。

**Palo Alto Networks - Aperture で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Palo Alto Networks - Aperture** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

1. アプリケーションを **IDP** 開始モードで構成する場合は、**[Palo Alto Networks - Aperture のドメインと URL]** セクションで次の手順を実行します。

    ![[Palo Alto Networks - Aperture のドメインと URL] のシングル サインオン情報](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Palo Alto Networks - Aperture のドメインと URL] のシングル サインオン情報](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Palo Alto Networks - Aperture クライアント サポート チーム](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support)に連絡してください。 

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


1. **[Palo Alto Networks - Aperture Configuration]\(Palo Alto Networks - Aperture 構成\)** セクションで、**[Configure Palo Alto Networks - Aperture]\(Palo Alto Networks - Aperture の構成\)** をクリックし、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![[構成] リンク](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

1. 別の Web ブラウザー ウィンドウで、Palo Alto Networks - Aperture に管理者としてログインします。

1. 上部のメニュー バーで **[設定]** をクリックします。

    ![[設定] タブ](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

1. **[アプリケーション]** に移動し、メニューの左にある **[認証]** をクリックします。

    ![[認証] タブ](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
1. **[認証]** ページで、次の手順を実行します。
    
    ![[認証] タブ](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. **[シングル サインオン]** フィールドの **[Enable Single Sign-On(Supported SSP Providers are Okta, Onelogin)]\(シングル サインオンを有効にする (サポートされている SSP プロバイダーは Okta、Onelogin)\)** をオンにします。

    b. **[ID プロバイダー]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    c. **[ファイルの選択]** をクリックして、Azure AD からダウンロードした証明書を **[ID プロバイダー証明書]** フィールドにアップロードします。

    d. **[ID プロバイダー SSO URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    e. **[Aperture 情報]** セクションで IdP 情報を確認し、**[Aperture キー]** フィールドから証明書をダウンロードします。

    f. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Palo Alto Networks - Aperture テスト ユーザーの作成

このセクションでは、Palo Alto Networks - Aperture で Britta Simon というユーザーを作成します。 [Palo Alto Networks - Aperture Client サポート チーム](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support)と協力し合い、Palo Alto Networks - Aperture プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Palo Alto Networks - Aperture へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**を Palo Alto Networks - Aperture に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Palo Alto Networks - Aperture]** を選択します。

    ![アプリケーションの一覧の [Palo Alto Networks - Aperture] リンク](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Palo Alto Networks - Aperture のタイルをクリックすると、Palo Alto Networks - Aperture アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_203.png

