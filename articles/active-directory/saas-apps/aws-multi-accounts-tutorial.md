---
title: 'チュートリアル: Azure Active Directory と Amazon Web Services (AWS) を統合して複数のアカウントを接続する | Microsoft Docs'
description: Azure AD と Amazon Web Services (AWS) の複数のアカウントの間でシングルサインオンを構成する方法について説明します。
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
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: 9634d8ede40500bf0a92ae07a1a514895d355a31
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437935"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>チュートリアル: Azure Active Directory と複数の Amazon Web Services (AWS) アカウントの統合

このチュートリアルでは、Azure Active Directory (Azure AD) と Amazon Web Services (AWS) の複数のアカウントを統合する方法を説明します。

Amazon Web Services (AWS) と Azure AD の統合には、次の利点があります。

- Amazon Web Services (AWS) にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Amazon Web Services (AWS) にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Amazon Web Services (AWS) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD の Basic または Premium サブスクリプションを取得する
- Amazon Web Services (AWS) での複数のシングル サインオンが有効なアカウント

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

    ![結果の一覧の Amazon Web Services (AWS)](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

1. アプリケーションが追加されたら、**[プロパティ]** ページに移動して、**オブジェクト ID** をコピーします

    ![結果の一覧の Amazon Web Services (AWS)](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Amazon Web Services (AWS) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Amazon Web Services (AWS) ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと Amazon Web Services (AWS) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Amazon Web Services (AWS) で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Amazon Web Services (AWS) との Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、Amazon Web Services (AWS) アプリケーションへのシングル サインオンを構成します。

**Amazon Web Services (AWS) との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Amazon Web Services (AWS)** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

1. アプリは Azure と事前に統合済みであるため、**[Amazon Web Services (AWS) のドメインと URL]** セクションで特に手順を実施する必要はありません。

    ![[Amazon Web Services (AWS) のドメインと URL] のシングル サインオン情報](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

1. Amazon Web Services (AWS) アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。

    ![シングル サインオン属性の構成](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)    

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名  | 属性値 | 名前空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >AWS コンソールからすべてのロールをフェッチするには、Azure AD でユーザー プロビジョニングを構成する必要があります。 次のプロビジョニングの手順をご覧ください。

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![シングル サインオンの構成の追加](./media/aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![シングル サインオン属性の構成](./media/aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** ボックスに、その行に表示される名前空間の値を入力します。
    
    d. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/aws-multi-accounts-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザーのウィンドウで、管理者として Amazon Web Services (AWS) 企業サイトにサインオンします。

1. **[AWS Home]** をクリックします。
   
    ![シングル サインオン ホームの構成][11]

1. **[IAM]**(Identity and Access Management) をクリックします。 
   
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

1. **[Attach Permissions Policies]** ダイアログで **[Next: Review]** をクリックします。  
    
    ![シングル サインオン ポリシーの構成][33]

1. **[Review]** ダイアログで、次の手順を実行します。   
    
    ![シングル サインオンの構成の確認][34] 

    a. **[Role name]** テキストボックスに自分のロール名を入力します。

    b. **[Role description]** ボックスに説明を入力します

    a. **[Create Role]** をクリックします。

    b. 必要な数の役割ロールを作成し、それらを ID プロバイダーにマップします。

1. 現在の AWS アカウントからサインアウトし、Azure AD によるシングル サインオンを構成する他のアカウントでログインします。

1. 手順 9 から 17 を実行して、このアカウントにセットアップする複数のロールを作成します。 3 つ以上のアカウントがある場合は、それらのロールを作成するすべてのアカウントについて同じ手順を実行してください。

1. アカウントですべてのロールが作成されると、それらのアカウントの **[Roles]** 一覧にそれらが表示されます。

    ![ロールのセットアップ](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

1. すべてのアカウントのすべてのロールのロール ARN と信頼済みエンティティを取得する必要があります。それらは、Azure AD アプリケーションと手動でマッピングするために必要です。 

1. ロールをクリックして、**[Role ARN]** と **[Trusted Entities]** の値をコピーします。 Azure AD で作成する必要があるすべてのロールにこれらの値が必要です。

    ![ロールのセットアップ](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
1. すべてのアカウントのすべてのロールに対して上記の手順を実行し、**ロール ARN,信頼済みエンティティ**の形式でメモ帳に記録します。 

1. 別のウィンドウで [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開きます。

    a. テナントのグローバル管理者/共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。

    b. ロールを作成するための十分なアクセス許可が必要です。 **[アクセス許可の変更]** をクリックして、必要なアクセス許可を取得します。 

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. 一覧から次のアクセス許可を選択して (まだない場合)、[アクセス許可の変更] をクリックします。 

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. ここで、再度ログインし、同意を受け入れるよう求められます。 同意を受け入れると、Graph Explorer に再度ログインされます。

    e. バージョン ドロップダウンを**ベータ**に変更します。 テナントからすべてのサービス プリンシパルを取得するには、次のクエリを使用します。
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    複数のディレクトリを使用している場合、次のパターンを使用できます。プライマリ ドメインは `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` にあります。
    
    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. 取得したサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl キーを押しながら F キーを押して、一覧に示されたすべての ServicePrincipals からアプリケーションを検索することもできます。 Azure AD プロパティ ページからコピーした **オブジェクト ID** を使用して、次のクエリを使用し、対応するサービス プリンシパルを取得することができます。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. サービス プリンシパル オブジェクトから appRoles プロパティを抽出します。 

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. ここで、アプリケーションの新しいロールを生成する必要があります。 

    i. 以下の JSON は、appRoles オブジェクトの例です。 同様のオブジェクトを作成して、アプリケーションに必要なロールを追加します。 

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > パッチ操作では、**msiam_access** の後にのみ、新しいロールを追加できます。 また、組織のニーズごとに必要な数のロールを追加することができます。 Azure AD では、SAML 応答の要求値として、これらのロールの**値**を送信します。
    
    j. Graph Explorer に戻り、メソッドを **GET** から **PATCH** に変更します。 上の例に示されているように appRoles プロパティを更新して、必要なロールを持つようにサービス プリンシパル オブジェクトを修正します。 **[クエリの実行]** をクリックして、パッチ操作を実行します。 成功のメッセージで、Amazon Web Services アプリケーションのロールの作成を確認します。

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. より多くのロールでサービス プリンシパルを修正したら、対応するロールにユーザー/グループを割り当てることができます。 これは、ポータルに移動し、Amazon Web Services アプリケーションに移動することで実行できます。 上部の **[ユーザーとグループ]** タブをクリックします。 

1. グループで特定のロールを割り当てることができるように、すべての AWS ロール用に新しいグループを作成することをお勧めします。 これは 1 つのグループが 1 つのロールに対応する 1 対 1 のマッピングであることに注意してください。 そのグループに属するメンバーを追加できます。

1. グループが作成されたら、グループを選択し、アプリケーションに割り当てます。 

    ![シングル サインオンの構成の追加](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

1. グループにロールを割り当てるには、ロールを選択し、ページの下部にある **[割り当て]** ボタンをクリックします。

    ![シングル サインオンの構成の追加](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> 新しいロールを表示するには、Azure Portal でセッションを更新する必要があることに注意してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Amazon Web Services (AWS)] タイルをクリックすると、Amazon Web Services (AWS) アプリケーション ページが表示され、ロールを選択するオプションが表示されます。

![シングル サインオンの構成の追加](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

SAML 応答を確認し、クレームとして渡されるロールを参照することもできます。

![シングル サインオンの構成の追加](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png

