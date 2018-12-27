---
title: 'チュートリアル: Azure Active Directory と ADP の統合 | Microsoft Docs'
description: Azure Active Directory と ADP の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: jeedes
ms.openlocfilehash: 75b84c2856373126ceba0fc536e41d270f4d2d05
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048781"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>チュートリアル: Azure Active Directory と ADP の統合

このチュートリアルでは、ADP と Azure Active Directory (Azure AD) を統合する方法について説明します。

ADP と Azure AD の統合には、次の利点があります。

- ADP にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで ADP に自動的にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ADP と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ADP が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ADP の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-adp-from-the-gallery"></a>ギャラリーからの ADP の追加
Azure AD への ADP の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ADP を追加する必要があります。

**ギャラリーから ADP を追加するには、次の手順を実行します。**

1.  Microsoft Azure の ID プロバイダー環境に管理者としてログオンします。

2. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

3. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
4. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

5. 検索ボックスに「**ADP**」と入力し、結果パネルで **[ADP]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の ADP](./media/adpfederatedsso-tutorial/tutorial_adp_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、ADP で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する ADP のユーザーを認識している必要があります。 言い換えると、Azure AD ユーザーと ADP の関連ユーザーの間で、リンク関係が確立されている必要があります。

ADP で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

ADP で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ADP のテスト ユーザーの作成](#create-an-adp-test-user)** - ADP で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、ADP アプリケーションでシングル サインオンを構成します。

**ADP で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **ADP** アプリケーション統合ページで、**[プロパティ]** タブをクリックし、次の手順を実行します。 

    ![シングル サインオンのプロパティ](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. **[ユーザーのサインインが有効になっていますか?]** フィールドの値を **[はい]** に設定します。

    b. **[ユーザーのアクセス URL]** をコピーします。これを **[サインオン URL の構成]** セクションに貼り付ける必要がありますが、この操作については、このチュートリアルの後半で説明します。

    c. **[ユーザーの割り当てが必要]** フィールドの値を **[はい]** に設定します。

    d. **[ユーザーに表示しますか?]** フィールドの値を **[いいえ]** に設定します。

2. **ADP** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

3. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/adpfederatedsso-tutorial/tutorial_adp_samlbase.png)

4. **[ADP Domain and URLs]\(ADP のドメインと URL\)** セクションで、次の手順を実行します。

    ![[ADP Domain and URLs]\(ADP のドメインと URL\) のシングル サインオン情報](./media/adpfederatedsso-tutorial/tutorial_adp_url.png)

    **[識別子]** ボックスに次の URL を入力します。`https://fed.adp.com` 
    
5. ADP アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 クレームの名前は常に **"PersonImmutableID"** であり、その値は **employeeid** にマップされています。 

    ここで、Azure AD から ADP へのユーザー マッピングは **employeeid** で完了しますが、これをアプリケーションの設定に基づいて別の値にマップできます。 そのため、まず [ADP サポート チーム](https://www.adp.com/contact-us/overview.aspx)と協力してユーザーの正しい識別子を使用し、その値を **"PersonImmutableID"** クレームとマップしてください。

    ![Configure single sign-on](./media/adpfederatedsso-tutorial/tutorial_adp_attribute.png)

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

    > [!NOTE] 
    > SAML アサーションを構成するには、その前に [ADP サポート チーム](https://www.adp.com/contact-us/overview.aspx)に連絡し、テナントの一意識別子属性の値を要求する必要があります。 この値は、アプリケーションのカスタム要求を構成するのに必要です。 

7. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/adpfederatedsso-tutorial/tutorial_adp_certificate.png) 

8. **ADP** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [ADP の Web サイト](https://adpfedsso.adp.com/public/login/index.fcc)にアップロードする必要があります。

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

4. 検索ボックスに「**ADP**」と入力し、結果パネルで **[ADP]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の ADP](./media/adpfederatedsso-tutorial/tutorial_adp_addservicegallery.png)

5. Azure Portal の **ADP** アプリケーション統合ページで、**[プロパティ]** タブをクリックし、次の手順を実行します。  

    ![シングル サインオンのリンクされたプロパティ](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  **[ユーザーのサインインが有効になっていますか?]** フィールドの値を **[はい]** に設定します。

    b.  **[ユーザーの割り当てが必要]** フィールドの値を **[はい]** に設定します。

    c.  **[ユーザーに表示しますか?]** フィールドの値を **[はい]** に設定します。

6. **ADP** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

7. **[シングル サインオン]** ダイアログで、**[モード]** として **[リンクされたサインオン]** を選択します。 アプリケーションを **ADP** にリンクさせます。

    ![リンクされたシングル サインオン](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. **[サインオン URL の構成]** セクションに移動し、次の手順を実行します。

    ![シングル サインオンのプロパティ](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)
                                                              
    a. 上記の **[プロパティ]** タブ (メインの ADP アプリケーション) からコピーした、**ユーザーのアクセス URL** を貼り付けます。
                                                             
    b. 次の 5 つのアプリは、異なる**リレー状態 URL** をサポートしています。 特定のアプリケーションの適切な**リレー状態 URL** の値を、**ユーザーのアクセス URL** に手動で追加する必要があります。
    
    * **ADP Workforce Now**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **保存** します。

10. ADP の担当者から確認の電子メールを受信したら、1 人または 2 人のユーザーでテストを開始します。

    a. 数名のユーザーを ADP サービス アプリに割り当てて、フェデレーション アクセスをテストします。

    b. ユーザーがギャラリーの ADP サービス アプリにアクセスして ADP サービスにアクセスできると、テストは成功です。
 
11. テストの性行を確認したら、個々のユーザーまたはユーザー グループにフェデレーション ADP サービスを割り当てます。これについてはこのチュートリアルで後ほど説明しますので、従業員にロールアウトしてください。 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/adpfederatedsso-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/adpfederatedsso-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/adpfederatedsso-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/adpfederatedsso-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-adp-test-user"></a>ADP テスト ユーザーを作成する

このセクションの目的は、ADP で Britta Simon というユーザーを作成することです。 [ADP サポート チーム](https://www.adp.com/contact-us/overview.aspx)と協力して、ADP アカウントにユーザーを追加します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ADP へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**ADP に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[ADP]** を選択します。

    ![アプリケーションの一覧の [ADP] リンク](./media/adpfederatedsso-tutorial/tutorial_adp_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで ADP のタイルをクリックすると、ADP アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/adpfederatedsso-tutorial/tutorial_general_203.png

