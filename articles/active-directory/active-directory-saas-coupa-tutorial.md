---
title: "チュートリアル: Azure Active Directory と Coupa の統合 | Microsoft Docs"
description: "Azure Active Directory で Coupa を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 08331c68378caf7cf3b2bb9bcc14a824a58a249f


---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>チュートリアル: Azure Active Directory と Coupa の統合
このチュートリアルでは、Azure と Coupa の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Coupa でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Coupa に割り当てた Azure AD ユーザーは、「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Coupa のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-coupa-tutorial/IC791897.png "Scenario")

## <a name="enabling-the-application-integration-for-coupa"></a>Coupa のアプリケーション統合の有効化
このセクションでは、Coupa のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-coupa-perform-the-following-steps"></a>Coupa のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![[Active Directory]](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![[アプリケーション]](./media/active-directory-saas-coupa-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![[アプリケーションの追加]](./media/active-directory-saas-coupa-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-coupa-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Coupa**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-coupa-tutorial/IC791898.png "Application Gallery")
7. 結果ウィンドウで **[Coupa]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
   ## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Coupa に対する認証を行うことができるようにする方法を説明します。  
Coupa にシングル サインオンを構成するには、証明書から拇印の値を取得する必要があります。  
この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Coupa 企業サイトに管理者としてサインオンします。
2. **[Setup]\>[Security Control]** の順に移動します。
   
   ![[シングル サインオンの設定]](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")
3. コンピューターに Coupa のメタデータ ファイルをダウンロードするには、 **[SP メタデータのダウンロードとインポート]**をクリックします。
   
   ![Coupa SP メタデータ](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP metadata")
4. 別のブラウザー ウィンドウで、Azure クラシック ポータルにサインオンします。
5. **Coupa** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![Configure Single Sign-On](./media/active-directory-saas-coupa-tutorial/IC791902.png "Configure Single Sign-On")
6. **[ユーザーの Coupa へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![Configure Single Sign-On](./media/active-directory-saas-coupa-tutorial/IC791903.png "Configure Single Sign-On")
7. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
   ![Configure App URL](./media/active-directory-saas-coupa-tutorial/IC791904.png "Configure App URL")
   
   1. **[サインオン URL]** ボックスに、ユーザーが Coupa アプリケーションへのサインオンに使用する URL (例: "*http://company.Coupa.com*") を入力します。
   2. ダウンロードした Coupa メタデータ ファイルを開き、 **AssertionConsumerService index/URL**をコピーします。
   3. **[Coupa 応答 URL]** ボックスに、**AssertionConsumerService index/URL** 値を貼り付けます。
   4. **[次へ]**をクリックします。
8. **[Coupa でのシングル サインオンの構成]** ページで、メタデータ ファイルをダウンロードするには、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターにローカルに保存します。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-coupa-tutorial/IC791905.png "Configure Single Sign-On")
9. Coupa 企業サイトで、**[設定]\>[セキュリティ コントロール]** の順にクリックします。
   
   ![[シングル サインオンの設定]](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")
10. **[Coupa 資格情報を使用してログイン]** セクションで、次の手順を実行します。
    
    ![[Coupa 資格情報を使用してログイン]](./media/active-directory-saas-coupa-tutorial/IC791906.png "Log in using Coupa credentials")
    
    1. **[SAML を使用してログイン]**を選択します。
    2. **[参照]** をクリックして、ダウンロードした Azure Active メタデータ ファイルをアップロードします。
    3. [ **Save**] をクリックします。
11. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-coupa-tutorial/IC791907.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Coupa にログインできるようにするには、そのユーザーを Coupa にプロビジョニングする必要があります。  
Coupa の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. **Coupa** 企業サイトに管理者としてログインします。
2. 上部のメニューで、**[設定]**、**[ユーザー]** の順にクリックします。
   
   ![Users](./media/active-directory-saas-coupa-tutorial/IC791908.png "Users")
3. **[作成]**をクリックします。
   
   ![ユーザーの作成](./media/active-directory-saas-coupa-tutorial/IC791909.png "Create Users")
4. **[ユーザーを作成]** セクションで、次の手順を実行します。
   
   ![[User Details]](./media/active-directory-saas-coupa-tutorial/IC791910.png "User Details")
   
   1. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの属性として、**ログイン**、**名**、**姓**、**シングル サインオン ID**、**電子メール**を入力します。
   2. **[作成]**をクリックします。
   
   > [!NOTE]
   > Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
   > 
   > 

> [!NOTE]
> Coupa から提供されている他の Coupa ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-coupa-perform-the-following-steps"></a>ユーザーを Coupa に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Coupa** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![[ユーザーの割り当て]](./media/active-directory-saas-coupa-tutorial/IC791911.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-coupa-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Nov16_HO3-->


