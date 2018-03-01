---
title: "チュートリアル: Azure Active Directory と Amazon Web Services (AWS) の統合 | Microsoft Docs"
description: "Azure Active Directory と Amazon Web Services (AWS) の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/16/2017
ms.author: jeedes
ms.openlocfilehash: 0ff14365323d66a101e5847d7959045c3f20dea2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>チュートリアル: Azure Active Directory と Amazon Web Services (AWS) の統合

このチュートリアルでは、Amazon Web Services (AWS) と Azure Active Directory (Azure AD) を統合する方法について説明します。

Amazon Web Services (AWS) と Azure AD の統合には、次の利点があります。

- Amazon Web Services (AWS) にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的にアマゾン ウェブ サービス (AWS) にサインインできるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Amazon Web Services (AWS) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Amazon Web Services (AWS) でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使わないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使わないでください。
- Azure AD の評価環境がない場合は、[1 か月間無料の試用版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Amazon Web Services (AWS) の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>ギャラリーからの Amazon Web Services (AWS) の追加
Azure AD への Amazon Web Services (AWS) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amazon Web Services (AWS) を追加する必要があります。

**ギャラリーからアマゾン ウェブ サービス (AWS) を追加するには、次の手順を実行します:** 

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンを選びます。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**Amazon Web Services (AWS)**」と入力します。 結果パネルから **[アマゾン ウェブ サービス (AWS)]** を選択し、**[追加]** を選択してアプリケーションを追加します。

    ![結果の一覧の Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、アマゾン ウェブ サービス (AWS) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応するアマゾン ウェブ サービス (AWS) ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーとアマゾン ウェブ サービス (AWS) の関連ユーザーの間で、リンクが確立されている必要があります。

リンクを確立するには、アマゾン ウェブ サービス (AWS) の**ユーザー名**に、Azure AD 内の**ユーザー名**と同じ値を割り当てます。 

アマゾン ウェブ サービス (AWS) との Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
3. [アマゾン ウェブ サービス (AWS) テスト ユーザーの作成](#create-an-amazon-web-services-aws-test-user) - アマゾン ウェブ サービス (AWS) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、Amazon Web Services (AWS) アプリケーションへのシングル サインオンを構成します。

**アマゾン ウェブ サービス (AWS) との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の**アマゾン ウェブ サービス (AWS)** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

2. シングル サインオンを有効にするには、**[シングル サインオン]** ダイアログ ボックスの **[モード]** ドロップダウン リストで **[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. アプリは Azure と事前に統合済みであるため、**[アマゾン ウェブ サービス (AWS) のドメインと URL]** セクションで特に手順を実施する必要はありません。

    ![[アマゾン ウェブ サービス (AWS) のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. アマゾン ウェブ サービス (AWS) アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットは、例を示しています。

    ![シングル サインオン属性の構成](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. **[シングル サインオン]** ボックスの **[ユーザー属性]** セクションで、前の画像で示されているように、SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名  | 属性名 | 名前空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 役割            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >アマゾン ウェブ サービス (AWS) コンソールからすべてのロールをフェッチするには、Azure AD でユーザー プロビジョニングを構成します。 次のプロビジョニング手順を参照してください。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[属性の追加]** ダイアログ ボックスを開くには、**[属性の追加]** を選択します。

    ![シングル サインオン属性の構成](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![シングル サインオン属性の構成](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される属性値を入力します。

    d. **[名前空間]** ボックスに、その行に表示される名前空間の値を入力します。
    
    d. **[OK]** を選びます。

6. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** を選択します。 次に、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. 別の Web ブラウザーのウィンドウで、管理者としてアマゾン ウェブ サービス (AWS) 企業サイトにサインインします。

9. **[Console Home]\(コンソール ホーム\)** を選択します。
   
    ![シングル サインオン ホームの構成][11]

10. **[Identity and Access Management]\(ID とアクセス管理\)** を選択します。 
   
    ![シングル サインオン ID の構成][12]

11. **[Identity Providers]\(ID プロバイダー\)** を選択します。 **[Create Provider]\(プロバイダーを作成する\)** を選択します。 
   
    ![シングル サインオン プロバイダーの構成][13]

12. **[Configure Provider]\(プロバイダーの構成\)** ダイアログ ボックスで、次の手順を実行します。 
   
    ![シングル サインオンの構成ダイアログ][14]
 
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Provider Type]\(プロバイダーの種類\)** で **[SAML]** を選択します。

    b. **[Provider Name]\(プロバイダー名\)** ボックスにプロバイダー名を入力します (例: *WAAD*)。

    c. Azure Portal からダウンロードした**メタデータ ファイル**をアップロードするには、**[Choose File]\(ファイルの選択\)** を選択します。

    d. **[Next Step]\(次のステップ\)** を選択します。

13. **[Verify Provider Information]\(プロバイダー情報の検証\)** ダイアログ ボックスで **[Create]\(作成\)** を選択します。 
    
    ![シングル サインオン検証の構成][15]

14. **[役割]** を選びます。 **[Create New Role]\(新しいロールの作成\)** を選択します。 
    
    ![シングル サインオン ロールの構成][16]

15. **[Set Role Name]\(ロール名の設定\)** ダイアログ ボックスで、次の手順を実行します。 
    
    ![シングル サインオン名の構成][17] 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Role Name]\(ロール名\)** ボックスにロール名を入力します (例: *TestUser*)。 

    b. **[Next Step]\(次のステップ\)** を選択します。

16. **[Select Role Type]\(ロールの種類の選択\)** ダイアログ ボックスで、次の手順を実行します。 
    
    ![シングル サインオンのロールの種類の構成][18] 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Role For Identity Provider Access]** を選択します。 

    b. **[Grant Web Single Sign-On (WebSSO) access to SAML providers]** セクションで、**[Select]** をクリックします。

17. **[Establish Trust]\(信頼の確立\)** ダイアログ ボックスで、次の手順を実行します。  
    
    ![シングル サインオンの信頼の構成][19] 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 先ほど作成した SAML プロバイダーを選択します (例: *WAAD*)。 
  
    b. **[Next Step]\(次のステップ\)** を選択します。

18. **[Verify Role Trust]\(ロールの信頼の検証\)** ダイアログ ボックスで、**[Next Step]\(次のステップ\)** を選択します。 
    
    ![シングル サインオン ロールの信頼の構成][32]

19. **[Attach Policy]\(ポリシーのアタッチ\)** ダイアログ ボックスで、**[Next Step]\(次のステップ\)** を選択します。  
    
    ![シングル サインオン ポリシーの構成][33]

20. **[Review]\(確認\)** ダイアログ ボックスで、次の手順を実行します。   
    
    ![シングル サインオンの構成の確認][34] 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Create Role]\(ロールの作成\)** を選択します。

    b. 必要な数のロールを作成し、それらを ID プロバイダーにマップします。

21. Azure AD ユーザー プロビジョニングのアマゾン ウェブ サービス (AWS) アカウントからロールをフェッチするには、アマゾン ウェブ サービス (AWS) のサービス アカウントの資格情報を使用します。 このタスクを開始するには、アマゾン ウェブ サービス (AWS) のコンソール ホームを開きます。

22. **[Services]\(サービス\)** > **[Security, Identity & Compliance]\(セキュリティ、ID、およびコンプライアンス\)** > **[IAM]** を選択します。

    ![アマゾン ウェブ サービス (AWS) アカウントからロールをフェッチ](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. IAM セクションで **[Policies]\(ポリシー\)** タブを選択します。

    ![アマゾン ウェブ サービス (AWS) アカウントからロールをフェッチ](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. 新しいポリシーを作成するには、**[Create policy]\(ポリシーの作成\)** を選択します。

    ![新しいポリシーの作成](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. アマゾン ウェブ サービス (AWS) のアカウントからすべてのロールをフェッチする独自のポリシーを作成するには、次の手順を実行します。

    ![新しいポリシーの作成](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Create policy]\(ポリシーの作成\)** セクションで、**[JSON]** タブを選択します。

    b. ポリシー ドキュメントで、次の JSON を追加します。
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam: ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. ポリシーを検証するには、**[Review Policy]\(ポリシーの確認\) ボタン**を選択します。

    ![新しいポリシーの定義](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. 以下の手順を実行して**新しいポリシー**を定義します。

    ![新しいポリシーの定義](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Policy Name]** として **AzureAD_SSOUserRole_Policy** を指定します。

    b. ポリシーの**説明**には、「**This policy will allow you to fetch the roles from AWS accounts**」を指定できます。
    
    c. **[Create Policy]\(ポリシーの作成\)** ボタンを選択します。
        
27. アマゾン ウェブ サービス (AWS) IAM サービスに新しいユーザー アカウントを作成するには、次の手順を実行します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 アマゾン ウェブ サービス (AWS) IAM コンソールの **[Users]\(ユーザー\)** を選択します。

    ![新しいポリシーの定義](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. 新しいユーザーを作成するには、**[Add user]\(ユーザーの追加\)** ボタンを選択します。

    ![ユーザーの追加](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. **[Add user]\(ユーザーの追加\)** セクションで、次の手順を実行します。
    
    ![ユーザーの追加](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * ユーザー名のボックスに「**AzureADRoleManager**」と入力します。
    
    * [Access type]\(アクセスの種類\) で、**[Programmatic access]\(プログラム アクセス\)** オプションを選択します。 こうすると、ユーザーは API を呼び出し、アマゾン ウェブ サービス (AWS) アカウントからロールをフェッチできます。
    
    * 右下隅の **[Next Permissions]\(次のアクセス許可\)** ボタンを選択します。

28. 次の手順を実行して、このユーザーの新しいポリシーを作成します。

    ![ユーザーの追加](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Attach existing policies directly]\(既存のポリシーを直接アタッチする\)** ボタンを選択します。

    b. [フィルター] セクションで、新しく作成されたポリシー **AzureAD_SSOUserRole_Policy** を検索します。
    
    c. **ポリシー**を選択します。 続けて、**[Next: Review]\(次へ: 確認\)** ボタンを選択します。

29. 次の手順を実行して、接続されたユーザーに対するポリシーを確認します。

    ![ユーザーの追加](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 ユーザー名、アクセスの種類、ユーザーにマップされているポリシーを確認します。
    
    b. ユーザーを作成するには、右下隅の **[Create user]\(ユーザーの作成\)** ボタンを選択してユーザーを作成します。

30. 次の手順を実行して、ユーザーの資格情報をダウンロードします。

    ![ユーザーの追加](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 ユーザーの **[Access key ID]** と **[Secret access key]** をコピーします。
    
    b. これらの資格情報を Azure AD の [ユーザー プロビジョニング] セクションに入力して、アマゾン ウェブ サービス (AWS) コンソールからロールをフェッチします。
    
    c. 右上にある **[閉じる]** ボタンを選択します。

31. Azure AD 管理ポータルでアマゾン ウェブ サービス (AWS) アプリの **[ユーザー プロビジョニング]** セクションに移動します。

    ![ユーザーの追加](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. **アクセス キー**と**シークレット**をそれぞれ **[クライアント シークレット]** フィールドと **[シークレット トークン]** フィールドに入力します。

    ![ユーザーの追加](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 アマゾン ウェブ サービス (AWS) ユーザーのアクセス キーを **[clientsecret]** フィールドに入力します。
    
    b. アマゾン ウェブ サービス (AWS) ユーザーのシークレットを **[シークレット トークン]** フィールドに入力します。
    
    c. **[接続のテスト]** ボタンを選択します。 この接続テストに成功することが必要です。

    d. 上部にある **[保存]** を選択して、設定を保存します。
 
33. **[設定]** の [プロビジョニングの状態] が **[オン]** になっていることを確認します。 オンの状態にするには、**[オン]** を選択して上部の **[保存]** ボタンを選択してください。

    ![ユーザーの追加](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。 このアプリを追加した後、**[Active Directory] > [エンタープライズ アプリケーション]** セクションで、**[シングル サインオン]** タブを選びます。次に、下部の **[構成]** セクションから組み込みドキュメントにアクセスします。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動します。 次に、**[すべてのユーザー]** を選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**を選択します。
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>アマゾン ウェブ サービス (AWS) テスト ユーザーの作成

このセクションの目的は、Amazon Web Services (AWS) で Britta Simon というユーザーを作成することです。 アマゾン ウェブ サービス (AWS) では、シングル サインオン用にユーザーをシステムに作成する必要がないため、ここで操作を実行する必要はありません。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon にアマゾン ウェブ サービス (AWS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon をアマゾン ウェブ サービス (AWS) に割り当てるには、次の手順に従います。**

1. Azure ポータルで、アプリケーション ビューを開きます。 続けて、ディレクトリ ビューに移動し、**[エンタープライズ アプリケーション]** を選択します。 次に、**[すべてのアプリケーション]** を選びます。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[アマゾン ウェブ サービス (AWS)]** を選択します。

    ![アプリケーションの一覧の [アマゾン ウェブ サービス (AWS)] リンク](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンを選びます。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。 

7. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [アマゾン ウェブ サービス (AWS)] タイルをクリックすると、自動的にアマゾン ウェブ サービス (AWS) アプリケーションにサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

