---
title: 'チュートリアル: Azure Active Directory と Amazon Web Services (AWS) の統合 | Microsoft Docs'
description: Azure Active Directory と Amazon Web Services (AWS) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 797be143284566efcefce5ed6c7ded822d5aa97f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438899"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>チュートリアル: Azure Active Directory と Amazon Web Services (AWS) の統合

このチュートリアルでは、Amazon Web Services (AWS) と Azure Active Directory (Azure AD) を統合する方法について説明します。

Amazon Web Services (AWS) と Azure AD の統合には、次の利点があります。

- Amazon Web Services (AWS) にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Amazon Web Services (AWS) にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Amazon Web Services (AWS) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Amazon Web Services (AWS) でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Amazon Web Services (AWS) の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>ギャラリーからの Amazon Web Services (AWS) の追加
Azure AD への Amazon Web Services (AWS) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amazon Web Services (AWS) を追加する必要があります。

**ギャラリーから Amazon Web Services (AWS) を追加するには、次の手順を実行します:** 

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Amazon Web Services (AWS)**」と入力して結果パネルから **[Amazon Web Services (AWS)]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果の一覧の Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Amazon Web Services (AWS) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Amazon Web Services (AWS) ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと Amazon Web Services (AWS) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Amazon Web Services (AWS) で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Amazon Web Services (AWS) との Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Amazon Web Services (AWS) テスト ユーザーの作成](#create-an-amazon-web-services-aws-test-user)** - Amazon Web Services (AWS) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、Amazon Web Services (AWS) アプリケーションへのシングル サインオンを構成します。

**Amazon Web Services (AWS) との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Amazon Web Services (AWS)** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

1. アプリは Azure と事前に統合済みであるため、**[Amazon Web Services (AWS) のドメインと URL]** セクションで特に手順を実施する必要はありません。

    ![[Amazon Web Services (AWS) のドメインと URL] のシングル サインオン情報](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

1. Amazon Web Services (AWS) アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。

    ![シングル サインオンの構成の属性](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png) 

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名  | 属性値 | 名前空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >AWS コンソールからすべてのロールをフェッチするには、Azure AD でユーザー プロビジョニングを構成する必要があります。 次のプロビジョニングの手順をご覧ください。

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![シングル サインオンの構成の追加](./media/amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![シングル サインオンの構成の属性の追加](./media/amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** ボックスに、その行に表示される名前空間の値を入力します。
    
    d. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/amazon-web-service-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザーのウィンドウで、管理者として Amazon Web Services (AWS) 企業サイトにサインオンします。

1. **[AWS Home]** をクリックします。
   
    ![シングル サインオン ホームの構成][11]

1. **[Identity and Access Management]** をクリックします。 
   
    ![シングル サインオン ID の構成][12]

1. **[Identity Providers]**、**[Create Provider]** の順にクリックします。 
   
    ![シングル サインオン プロバイダーの構成][13]

1. **[Configure Provider]** ダイアログ ページで、次の手順を実行します。 
   
    ![シングル サインオンの構成ダイアログ][14]
 
    a. **[Provider Type]** として **[SAML]** を選択します。

    b. **[Provider Name]** ボックスにプロバイダー名を入力します (例: *WAAD*)。

    c. Azure Portal からダウンロードした**メタデータ ファイル**をアップロードするには、**[ファイルの選択]** をクリックします。

    d. ページの下部にある **[Next Step]**」を参照してください。

1. **[Verify Provider Information]** ダイアログ ボックスで、**[Create]** をクリックします。 
    
    ![シングル サインオンの検証の構成][15]

1. **[Roles]** をクリックしてから **[Create role]** をクリックします。 
    
    ![シングル サインオン ロールの構成][16]

1. **[Create role]** ページで、以下の手順を実行します。  
    
    ![シングル サインオンの信頼の構成][19] 

    a. **[Select type of trusted entity]** の **[SAML 2.0 federation]** を選択します。

    b. **[Choose a SAML 2.0 Provider section]** で、先ほど作成した **SAML プロバイダー**を選択します (例: *WAAD*)

    c. **[Allow programmatic and AWS Management Console access]** を選択します。
  
    d. **[Next: Permissions]** をクリックします。

1. **[Attach Permissions Policies]\(アクセス許可ポリシーのアタッチ\)** ダイアログ ボックスで、任意のポリシーをアタッチする必要はありません。 **[次へ: レビュー]** をクリックします。  
    
    ![シングル サインオン ポリシーの構成][33]

1. **[Review]** ダイアログで、次の手順を実行します。   
    
    ![シングル サインオンの構成の確認][34] 

    a. **[Role name]** テキストボックスに自分のロール名を入力します。

    b. **[Role description]** ボックスに説明を入力します

    c. **[Create Role]** をクリックします。

    d. 必要な数の役割ロールを作成し、それらを ID プロバイダーにマップします。

1. Azure AD ユーザー プロビジョニングの AWS アカウントからロールをフェッチするには、AWS サービス アカウントの資格情報を使用します。 そのためには、AWS コンソール ホームを開きます。

1. **[Services]** -> **[Security, Identity& Compliance]** -> **[IAM]** をクリックします。

    ![AWS アカウントからのロールのフェッチ](./media/amazon-web-service-tutorial/fetchingrole1.png)

1. [IAM] セクションで **[Policies]** タブを選択します。

    ![AWS アカウントからのロールのフェッチ](./media/amazon-web-service-tutorial/fetchingrole2.png)

1. AWS アカウントからロールを取得するために、Azure AD User Provisioning で **[Create policy]** をクリックして新しいポリシーを作成します。

    ![新しいポリシーの作成](./media/amazon-web-service-tutorial/fetchingrole3.png)

1. 以下の手順を実行して、AWS アカウントからすべてのロールをフェッチする独自のポリシーを作成します。

    ![新しいポリシーの作成](./media/amazon-web-service-tutorial/policy1.png)

    a. **[ポリシーの作成]** セクションで、**[JSON]** タブをクリックします。

    b. ポリシー ドキュメントで、次の JSON を追加します。
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam:ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. **[ポリシーの確認] ボタン**をクリックして、ポリシーを検証します。

    ![新しいポリシーの定義](./media/amazon-web-service-tutorial/policy5.png)

1. 以下の手順を実行して**新しいポリシー**を定義します。

    ![新しいポリシーの定義](./media/amazon-web-service-tutorial/policy2.png)

    a. **[Policy Name]** として **AzureAD_SSOUserRole_Policy** を指定します。

    b. ポリシーの **[Description]** には、「**This policy will allow to fetch the roles from AWS accounts**」を指定できます。
    
    c. **[ポリシーの作成]** ボタンをクリックします。

1.  次の手順を実行して、AWS IAM サービスで新しいユーザー アカウントを作成します。

    a. AWS IAM コンソールで **[Users]** ナビゲーションをクリックします。

    ![新しいポリシーの定義](./media/amazon-web-service-tutorial/policy3.png)
    
    b. **[Add user]** をクリックして新しいユーザーを作成します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/policy4.png)

    c. **[Add user]** セクションで、次の手順を実行します。
    
    ![ユーザーの追加](./media/amazon-web-service-tutorial/adduser1.png)
    
    * ユーザー名として「**AzureADRoleManager**」を入力します。
    
    * [Access type] で **[Programmatic access]** オプションを選択します。 こうすると、ユーザーは API を呼び出し、AWS アカウントからロールをフェッチできます。
    
    * 右下隅の **[Next Permissions]** をクリックします。

1. ここで、次の手順を実行して、このユーザーの新しいポリシーを作成します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/adduser2.png)
    
    a. **[Attach existing policies directly]** をクリックします。

    b. [フィルター] セクションで、新しく作成されたポリシー **AzureAD_SSOUserRole_Policy** を検索します。
    
    c. **ポリシー**を選択し、**[Next: Review]** をクリックします。

1.  次の手順を実行して、接続されたユーザーに対するポリシーを確認します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/adduser3.png)
    
    a. ユーザー名、アクセスの種類、ユーザーにマップされているポリシーを確認します。
    
    b. 右下隅にある **[Create user]** をクリックしてユーザーを作成します。

1. 次の手順を実行して、ユーザーのユーザー資格情報をダウンロードします。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/adduser4.png)
    
    a. ユーザーの **[Access key ID]** と **[Secret access key]** をコピーします。
    
    b. これらの資格情報を Azure AD の [ユーザー プロビジョニング] セクションに入力して、AWS コンソールからロールをフェッチします。
    
    c. 下部にある **[Close]** をクリックします。

1. Azure AD 管理ポータルでアマゾン ウェブ サービス アプリの **[ユーザー プロビジョニング]** セクションに移動します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/provisioning.png)

1. **アクセス キー**と**シークレット**をそれぞれ **[クライアント シークレット]** フィールドと **[シークレット トークン]** フィールドに入力します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/provisioning1.png)
    
    a. AWS ユーザーのアクセス キーを **[clientsecret]/(clientsecret/)** フィールドに入力します。
    
    b. AWS ユーザー シークレットを **[シークレット トークン]** フィールドに入力します。
    
    c. **[テスト接続]** をクリックすると、この接続を正常にテストできます。

    d. 上部にある **[保存]** をクリックして、設定を保存します。
 
1. スイッチをオンにしてから上部の **[保存]** をクリックして、[設定] セクションで [プロビジョニングの状態] を **[オン]** にします。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/provisioning2.png)

> [!Note]
> シングル サインオンのために複数の AWS アカウントを 1 つの Azure アカウントに統合したい場合は、[この](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial)記事を参照してください。 


### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/amazon-web-service-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/amazon-web-service-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/amazon-web-service-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/amazon-web-service-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>アマゾン ウェブ サービス (AWS) テスト ユーザーの作成

このセクションの目的は、Amazon Web Services (AWS) で Britta Simon というユーザーを作成することです。 アマゾン ウェブ サービス (AWS) では、SSO 用にユーザーをシステムに作成する必要がないため、ここで操作を実行する必要はありません。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon にアマゾン ウェブ サービス (AWS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Amazon Web Services (AWS) に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、 **[Amazon Web Services (AWS)]** を選択します。

    ![アプリケーションの一覧の [Amazon Web Services (AWS)] リンク](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Amazon Web Services (AWS)] タイルをクリックすると、自動的に Amazon Web Services (AWS) アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
