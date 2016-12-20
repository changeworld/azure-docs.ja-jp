---
title: "チュートリアル: Azure Active Directory と Huddle の統合 | Microsoft Docs"
description: "Azure Active Directory で Huddle を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85d7789599cfb39ce0de5d52e0fe057482a49039


---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>チュートリアル: Azure Active Directory と Huddle の統合
このチュートリアルでは、Azure と Huddle の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Huddle でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Huddle に割り当てた Azure AD ユーザーは、Huddle 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Huddle のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![Configure Single Sign-On](./media/active-directory-saas-huddle-tutorial/IC787830.png "Configure Single Sign-On")

## <a name="enabling-the-application-integration-for-huddle"></a>Huddle のアプリケーション統合の有効化
このセクションでは、Huddle のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>Huddle のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![[Active Directory]](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![[アプリケーション]](./media/active-directory-saas-huddle-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![[アプリケーションの追加]](./media/active-directory-saas-huddle-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-huddle-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に「**Huddle**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-huddle-tutorial/IC787831.png "Application Gallery")
7. 結果ウィンドウで **[Huddle]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
   
   ## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Huddle に対する認証を行うことができるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Huddle** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-huddle-tutorial/IC787833.png "Configure Single Sign-On")
2. **[ユーザーの Huddle へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-huddle-tutorial/IC787834.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページの **[Huddle サインオン URL]** テキストボックスに、"*http://company.huddle.com*" のパターンを使用して Huddle テナントの URL を入力し、**[次へ]** をクリックします。
   
   ![Configure App URL](./media/active-directory-saas-huddle-tutorial/IC787835.png "Configure App URL")
4. **[Huddle でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
   ![Configure Single Sign-On](./media/active-directory-saas-huddle-tutorial/IC787836.png "Configure Single Sign-On")
   
   1. **[証明書のダウンロード]**をクリックし、コンピューターに証明書ファイルを保存します。
   2. **[発行者の URL]** の値、**[SAML SSO URL]** の値、およびダウンロードした証明書をコピーし、それらを Huddle サポート チームに送信します。
   
   > [!NOTE]
   > Huddle サポート チームがシングル サインオンを有効にする必要があります。
   > 構成が完了すると、通知が届きます。
   > 
   > 
5. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-huddle-tutorial/IC787837.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Huddle にログインできるようにするには、そのユーザーを Huddle にプロビジョニングする必要があります。  
Huddle の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. **Huddle** 企業サイトに管理者としてログインします。
2. **[ワークスペース]**をクリックします。
3. **[ユーザー] \>[ユーザーの招待]** の順にクリックします。
   
   ![ユーザー](./media/active-directory-saas-huddle-tutorial/IC787838.png "People")
4. **[新しい招待の作成]** セクションで、次の手順を実行します。
   
   ![新しい招待](./media/active-directory-saas-huddle-tutorial/IC787839.png "New Invitation")
   
   1. **[ユーザーを招待して参加させるチームの選択]** の一覧で **[チーム]** を選択します。
   2. プロビジョニングする有効な AAD アカウントの **電子メール アドレス** を、関連するテキスト ボックスに入力します。
   3. **[招待]**をクリックします。
   
   > [!NOTE]
   > アカウントがアクティブになる前に、Azure AD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。
   > 
   > 

> [!NOTE]
> Huddle から提供されている他の Huddle ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-huddle-perform-the-following-steps"></a>ユーザーを Huddle に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Huddle** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![[ユーザーの割り当て]](./media/active-directory-saas-huddle-tutorial/IC787840.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-huddle-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Nov16_HO3-->


