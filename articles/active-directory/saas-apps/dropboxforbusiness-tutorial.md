---
title: 'チュートリアル: Azure Active Directory と Dropbox for Business の統合 | Microsoft Docs'
description: Azure Active Directory と Dropbox for Business の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: d46f2aac5fb16b10f33cccabdcd76d60f0d6dfb9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438060"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>チュートリアル: Azure Active Directory と Dropbox for Business の統合

このチュートリアルでは、Dropbox for Business と Azure Active Directory (Azure AD) を統合する方法について説明します。

Dropbox for Business と Azure AD の統合には、次の利点があります。

- Dropbox for Business にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Dropbox for Business にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Dropbox for Business の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Dropbox for Business でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Dropbox for Business を追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-dropbox-for-business-from-the-gallery"></a>ギャラリーから Dropbox for Business を追加
Azure AD への Dropbox for Business の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Dropbox for Business を追加する必要があります。

**ギャラリーから Dropbox for Business を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Dropbox for Business**」と入力し、結果パネルで **[Dropbox for Business]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Dropbox for Business](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Dropbox for Business で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Dropbox for Business ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Dropbox for Business の関連ユーザーの間で、リンク関係が確立されている必要があります。

Dropbox for Business で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Dropbox for Business で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Dropbox for Business のテスト ユーザーの作成](#create-a-dropbox-for-business-test-user)** - Dropbox for Business で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Dropbox for Business アプリケーションでシングル サインオンを構成します。

**Dropbox for Business で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Dropbox for Business** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

1. **[Dropbox for Business のドメインと URL]** セクションで、次の手順を実行します。

    ![[Dropbox for Business のドメインと URL] のシングル サインオン情報](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. **[サインオン URL]** ボックスに、`https://www.dropbox.com/sso/<id>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、値 `Dropbox` を入力します。

    > [!NOTE] 
    > 上記のサインオン URL の値は、実際の値ではありません。 実際のサインオン URL に値を置き換えます。実際の値については後で説明します。 この値を取得するには、[Dropbox for Business クライアント サポート チーム](https://www.dropbox.com/business/contact)にお問い合わせください。 
 

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/dropboxforbusiness-tutorial/tutorial_general_400.png)

1. **[Dropbox for Business Configuration]\(Dropbox for Business 構成\)** セクションで、**[Configure Dropbox for Business]\(Dropbox for Business の構成\)** をクリックし、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Dropbox for Business の構成](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

1. **Dropbox for Business** 側のシングル サインオンを構成するには、Dropbox for Business テナントに移動します。

    a. Dropbox for Business テナントにサインオンします。 
   
    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/ic769509.png "Configure single sign-on")
   
    b. 左側のナビゲーション ウィンドウで、**[管理コンソール]** をクリックします。 
   
    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/ic769510.png "Configure single sign-on")
   
    c. **[管理コンソール]** の左側のナビゲーション ウィンドウで、**[認証]** をクリックします。 
   
    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/ic769511.png "Configure single sign-on")
   
    d. **[シングル サインオン]** セクションで **[シングル サインオンを有効にする]** を選択し、**[詳細]** をクリックしてこのセクションを展開します。  
   
    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/ic769512.png "Configure single sign-on")
   
    e. **[Users can sign in by entering their email address or they can go directly to]\(ユーザーは電子メール アドレスを入力してサインインすることも、次の URL に直接移動することもできます\)** の横の URL をコピーします。次に、Azure Portal の **[Dropbox for Business のドメインと URL]** セクションの **[サインオン URL]** ボックスにそれを貼り付けます。 
    
    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/ic769513.png)
    
1. **[認証]** ページの **[シングル サインオン]** セクションで、次の手順を実行します。 
   
    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/IC769516.png "Configure single sign-on")
   
    a. **[必須項目です]** をクリックします。
   
    b. **[サインイン URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[証明書の選択]** をクリックし、**Base-64 でエンコードされた証明書ファイル**を参照します。

    d. **[変更の保存]** をクリックして、DropBox for Business テナントでの構成を完了します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/dropboxforbusiness-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/dropboxforbusiness-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/dropboxforbusiness-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-dropbox-for-business-test-user"></a>Dropbox for Business のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Dropbox for Business に作成します。 Dropbox for Business では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Dropbox for Business に存在しない場合は、Dropbox for Business にアクセスしようとしたときに新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Dropbox for Business クライアント サポート チーム](https://www.dropbox.com/business/contact)にお問い合わせください。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Dropbox for Business へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Dropbox for Business に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Dropbox for Business]** を選択します。

    ![アプリケーション一覧の Dropbox for Business リンク](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Dropbox for Business のタイルをクリックすると、Dropbox for Business アプリケーションのログイン ページが開きます。
 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/dropboxforbusiness-tutorial/tutorial_general_203.png

