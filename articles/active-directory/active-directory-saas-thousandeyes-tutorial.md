---
title: "チュートリアル: Azure Active Directory と ThousandEyes の統合 | Microsoft Docs"
description: "Azure Active Directory で ThousandEyes を使用してシングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: e0f85965cb884022f665d3664bc2b824095ad0fe


---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>チュートリアル: Azure Active Directory と ThousandEyes の統合
このチュートリアルでは、Azure Active Directory (Azure AD) と ThousandEyes の間でのシングル サインオンを設定する方法を説明します。

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* ThousandEyes でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、ThousandEyes に割り当てた AAD ユーザーは、ThousandEyes 企業サイト (サービス プロバイダーが開始したサインオン) で、または AAD アクセス パネルを使用して、アプリケーションにシングル サインオンできるようになります。

1. ThousandEyes のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Scenario")

## <a name="enabling-the-application-integration-for-thousandeyes"></a>ThousandEyes のアプリケーション統合の有効化
このセクションでは、ThousandEyes のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-thousandeyes-perform-the-following-steps"></a>ThousandEyes のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![[Active Directory]](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![[アプリケーションの追加]](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**ThousandEyes**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Application Gallery")
7. 結果ウィンドウで **[ThousandEyes]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure Active Directory でのユーザーのアカウントで ThousandEyes に対する認証を行えるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **ThousandEyes** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configure Single SignOn")

1. **[ユーザーの ThousandEyes へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configure Single SignOn")

3. **[アプリケーション URL の構成]** ページの **[ThousandEyes のサインオン URL]** テキストボックスに、ユーザーが ThousandEyes アプリケーションにサインオンするときに使用する URL (例: "*https://app.thousandeyes.com/login/sso*") を入力し、**[次へ]** をクリックします。 
   
    ![Configure App URL](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configure App URL")

4. **[ThousandEyes でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configure Single SignOn")

5. 別の Web ブラウザーのウィンドウで、管理者として **ThousandEyes** 企業サイトにサインオンします。

6. 上部のメニューで **[Settings]**をクリックします。
   
    ![[Settings]](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

7. ページの下部にある **[Account]**
   
    ![アカウント](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

8. **[Security & Authentication]** タブをクリックします。
   
    ![[Security & Authentication]](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Security & Authentication")

9. **[Setup Single Sign-On]** セクションで、次の手順を実行します。
   
    ![[Setup Single Sign-On]](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Setup Single Sign-On")
   
    a. **[シングル サインオンを有効にする]**を選択します。
   
    b. Microsoft Azure クラシック ポータルの **[ThousandEyes でのシングル サインオンの構成]** ページで、**[リモート ログイン URL]** 値をコピーして、**[ログイン ページの URL]** テキスト ボックスに貼り付けます。
   
    c. Microsoft Azure クラシック ポータルの **[ThousandEyes でのシングル サインオンの構成]** ページで、**[リモート ログアウト URL]** 値をコピーして、**[ログアウト ページの URL]** テキスト ボックスに貼り付けます。
   
    d. Microsoft Azure クラシック ポータルの **[ThousandEyes でのシングル サインオンの構成]** ページで、**[発行者の URL]** 値をコピーして、**[ID プロバイダーの発行者]** テキスト ボックスに貼り付けます。
   
    e. **[ID プロバイダー証明書]** で **[ファイルの選択]** をクリックし、Microsoft Azure クラシック ポータルからダウンロードした証明書をアップロードします。
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 [ **Save**] をクリックします。

10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configure Single SignOn")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Azure AD ユーザーが ThousandEyes にログインできるようにするには、そのユーザーを ThousandEyes にプロビジョニングする必要があります。  
ThousandEyes の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-account-to-thousandeyes-perform-the-following-steps"></a>ユーザー アカウントを ThousandEyes にプロビジョニングするには、次の手順に従います。
1. ThousandEyes 企業サイトに管理者としてログインします。

2. **[Settings]**をクリックします。
   
    ![[Settings]](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

3. [ **アカウント**] クリックします。
   
    ![アカウント](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

4. **[Accounts & Users]** タブをクリックします。
   
    ![[Accounts & Users]](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Accounts & Users")

5. **[Add Users & Accounts]** セクションで、次の手順を実行します。
   
    ![ユーザー アカウントの追加](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Add User Accounts")
   
    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 プロビジョニングする有効な Azure Active Directory アカウントの **[Name]**、**[Email]**、その他の詳細を該当するボックスに入力します。
   
    b. **[Add New User to Account]**をクリックします。
      
    > [!NOTE]
    > AAD アカウントの所有者にアカウントの確認およびアクティブ化用のリンクを含む電子メールが送信されます。
    > 
    > 

> [!NOTE]
> ThousandEyes から提供されている他の ThousandEyes ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-thousandeyes-perform-the-following-steps"></a>ユーザーを ThousandEyes に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **ThousandEyes** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![[ユーザーの割り当て]](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Dec16_HO1-->


