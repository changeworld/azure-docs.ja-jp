---
title: 'チュートリアル: Azure Active Directory と LinkedIn Learning の統合 | Microsoft Docs'
description: Azure Active Directory と LinkedIn Learning の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 02e7d9d26b389e82365f3447cceb5566244236f1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440910"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>チュートリアル: Azure Active Directory と LinkedIn Learning の統合

このチュートリアルでは、LinkedIn Learning と Azure Active Directory (Azure AD) を統合する方法について説明します。

LinkedIn Learning と Azure AD の統合には、次の利点があります。

- LinkedIn Learning にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで LinkedIn Learning に自動的にサインオン (シングル サインオン) する機能を有効にすることができます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

LinkedIn Learning と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- LinkedIn Learning でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの LinkedIn Learning の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-linkedin-learning-from-the-gallery"></a>ギャラリーからの LinkedIn Learning の追加
Azure AD への LinkedIn Learning の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LinkedIn Learning を追加する必要があります。

**ギャラリーから LinkedIn Learning を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[追加]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**LinkedIn Learning**」と入力します。 結果パネルで、**[LinkedIn Learning]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、LinkedIn Learning で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する LinkedIn Learning ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと LinkedIn Learning の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、LinkedIn Learning の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

LinkedIn Learning で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[LinkedIn Learning のテスト ユーザーの作成](#creating-a-linkedin-learning-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、LinkedIn Learning アプリケーションでシングル サインオンを構成します。

**LinkedIn Learning で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **LinkedIn Learning** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial-linkedin_01.png)

1. 別の Web ブラウザーのウィンドウで、管理者として LinkedIn Learning テナントにサインオンします。

1. **[アカウント センター]** で、**[設定]** の下の **[グローバル設定]** をクリックします。 また、ドロップダウン リストから **[Learning - Default (ラーニング - 既定)]** を選択します。

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

1. **[OR Click Here to load and copy individual fields from the form (または、ここをクリックしてフォームから個々のフィールドを読み込み、コピーする)]** をクリックし、**[Entity Id (エンティティ ID)]** と **[Assertion Consumer Access (ACS) Url (Assertion Consumer Access (ACS) URL)]** をコピーします。

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

1. Azure Portal の **[LinkedIn Learning のドメインと URL]** で、SSO を **IdP 開始**モードで構成する場合は、次の手順を実行します

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. **[識別子]** テキストボックスに、LinkedIn ポータルからコピーした**エンティティ ID** を入力します 

    b. **[応答 URL]** テキストボックスに、LinkedIn ポータルからコピーした **Assertion Consumer Access (ACS) URL** を入力します

1. SSO を **SP 開始**で構成する場合は、構成セクションの [詳細な URL 設定の表示] オプションをクリックし、次のパターンでサインオン URL を構成します。

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
1. LinkedIn Learning アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングをSAML トークン属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 **[ユーザー識別子]** の既定値は **user.userprincipalname** ですが、LinkedIn Learning はこれがユーザーの電子メール アドレスにマップされることを想定します。 そのため、一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。 

    ![Configure single sign-on](./media/linkedinlearning-tutorial/updateusermail.png)
    
1. **[ユーザー属性]** セクションで、**[その他のすべてのユーザー属性を表示および編集する]** をクリックし、属性を設定します。 ユーザーは **email**、**department**、**firstname**、**lastname** という名前の 4 つの要求を追加する必要があり、値はそれぞれ **user.mail**、**user.department**、**user.givenname**、**user.surname** にマップします。

    | 属性名 | 属性値 |
    | --- | --- |
    | email| User.mail |    
    | department| user.department |
    | firstname| User.givenname |
    | lastname| User.surname |
    
    ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/userattribute.png)
    
    a. **[属性の追加]** をクリックして [属性の追加] ダイアログを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/tutorial_attribute_04.png)

    ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

1. **name** 属性で以下の手順を実行します。

    a. 属性をクリックして、**[属性の編集]** ウィンドウを開きます。

    ![Configure single sign-on](./media/linkedinlearning-tutorial/url_update.png)

    b. **namespace** から URL の値を削除します。
    
    c. **[OK]** をクリックして設定を保存します。

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png)

1. **[Save]** をクリックします。

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_general_400.png)

1. **[LinkedIn Admin Settings (LinkedIn 管理者設定)]** セクションに移動します。 [Upload XML file]\(XML ファイルのアップロード\) オプションをクリックして、Azure Portal からダウンロードした XML ファイルをアップロードします。

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

1. **[ON (オン)]** をクリックして SSO を有効にします。 SSO の状態が **[Not Connected (未接続)]** から **[Connected (接続済み)]** に変更されます

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="creating-a-linkedin-learning-test-user"></a>LinkedIn Learning テスト ユーザーの作成

LinkedIn Learning アプリケーションでは、ジャストインタイムのユーザーのプロビジョニングがサポートされ、認証後に、ユーザーが自動的に、アプリケーションに作成されます。 LinkedIn Learning ポータルの管理者設定ページで、スイッチ **[Automatically Assign licenses (ライセンスを自動的に割り当てる)]** を切り替えて、ジャストインタイム プロビジョニングを有効にします。これにより、ユーザーにライセンスも割り当てられます。

   ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LinkedIn Learning へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を LinkedIn Learning に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[LinkedIn Learning]** を選択します。

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png)

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで LinkedIn Learning タイルをクリックすると、Azure サインオン ページが表示され、サインオンに成功すると LinkedIn Learning アプリケーションに入ります。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/linkedinlearning-tutorial/tutorial_general_203.png
