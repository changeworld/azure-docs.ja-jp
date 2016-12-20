---
title: "チュートリアル: Azure Active Directory と Greenhouse の統合 | Microsoft Docs"
description: "Azure Active Directory で Greenhouse を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 78ec1766-4f79-4f16-9a66-d5584c4b6151
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 55a9a01414d930b40f60119318f5c023172141e1


---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>チュートリアル: Azure Active Directory と Greenhouse の統合
このチュートリアルでは、Azure と Greenhouse の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Greenhouse シングル サインオンでのサブスクリプション

このチュートリアルを完了すると、Greenhouse に割り当てた Azure AD ユーザーは、Greenhouse 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Greenhouse のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scenario")

## <a name="enabling-the-application-integration-for-greenhouse"></a>Greenhouse のアプリケーション統合の有効化
このセクションでは、Greenhouse のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>Greenhouse のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![[Active Directory]](./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![[アプリケーション]](./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![[アプリケーションの追加]](./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に「**greenhouse**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Application Gallery")
7. 結果ウィンドウで **[Greenhouse]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![greenhouse](./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Greenhouse")
   
   ## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Greenhouse に対する認証を行うことができるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Greenhouse** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![Configure single sign-on](./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configure single sign-on")
2. **[ユーザーの Greenhouse へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![Configure single sign-on](./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configure single sign-on")
3. **[アプリ URL の構成]** ページの **[サインオン URL]** ボックスに、"*https://company.greenhouse.io*" というパターンの URL を入力して、**[次へ]** をクリックします。
   
   ![Configure App URL](./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configure App URL")
4. **[Greenhouse でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルでメタデータ ファイルを保存します。
   
   ![Configure single sign-on](./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configure single sign-on")
5. このメタデータ ファイルを Greenhous サポート チームに転送します。
   
   > [!NOTE]
   > Greenhouse サポート チームがシングル サインオンを有効にする必要があります。
   > 
   > 
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![Configure single sign-on](./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Greenhouse にログインできるようにするには、ユーザーを Greenhouse にプロビジョニングする必要があります。  
Greenhouse の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。
1. **Greenhouse** 企業サイトに管理者としてログインします。
2. 上部のメニューで、**[構成]**、**[ユーザー]** の順にクリックします。
   
   ![Users](./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Users")
3. **[新しいユーザー]**をクリックします。
   
   ![新しいユーザー](./media/active-directory-saas-greenhouse-tutorial/IC790792.png "New User")
4. **[新しいユーザーの追加]** セクションで、次の手順を実行します。
   
   ![[新しいユーザーの追加]](./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Add New User")
   
   1. **[ユーザー電子メールの入力]** テキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの電子メール アドレスを入力します。
   2. **[保存]**をクリックします。
      
      > [!NOTE]
      > Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
      > 
      > 

> [!NOTE]
> Greenhouse から提供されている他の Greenhouse ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>ユーザーを Greenhouse に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Greenhouse** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![[ユーザーの割り当て]](./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Nov16_HO3-->


