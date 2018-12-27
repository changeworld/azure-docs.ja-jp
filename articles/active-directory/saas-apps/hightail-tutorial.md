---
title: 'チュートリアル: Azure Active Directory と Hightail の統合 | Microsoft Docs'
description: Azure Active Directory と Hightail の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: jeedes
ms.openlocfilehash: 1151044d5c1002c808ae1214086aff5fad84a55e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431336"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>チュートリアル: Azure Active Directory と Hightail の統合

このチュートリアルでは、Hightail と Azure Active Directory (Azure AD) を統合する方法について説明します。

Hightail と Azure AD の統合には、次の利点があります。

- Hightail にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Hightail にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Hightail と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Hightail でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Hightail を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-hightail-from-the-gallery"></a>ギャラリーから Hightail を追加する
Azure AD への Hightail の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Hightail を追加する必要があります。

**ギャラリーから Hightail を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **Hightail**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/hightail-tutorial/tutorial_hightail_search.png)

1. 結果ウィンドウで **Hightail** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Hightail で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Hightail ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Hightail の関連ユーザーの間で、リンク関係が確立されている必要があります。

Hightail で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Hightail で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Hightail テスト ユーザーの作成](#creating-a-hightail-test-user)** - Hightail で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Hightail アプリケーションでシングル サインオンを構成します。

**Hightail で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Hightail** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/hightail-tutorial/tutorial_hightail_samlbase.png)

1. **[Hightail のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![Configure single sign-on](./media/hightail-tutorial/tutorial_hightail_url.png)

    **[応答 URL]** ボックスに、URL を「`https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`」と入力します。

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL に応答 URL 値を置き換えます。実際の値については後で説明します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![Configure single sign-on](./media/hightail-tutorial/tutorial_hightail_url1.png)

    **[サインオン URL]** ボックスに、URL として「`https://www.hightail.com/loginSSO`」と入力します。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/hightail-tutorial/tutorial_hightail_certificate.png) 

1. Hightail アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 これらの属性の値は、アプリケーションの **[属性]** タブから管理できます。 次のスクリーンショットはその例です。 

    ![Configure single sign-on](./media/hightail-tutorial/tutorial_hightail_attribute.png) 

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |
    | FirstName | User.givenname |
    | LastName | User.surname |
    | 電子メール | User.mail |    
    | UserIdentity | User.mail |
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/hightail-tutorial/tutorial_officespace_04.png)

    ![Configure single sign-on](./media/hightail-tutorial/tutorial_officespace_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** は空白のままにします。

    e. **[OK]** をクリックします。

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/hightail-tutorial/tutorial_general_400.png)

1. **[Hightail 構成]** セクションで、**[Hightail の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/hightail-tutorial/tutorial_hightail_configure.png)

    >[!NOTE]
    >Hightail アプリでシングル サインオンを構成する前に、電子メール ドメインを Hightail チームのホワイト リストに登録し、そのドメインを使用するすべてのユーザーがシングル サインオン機能を利用できるようにします。

1. 別のブラウザー ウィンドウで、**Hightail** 管理ポータルを開きます。

1. ページの右上隅の**ユーザー アイコン**をクリックします。 

    ![Configure single sign-on](./media/hightail-tutorial/configure1.png)

1. **[管理コンソールを表示]** タブをクリックします。

    ![Configure single sign-on](./media/hightail-tutorial/configure2.png)

1. 上部のメニューで、**[SAML]** タブをクリックし、次の手順を実行します。

    ![Configure single sign-on](./media/hightail-tutorial/configure3.png)

    a. **[ログイン URL]** テキストボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    b. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[SAML Certificate]\(SAML 証明書\)** ボックスに貼り付けます。

    c. **[コピー]** をクリックして、インスタンスの SAML コンシューマー URL をコピーし、Azure portal の **[Hightail ドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。

    d. **[構成の保存]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/hightail-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/hightail-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/hightail-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/hightail-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-hightail-test-user"></a>Hightail テスト ユーザーの作成

このセクションの目的は、Hightail で Britta Simon というユーザーを作成することです。 

このセクションでは、ユーザー側で必要な操作はありません。 Hightail は、カスタムの要求に基づく時間どおりのユーザー プロビジョニングをサポートしています。 前述のセクション「 **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** 」に従ってカスタムの要求を構成した場合、まだ存在していないアプリケーションにユーザーが自動的に作成されます。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[Hightail サポート チーム](mailto:support@hightail.com)に問い合わせてください。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Hightail へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Hightail に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Hightail]** を選択します。

    ![Configure single sign-on](./media/hightail-tutorial/tutorial_hightail_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで Hightail のタイルをクリックすると、自動的に Hightail アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hightail-tutorial/tutorial_general_01.png
[2]: ./media/hightail-tutorial/tutorial_general_02.png
[3]: ./media/hightail-tutorial/tutorial_general_03.png
[4]: ./media/hightail-tutorial/tutorial_general_04.png

[100]: ./media/hightail-tutorial/tutorial_general_100.png

[200]: ./media/hightail-tutorial/tutorial_general_200.png
[201]: ./media/hightail-tutorial/tutorial_general_201.png
[202]: ./media/hightail-tutorial/tutorial_general_202.png
[203]: ./media/hightail-tutorial/tutorial_general_203.png

