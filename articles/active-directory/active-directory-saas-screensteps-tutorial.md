---
title: "チュートリアル: Azure Active Directory と ScreenSteps の統合 | Microsoft Docs"
description: "Azure Active Directory で ScreenSteps を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7fbb8874367bda9be618332947ffa2c9942fe4ec
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>チュートリアル: Azure Active Directory と ScreenSteps の統合
このチュートリアルの目的は、Azure と ScreenSteps の統合を示すことです。
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* ScreenSteps テナント

このチュートリアルを終了すると、ScreenSteps に割り当てられた Azure AD ユーザーは、ScreenSteps 企業サイト (サービス プロバイダーが開始したサインオン) でシングル サインオン (SSO) を使用するか、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにサインインできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. ScreenSteps のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成 
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scenario")

## <a name="enable-the-application-integration-for-screensteps"></a>ScreenSteps のアプリケーション統合の有効化
このセクションでは、ScreenSteps のアプリケーション統合を有効にする方法を説明します。

**ScreenSteps のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**ScreenSteps**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Application Gallery")
7. 結果ウィンドウで **[ScreenSteps]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ScreenSteps で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **ScreenSteps** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configure single sign-on")
2. **[ユーザーの ScreenSteps へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configure single sign-on")

3. 別の Web ブラウザー ウィンドウで、ScreenSteps 企業サイトに管理者としてログインします。

4. **[Account Management]**をクリックします。

    ![Account management](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")

5. **[Single Sign-on]**をクリックします。

    ![Remote authentication](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Remote authentication")

6. **[シングル サインオン エンドポイントの作成]** をクリックします。

    ![Remote authentication](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Remote authentication")

7. **[シングル サインオン エンドポイントの作成]** セクションで、次の手順に従います。

    ![Create an authentication endpoint](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Create an authentication endpoint")

    1. **[Title]** テキスト ボックスに、タイトルを入力します。
    2. **[Mode]** 一覧から **[SAML]** を選択します。
    3. **[作成]**をクリックします。

8. 新しいエンドポイントを編集します。

    ![エンドポイントの編集](./media/active-directory-saas-screensteps-tutorial/IC778528.png "Edit endpoint")

9. **[シングル サインオン エンドポイントの編集]** セクションで、次の手順に従います。

    ![Remote authentication endpoint](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote authentication endpoint")

    1. **SAML コンシューマー URL** をクリップボードにコピーします。

10. Azure クラシック ポータルの **[アプリケーション URL の構成]** ページで、**[ScreenSteps サインイン URL]** テキストボックスに **SAML コンシューマー URL** を貼り付けて、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configure app URL")

11. **[ScreenSteps でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configure single sign-on")


12. **[シングル サインオン エンドポイントの編集]** セクションに戻り、次の手順に従います。

    ![Remote authentication endpoint](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote authentication endpoint")

    1. **[新しい SAML 証明書ファイルのアップロード]** をクリックし、ダウンロードした証明書をアップロードします。
    2. Azure クラシック ポータルの **[ScreenSteps でのシングル サインオンの構成]** ページで、**[リモート ログイン URL]** の値をコピーし、**[Remote Login URL]** ボックスに貼り付けます。
    3. Azure クラシック ポータルの **[ScreenSteps でのシングル サインオンの構成]** ページで、**[リモート ログアウト URL]** の値をコピーし、**[Remote Logout URL]** ボックスに貼り付けます。
    4. **[グループ]** を選択し、プロビジョニングされたときにユーザーを割り当てます。
    5. **[Update]**をクリックします。
    6. **[シングル サインオン エンドポイントの編集]** に戻ります。
    7. ScreenSteps にログインするすべてのユーザーについて、このエンドポイントを使用するには、**[アカウントの既定値にする]** ボタンをクリックします。 または、**[サイトに追加]** ボタンをクリックして、**ScreenSteps** 内の特定のサイトにこのエンドポイントを使用することもできます。


12. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成



## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを ScreenSteps に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **ScreenSteps** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。


SSO の設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


