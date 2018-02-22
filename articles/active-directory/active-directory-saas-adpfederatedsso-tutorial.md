---
title: "チュートリアル: Azure Active Directory と ADP Federated SSO の統合 | Microsoft Docs"
description: "Azure Active Directory と ADP Federated SSO の間のシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: jeedes
ms.openlocfilehash: 76bbee514eac36aec8eaf84e8a20f85a27559fc7
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp-federated-sso"></a>チュートリアル: Azure Active Directory と ADP Federated SSO の統合

このチュートリアルでは、ADP Federated SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。

ADP Federated SSO と Azure AD の統合には、次の利点があります。

- ADP Federated SSO にアクセスするユーザーを Azure AD 上でコントロールできます。
- ユーザーが自分の Azure AD アカウントで自動的に ADP Federated SSO にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と ADP Federated SSO の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ADP Federated SSO が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ADP Federated SSO の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-adp-federated-sso-from-the-gallery"></a>ギャラリーからの ADP Federated SSO の追加
Azure AD への ADP Federated SSO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ADP Federated SSO を追加する必要があります。

**ギャラリーから ADP Federated SSO を追加するには、次の手順に従います。**

1.  Microsoft Azure の ID プロバイダー環境に管理者としてログオンします。

2. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

3. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
4. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

5. 検索ボックスに「**ADP Federated SSO**」と入力し、結果パネルで **[ADP Federated SSO]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の ADP Federated SSO](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーを基に、ADP Federated SSO で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ADP Federated SSO ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ADP Federated SSO の関連ユーザーの間で、リンク関係が確立されている必要があります。

ADP Federated SSO で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

ADP Federated SSO で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ADP Federated SSO のテスト ユーザーの作成](#create-an-adp-federated-sso-test-user)** - ADP Federated SSO で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、ADP Federated SSO アプリケーションでシングル サインオンを構成します。

**ADP Federated SSO で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **ADP Federated SSO** アプリケーション統合ページで、**[プロパティ]** タブをクリックし、次の手順を実行します。 

    ![シングル サインオンのプロパティ](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_prop.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[ユーザーのサインインが有効になっていますか?]** フィールドの値を **[はい]** に設定します。

    b. **[ユーザーのアクセス URL]** をコピーします。これを **[サインオン URL の構成]** セクションに貼り付ける必要がありますが、この操作については、このチュートリアルの後半で説明します。

    c. **[ユーザーの割り当てが必要]** フィールドの値を **[はい]** に設定します。

    d. **[ユーザーに表示しますか?]** フィールドの値を **[いいえ]** に設定します。

2. **ADP Federated SSO** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

3. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_samlbase.png)

4. **[ADP Federated SSO Domain and URLs]\(ADP Federated SSO のドメインと URL\)** セクションで、次の手順を実行します。

    ![[ADP Federated SSO Domain and URLs]\(ADP Federated SSO のドメインと URL\) のシングル サインオン情報](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_url.png)

    **[識別子]** ボックスに次の URL を入力します。`https://fed.adp.com/` 
    
5. ADP Federated SSO アプリケーションは特定の形式の SAML アサーションを予測しているため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットはその例です。 クレームの名前は常に **"PersonImmutableID"** であり、その値は **employeeid** にマップされています。 

    ここで、Azure AD から ADP Federated SSO へのユーザー マッピングは **employeeid** で完了しますが、これをアプリケーションの設定に基づいて別の値にマップできます。 そのため、まず [ADP サポート チーム](https://www.adp.com/contact-us/overview.aspx)と協力してユーザーの正しい識別子を使用し、その値を **"PersonImmutableID"** クレームとマップしてください。

    ![[Configure Single Sign-On]](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_attribute.png)

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[Configure Single Sign-On]](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![[Configure Single Sign-On]](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]**をクリックします。

    > [!NOTE] 
    > SAML アサーションを構成するには、その前に [ADP サポート チーム](https://www.adp.com/contact-us/overview.aspx)に連絡し、テナントの一意識別子属性の値を要求する必要があります。 この値は、アプリケーションのカスタム要求を構成するのに必要です。 

7. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_certificate.png) 

8. **ADP Federated SSO** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [ADP Federated SSO の Web サイト](https://adpfedsso.adp.com/public/login/index.fcc)にアップロードする必要があります。

> [!NOTE]  
> このプロセスは数日かかることがあります。 

### <a name="configure-your-adp-services-for-federated-access"></a>フェデレーション アクセスのために ADP サービスを構成する

>[!Important]
> ADP サービスへのフェデレーション アクセスが必要な従業員を ADP サービス アプリに割り当て、その後、ユーザーを特定の ADP サービスに再割り当てする必要があります。
ADP 担当者から送信される確認の電子メールを受信したら、ADP サービスを構成し、ユーザーの割り当てまたは管理によって、特定の ADP サービスへのユーザー アクセスを制御します。

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**ADP Federated SSO**」と入力し、結果パネルで **[ADP Federated SSO]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の ADP Federated SSO](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addservicegallery.png)

5. Azure Portal の **ADP Federated SSO** アプリケーション統合ページで、**[プロパティ]** タブをクリックし、次の手順を実行します。  

    ![シングル サインオンのリンクされたプロパティ](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedproperties.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。  **[ユーザーのサインインが有効になっていますか?]** フィールドの値を **[はい]** に設定します。

    b.  **[ユーザーの割り当てが必要]** フィールドの値を **[はい]** に設定します。

    c.  **[ユーザーに表示しますか?]** フィールドの値を **[はい]** に設定します。

6. **ADP Federated SSO** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

7. **[シングル サインオン]** ダイアログで、**[モード]** として **[リンクされたサインオン]** を選択し、アプリケーションを **ADP Federated SSO** にリンクします。
<!---Loc Comment:Sentence "to link your application to ADP Federated SSO." should be concatenated with the previous sentence--->

    ![Single sign-on linked](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linked.png)

8. **[サインオン URL の構成]** セクションに移動し、次の手順を実行します。

    ![シングル サインオンのプロパティ](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedsignon.png)
                                                              
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 上記の **[プロパティ]** タブ (メインの ADP Federated SSO アプリケーション) からコピーした、**ユーザーのアクセス URL** を貼り付けます。
                                                             
    b. 次の 5 つのアプリは、異なる**リレー状態 URL** をサポートしています。 特定のアプリケーションの適切な**リレー状態 URL** の値を、**ユーザーのアクセス URL** に手動で追加する必要があります。
    
    * **ADP Workforce Now**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **保存** します。

10. ADP の担当者から確認の電子メールを受信したら、1 人または 2 人のユーザーでテストを開始します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 数名のユーザーを ADP サービス アプリに割り当てて、フェデレーション アクセスをテストします。

    b. ユーザーがギャラリーの ADP サービス アプリにアクセスして ADP サービスにアクセスできると、テストは成功です。
 
11. テストの性行を確認したら、個々のユーザーまたはユーザー グループにフェデレーション ADP サービスを割り当てます。これについてはこのチュートリアルで後ほど説明しますので、従業員にロールアウトしてください。 

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-adp-federated-sso-test-user"></a>ADP Federated SSO のテスト ユーザーの作成

このセクションの目的は、ADP Federated SSO で Britta Simon というユーザーを作成することです。 [ADP サポート チーム](https://www.adp.com/contact-us/overview.aspx)と協力して、ADP Federated SSO アカウントにユーザーを追加します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ADP Federated SSO へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**ADP Federated SSO に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[ADP Federated SSO]** を選択します。

    ![アプリケーションの一覧の ADP Federated SSO のリンク](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで ADP Federated SSO のタイルをクリックすると、自動的に ADP Federated SSO アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_203.png

