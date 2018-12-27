---
title: 'チュートリアル: Azure Active Directory と FileCloud の統合 | Microsoft Docs'
description: Azure Active Directory と FileCloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 2263e583-3eb2-4a06-982d-33f5f54858f4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: jeedes
ms.openlocfilehash: 86e02fe51d4f461036d378f515746bddeb2d02a3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441427"
---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>チュートリアル: Azure Active Directory と FileCloud の統合

このチュートリアルでは、FileCloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

FileCloud と Azure AD の統合には、次の利点があります。

- FileCloud にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に FileCloud にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

FileCloud と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- FileCloud でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの FileCloud の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-filecloud-from-the-gallery"></a>ギャラリーからの FileCloud の追加
Azure AD への FileCloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に FileCloud を追加する必要があります。

**ギャラリーから FileCloud を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**FileCloud**」と入力し、結果ウィンドウで **[FileCloud]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の FileCloud](./media/filecloud-tutorial/tutorial_filecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、FileCloud で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する FileCloud ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと FileCloud の関連ユーザーの間で、リンク関係が確立されている必要があります。

FileCloud で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

FileCloud で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[FileCloud のテスト ユーザーの作成](#create-a-filecloud-test-user)** - FileCloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、FileCloud アプリケーションでシングル サインオンを構成します。

**FileCloud で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **FileCloud** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオンの構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/filecloud-tutorial/tutorial_filecloud_samlbase.png)

1. **[FileCloud のドメインと URL]** セクションで、次の手順を実行します。

    ![[FileCloud のドメインと URL] のシングル サインオン情報](./media/filecloud-tutorial/tutorial_filecloud_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.filecloudonline.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.filecloudonline.com/simplesaml/module.php/saml/sp/metadata.php/default-sp` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[FileCloud クライアント サポート チーム](mailto:support@codelathe.com)に連絡してください。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/filecloud-tutorial/tutorial_filecloud_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/filecloud-tutorial/tutorial_general_400.png)

1. **[FileCloud 構成]** セクションで、**[FileCloud の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から **SAML エンティティ ID** をコピーします。

    ![FileCloud の構成](./media/filecloud-tutorial/tutorial_filecloud_configure.png) 

1. 別の Web ブラウザーのウィンドウで、管理者として FileCloud テナントにサインオンします。

1. 左側のナビゲーション ウィンドウで、 **[Settings (設定)]** をクリックします。 
   
    ![アプリ側でのシングル サインオンの構成](./media/filecloud-tutorial/tutorial_filecloud_000.png)

1. [Settings (設定)] セクションの **[SSO]** タブをクリックします。 
   
    ![アプリ側でのシングル サインオンの構成](./media/filecloud-tutorial/tutorial_filecloud_001.png)

1. **[Single Sign On (SSO) Settings (シングル サインオン (SSO) 設定)]** パネルで、**[Default SSO Type (既定の SSO タイプ)]** として **[SAML]** を選択します。
   
    ![アプリ側でのシングル サインオンの構成](./media/filecloud-tutorial/tutorial_filecloud_002.png)

1. **[IdP End Point URL]\(IDP エンドポイント URL\)** テキストボックスに、Azure ポータルからコピーした **SAML エンティティ ID** の値を貼り付けます。

    ![アプリ側でのシングル サインオンの構成](./media/filecloud-tutorial/tutorial_filecloud_003.png)

1. ダウンロードしたメタデータ ファイルをメモ帳で開き、その内容をクリップボードにコピーし、**[SAML Settings (SAML 設定)]** パネルの **[IdP Meta Data (IdP メタ データ)]** ボックスに貼りつけます。

    ![アプリ側でのシングル サインオンの構成](./media/filecloud-tutorial/tutorial_filecloud_004.png)

1. **[保存]** ボタンをクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/filecloud-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/filecloud-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/filecloud-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/filecloud-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-filecloud-test-user"></a>FileCloud テスト ユーザーの作成

このセクションの目的は、FileCloud で Britta Simon というユーザーを作成することです。 FileCloud では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない FileCloud ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[FileCloud クライアント サポート チーム](mailto:support@codelathe.com)に問い合わせてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に FileCloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**FileCloud に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[FileCloud]** を選択します。

    ![アプリケーションの一覧の FileCloud のリンク](./media/filecloud-tutorial/tutorial_filecloud_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [FileCloud] タイルをクリックすると、自動的に FileCloud アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/filecloud-tutorial/tutorial_general_01.png
[2]: ./media/filecloud-tutorial/tutorial_general_02.png
[3]: ./media/filecloud-tutorial/tutorial_general_03.png
[4]: ./media/filecloud-tutorial/tutorial_general_04.png

[100]: ./media/filecloud-tutorial/tutorial_general_100.png

[200]: ./media/filecloud-tutorial/tutorial_general_200.png
[201]: ./media/filecloud-tutorial/tutorial_general_201.png
[202]: ./media/filecloud-tutorial/tutorial_general_202.png
[203]: ./media/filecloud-tutorial/tutorial_general_203.png

