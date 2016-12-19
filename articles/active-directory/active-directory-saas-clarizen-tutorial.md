---
title: "チュートリアル: Azure Active Directory と Clarizen の統合 | Microsoft Docs"
description: "Azure Active Directory で Clarizen を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9f8d2418fe86163a4022960f87803c82487c86fd


---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>チュートリアル: Azure Active Directory と Clarizen の統合
このチュートリアルでは、Azure と Clarizen の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Clarizen でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Clarizen に割り当てた Azure AD ユーザーは、Clarizen 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Clarizen のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenario")

## <a name="enabling-the-application-integration-for-clarizen"></a>Clarizen のアプリケーション統合の有効化
このセクションでは、Clarizen のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>Clarizen のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![[Active Directory]](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![[アプリケーション]](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![[アプリケーションの追加]](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Clarizen**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Application Gallery")
7. 結果ウィンドウで **[Clarizen]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
   
   ## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Clarizen に対する認証を行うことができるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Clarizen** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configure Single Sign-On")
2. **[ユーザーの Clarizen へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configure Single Sign-On")
3. **[Clarizen でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configure Single Sign-On")
4. 別の Web ブラウザー ウィンドウで、**Clarizen** 企業サイトに管理者としてログインします (例: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*)。
5. 自分のユーザー名をクリックし、 **[設定]**をクリックします。
   
   ![[設定]](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Settings")
6. **[グローバル設定]** タブをクリックし、**[フェデレーション認証]** の横にある **[編集]** をクリックします。
   
   ![グローバル設定](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Global Settings")
7. **[フェデレーション認証]** ダイアログで、次の手順を実行します。
   
   ![[フェデレーション認証]](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federated Authentication")
   
   1. **[アップロード]** をクリックして、ダウンロードした証明書をアップロードします。
   2. Azure クラシック ポータルの **[Clarizen でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[サインイン URL]** テキストボックスに貼り付けます。
   3. Azure クラシック ポータルの **[Clarizen でのシングル サインアウトの構成]** ダイアログ ページで、**[シングル サインアウト サービス URL]** の値をコピーし、**[サインアウト URL]** テキストボックスに貼り付けます。
   4. **[POST を使用]**を選択します。
   5. **[Save]**をクリックします。
8. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Clarizen にログインできるようにするには、そのユーザーを Clarizen にプロビジョニングする必要があります。  
Clarizen の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。
1. **Clarizen** 企業サイトに管理者としてログインします。
2. **[ユーザー]**をクリックします。
   
   ![[ユーザー]](./media/active-directory-saas-clarizen-tutorial/IC784689.png "People")
3. **[ユーザーの招待]**をクリックします。
   
   ![ユーザーの招待](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invite Users")
4. [ユーザーの招待] ダイアログ ページで、次の手順を実行します。
   
   ![ユーザーの招待](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invite People")
   
   1. **[電子メール]** テキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの電子メール アドレスを入力します。
   2. **[招待]**をクリックします。
   
   > [!NOTE]
   > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。
   > 
   > 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>ユーザーを Clarizen に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Clarizen** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![[ユーザーの割り当て]](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Nov16_HO3-->


