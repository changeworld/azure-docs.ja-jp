---
title: 'チュートリアル: Azure Active Directory と vxMaintain の統合 | Microsoft Docs'
description: Azure Active Directory と vxMaintain の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7e444692dfeab5ca14fbd896043cc28e2cbd8717
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433996"
---
# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>チュートリアル: Azure Active Directory と vxMaintain の統合

このチュートリアルでは、vxMaintain と Azure Active Directory (Azure AD) を統合する方法について説明します。

この統合には、いくつかの重要な利点があります。 次のことが行えます。

- vxMaintain にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが Azure AD アカウントによるシングル サインオン (SSO) を使って、vxMaintain に対して自動的にサインインできるようにできます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

vxMaintain と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- vxMaintain SSO が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

* ギャラリーからの vxMaintain の追加
* Azure AD シングル サインオンの構成とテスト

## <a name="add-vxmaintain-from-the-gallery"></a>ギャラリーからの vxMaintain の追加
Azure AD への vxMaintain の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に vxMaintain を追加する必要があります。

ギャラリーから vxMaintain を追加するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ][2]
    
1. アプリケーションを追加するには、**[すべてのアプリケーション]** ダイアログ ボックスで **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに、「 **vxMaintain**」と入力します。

    ![[シングル サインオン モード] ドロップダウン リスト](./media/vxmaintain-tutorial/tutorial_vxmaintain_search.png)

1. 結果リストで **[vxMaintain]** を選択し、**[追加]** を選択します。

    ![vxMaintain リンク](./media/vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、vxMaintain で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する vxMaintain ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーとそれに対応する vxMaintain ユーザーとの間にリンク関係を確立する必要があります。

リンク関係を確立するには、vxMaintain の**ユーザー名**の値を Azure AD の**ユーザー名**の値として割り当てます。

vxMaintain で Azure AD の SSO を構成してテストするには、次の構成要素を完了してください。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、次の手順で Azure Portal で Azure AD SSO を有効にし、vxMaintain アプリケーションに SSO を構成します。

1. Azure Portal の **vxMaintain** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    !["シングル サインオン" コマンド][4]

1. SSO を有効にするには、**[シングル サインオン モード]** ボックスの一覧から **[SAML ベースのサインオン]** を選択します。
 
    !["SAML ベースのサインオン" コマンド](./media/vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

1. **[vxMaintain のドメインと URL]** で、次の手順を実行します。

    ![[vxMaintain のドメインと URL] セクション](./media/vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. **[識別子]** ボックスに、`https://<company name>.verisae.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true` の形式で URL を入力します。

    > [!NOTE] 
    > 上記の値は、実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 値を取得するには、[vxMaintain サポート チーム](https://www.hubspot.com/company/contact)に問い合わせてください。
 
1. **[SAML 署名証明書]** で、**[メタデータ XML]** を選択し、コンピューターにメタデータ ファイルを保存します。

    !["SAML 署名証明書" セクション](./media/vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

1. **[保存]** を選択します。

    ![[保存] ボタン](./media/vxmaintain-tutorial/tutorial_general_400.png)

1. **vxMaintain** の SSO を構成するには、ダウンロードした**メタデータ XML** ファイルを [vxMaintain サポート チーム](https://www.hubspot.com/company/contact)に送信します。

> [!TIP]
> アプリのセットアップ中、上記手順の簡易版を [Azure Portal](https://portal.azure.com) でご覧いただけます。 **[Active Directory]** > **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、**[構成]** セクションから組み込みドキュメントにアクセスします。 
>
>組み込みドキュメント機能の詳細については、「[エンタープライズ アプリのシングル サインオンの管理](https://go.microsoft.com/fwlink/?linkid=845985)」を参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、Azure Portal で次の手順に従って Britta Simon というテスト ユーザーを作成します。

![Azure AD テスト ユーザー][100]

1. **Azure Portal** の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/vxmaintain-tutorial/create_aaduser_01.png) 

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** > **[すべてのユーザー]** の順に移動します。
    
    !["すべてのユーザー" リンク](./media/vxmaintain-tutorial/create_aaduser_02.png)  
    **[すべてのユーザー]** ダイアログ ボックスが表示されます。 

1. **[ユーザー]** ダイアログ ボックスを開くには、**[追加]** を選択します。
 
    ![[追加] ボタン](./media/vxmaintain-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/vxmaintain-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、テスト ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに生成された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-vxmaintain-test-user"></a>vxMaintain テスト ユーザーの作成

このセクションでは、vxMaintain で Britta Simon というテスト ユーザーを作成します。 vxMaintain プラットフォームにユーザーを追加するには、[vxMaintain サポート チーム](https://www.hubspot.com/company/contact)と連携してください。 SSO を使用する前に、ユーザーを作成してアクティブ化します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon というテスト ユーザーに vxMaintain へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。 そのためには、次の手順を実行します。

![[表示名] の一覧に表示されるテスト ユーザー][200] 

1. Azure Portal の **[アプリケーション]** ビューから **[ディレクトリ]** ビューに移動し、**[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に移動します。

    !["すべてのアプリケーション" リンク][201] 

1. **[アプリケーション]** の一覧で **[vxMaintain]** を選択します。

    ![vxMaintain リンク](./media/vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

1. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク][202] 

1. **[追加]** を選択し、**[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク][203]

1. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** 一覧から、**[Britta Simon]** を選択し、**[選択]** ボタンを選択します。

1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。
    
### <a name="test-your-azure-ad-single-sign-on"></a>Azure AD シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD SSO の構成をテストします。

アクセス パネルで **[vxMaintain]** タイルを選択すると、vxMaintain アプリケーションに自動的にサインインします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/vxmaintain-tutorial/tutorial_general_203.png

