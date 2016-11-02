<properties 
    pageTitle="チュートリアル: Azure Active Directory と Egnyte の統合 | Microsoft Azure" 
    description="Azure Active Directory で Egnyte を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Egnyte の統合
  
このチュートリアルでは、Azure と Egnyte の統合について説明します。
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Egnyte でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、Egnyte に割り当てた Azure AD ユーザーは、Egnyte 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Egnyte のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-egnyte-tutorial/IC787812.png "シナリオ")
##Egnyte のアプリケーション統合の有効化
  
このセクションでは、Egnyte のアプリケーション統合を有効にする方法について説明します。

###Egnyte のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-egnyte-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-egnyte-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-egnyte-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Egnyte**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-egnyte-tutorial/IC787813.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Egnyte]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Egnyte に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  
この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure クラシック ポータルの **Egnyte** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configure Single Sign-On")

2.  **[ユーザーの Egnyte へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページの **[Egnyte サインイン URL]** ボックスに、"*https://company.egnyte.com*" というパターンの URL を入力し、**[次へ]* をクリックします。

    ![Configure App URL](./media/active-directory-saas-egnyte-tutorial/IC787817.png "アプリケーション URL の構成")

4.  **[Egnyte でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、Egnyte 企業サイトに管理者としてログインします。

6.  **[設定]** をクリックします

    ![Settings](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Settings")

7.  メニューで **[設定]** をクリックします。

    ![Settings](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Settings")

8.  **[構成]** タブをクリックし、**[セキュリティ]** をクリックします。

    ![Security](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Security")

9.  **[シングル サインオン認証]** セクションで、次の手順を実行します。

    ![シングル サインオン認証](./media/active-directory-saas-egnyte-tutorial/IC787822.png "シングル サインオン認証")

    1.  **[シングル サインオン認証]** として **[SAML 2.0]** を選択します。
    2.  **[ID プロバイダー]** として **[AzureAD]** を選択します。
    3.  Azure クラシック ポータルで、**[Egnyte でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[ID プロバイダー ログイン URL]** ボックスに貼り付けます。
    4.  Azure クラシック ポータルで、**[Egnyte でのシングル サインオンの構成]** ダイアログ ページの **[エンティティ ID]** の値をコピーし、**[ID プロバイダー エンティティ ID]** ボックスに貼り付けます。
    5.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。

        >[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」を参照してください。

    6.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[ID プロバイダー証明書]** ボックスに貼り付けます。
    7.  **[既定のユーザー マッピング]** として **[電子メール アドレス]** を選択します。
    8.  **[ドメイン固有の発行者の値を使用]** として **[無効]** を選択します。
    9.  **[保存]** をクリックします。

10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Egnyte にログインできるようにするには、そのユーザーを Egnyte にプロビジョニングする必要があります。Egnyte の場合、プロビジョニングは手動で行います。

###ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **Egnyte** 企業サイトに管理者としてログインします。

2.  **[設定]、[ユーザーとグループ]** の順にクリックします。

3.  **[新しいユーザーの追加]** をクリックし、追加するユーザーの種類を選択します。

    ![Users](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Users")

4.  **[新しい標準ユーザー]** セクションで、次の手順を実行します。

    ![新しい標準ユーザー](./media/active-directory-saas-egnyte-tutorial/IC787825.png "新しい標準ユーザー")

    1.  プロビジョニングする有効な Azure Active Directory アカウントの、**電子メール**、**ユーザー名**などの詳細を入力します。
    2.  **[保存]** をクリックします。

    >[AZURE.NOTE] Azure Active Directory アカウント保有者に通知メールが届きます。

>[AZURE.NOTE] Egnyte から提供されている他の Egnyte ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Egnyte に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  **Egnyte** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-egnyte-tutorial/IC787826.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。

<!---HONumber=AcomDC_0713_2016-->