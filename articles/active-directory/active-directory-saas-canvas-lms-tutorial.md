---
title: "チュートリアル: Azure Active Directory と Canvas LMS の統合 | Microsoft Docs"
description: "Azure Active Directory で Canvas LMS を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0031446f8b5202f6627ed02e90c4789f6d434234


---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>チュートリアル: Azure Active Directory と Canvas LMS の統合
このチュートリアルでは、Azure と Canvas の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Canvas テナント

このチュートリアルを完了すると、Canvas に割り当てた Azure AD ユーザーは、Canvas 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Canvas のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scenario")

## <a name="enabling-the-application-integration-for-canvas"></a>Canvas のアプリケーション統合の有効化
このセクションでは、Canvas のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Canvas のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![[Active Directory]](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![[アプリケーション]](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![[アプリケーションの追加]](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Canvas**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Application Gallery")
7. 結果ウィンドウで **[Canvas]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![キャンバス](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Canvas")
   
   ## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Canvas に対する認証を行えるようにする方法を説明します。  
Canvas のシングル サインオンを構成するには、証明書からサムプリント値を取得する必要があります。  
この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Canvas** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![Configure single sign-on](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configure single sign-on")
2. **[ユーザーの Canvas へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configure Single Sign-On")
3. **[アプリの URL の構成]** ページで、**[Canvas サインイン URL]** ボックスに、`https://<tenant-name>.instructure.com` のパターンで URL を入力し、**[次へ]** をクリックします。
   
   ![Configure App URL](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configure App URL")
4. **[Canvas でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、コンピューターで証明書ファイルをローカルに保存します。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configure Single Sign-On")
5. 別の Web ブラウザー ウィンドウで、Canvas 企業サイトに管理者としてログインします。
6. **[コース] \> [管理アカウント] \> [Microsoft]** の順にクリックします。
   
   ![キャンバス](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
7. 左側のナビゲーション ウィンドウで **[認証]** を選択し、**[新しい SAML 構成を追加]** をクリックします。
   
   ![認証](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentication")
8. [現在の統合] ページで、次の手順を実行します。
   
   ![現在の統合](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Current Integration")
   
   1. Azure クラシック ポータルで、**[Canvas でのシングル サインオンの構成]** ダイアログ ページの **[エンティティ ID]** の値をコピーし、**[IdP エンティティ ID]** ボックスに貼り付けます。
   2. Azure クラシック ポータルで、**[Canvas でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[ログオン URL]** ボックスに貼り付けます。
   3. Azure クラシック ポータルで、**[Canvas でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[ログアウト URL]** ボックスに貼り付けます。
   4. Azure クラシック ポータルで、**[Canvas でのシングル サインオンの構成]** ダイアログ ページの **[パスワード変更 URL]** の値をコピーし、**[パスワードの変更リンク]** ボックスに貼り付けます。
   5. エクスポートした証明書から **[拇印]** の値をコピーし、**[証明書の指紋]** ボックスに貼り付けます。  
      
      > [!TIP]
      > 詳細については、「 [How to retrieve a certificate's thumbprint value (証明書のサムプリント値を取得する方法)](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. **[ログイン属性]** の一覧で、**[NameID]** を選択します。
   7. **[識別子の形式]** の一覧で、**[emailAddress]** を選択します。
   8. **[認証設定の保存]**をクリックします。
9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Canvas にログインできるようにするには、ユーザーを Canvas にプロビジョニングする必要があります。  
Canvas の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。
1. **Canvas** テナントにログインします。
2. **[コース] \> [管理アカウント] \> [Microsoft]** の順にクリックします。
   
   ![キャンバス](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
3. **[ユーザー]**をクリックします。
   
   ![[ユーザー]](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Users")
4. **[新しいユーザーの追加]**をクリックします。
   
   ![[ユーザー]](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Users")
5. [新しいユーザーの追加] ダイアログ ページで、次の手順を実行します。
   
   ![[ユーザーの追加]](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Add User")
   
   1. **[フル ネーム]** ボックスに、ユーザーの名前を入力します。
   2. **[電子メール]** ボックスに、ユーザーの電子メール アドレスを入力します。
   3. **[ログイン]** ボックスに、ユーザーの Azure AD 電子メール アドレスを入力します。
   4. **[このアカウント作成についてユーザーに電子メールを送信]**を選択します。
   5. **[ユーザーの追加]**をクリックします。

> [!NOTE]
> Canvas から提供されている他の Canvas ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-canvas-perform-the-following-steps"></a>ユーザーを Canvas に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Canvas** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Assigning users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Nov16_HO3-->


