<properties 
    pageTitle="チュートリアル: Azure Active Directory と TOPdesk - Secure の統合 | Microsoft Azure" description="Azure Active Directory で TOPdesk - Secure を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#チュートリアル: Azure Active Directory と TOPdesk - Secure の統合
  
このチュートリアルでは、Azure と TOPdesk - Secure の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   TOPdesk - Secure でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、TOPdesk - Secure に割り当てた Azure AD ユーザーは、TOPdesk - Secure 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  TOPdesk - Secure のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "シナリオ")

##TOPdesk - Secure のアプリケーション統合の有効化
  
このセクションでは、TOPdesk - Secure のアプリケーション統合を有効にする方法を説明します。

###TOPdesk - Secure のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**TOPdesk - Secure**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[TOPdesk - Secure]** を選び、**[完了]** をクリックしてアプリを追加します。

    ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで TOPdesk - Secure に対する認証を行うことができるようにする方法を説明します。TOPdesk - Secure のシングル サインオンを構成するには、ロゴのアイコン ファイルをアップロードする必要があります。アイコン ファイルを取得するには、TOPdesk サポート チームにお問い合わせください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  **TOPdesk - Secure** 企業サイトに管理者としてサインオンします。

2.  **[TOPdesk]** メニューで **[Settings]** をクリックします。

    ![設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "設定")

3.  **[Login Settings]** をクリックします。

    ![ログイン設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "ログイン設定")

4.  **[Login Settings]** メニューを展開し、**[General]** をクリックします。

    ![全般](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "全般")

5.  **[SAML login]** 構成セクションの **[Secure]** セクションで、次の手順に従います。

    ![技術設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "技術設定")

    1.  **[Download]** をクリックしてパブリック メタデータ ファイルをダウンロードし、コンピューターにローカルに保存します。
    2.  メタデータ ファイルを開いて、**AssertionConsumerService** ノードを探します。![Assertion Consumer Service](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Assertion Consumer Service")
    3.  **AssertionConsumerService** の値をコピーします。  

        >[AZURE.NOTE]この値は、このチュートリアルの以降の「**アプリケーション URL の構成**」セクションで必要になります。

6.  別の Web ブラウザーのウィンドウで、**Azure Active Directory** ポータルに管理者としてログインします。

7.  **TOPdesk - Secure** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configure Single Sign-On")

8.  **[ユーザーの TOPdesk - Secure へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configure Single Sign-On")

9.  **[アプリケーション URL の構成]** ページで、次の手順を実行します。

    ![アプリケーション URL の構成](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "アプリケーション URL の構成")

    1.  **[TOPdesk - Secure サインオン URL]** テキストボックスに、ユーザーが TOPdesk - Secure アプリケーションへのサインオンに使用する URL (例: **https://qssolutions.topdesk.net*")) を入力します。
2.  **[TOPdesk - Secure 応答 URL]** テキストボックスに、**TOPdesk - Secure AssertionConsumerService URL** (例: **https://qssolutions.topdesk.net/tas/public/login/saml*") を貼り付けます。
3.  **[次へ]** をクリックします。

10. **[TOPdesk - Secure でのシングル サインオンの構成]** ページで、メタデータ ファイルをダウンロードするには、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターのローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configure Single Sign-On")

11. 証明書ファイルを作成するには、次の手順を実行します。

    ![証明書](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "証明書")

    1.  ダウンロードしたメタデータ ファイルを開きます。
    2.  **fed:ApplicationServiceType** の **xsi:type** を持つ **RoleDescriptor** ノードを展開します。
    3.  **X509Certificate** ノードの値をコピーします。
    4.  コピーした **X509Certificate** の値をコンピューター上のファイル内にローカルに保存します。

12. TOPdesk - Secure 企業サイトの **[TOPdesk]** メニューで、**[Settings]** をクリックします。

    ![設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "設定")

13. **[Login Settings]** をクリックします。

    ![ログイン設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "ログイン設定")

14. **[Login Settings]** メニューを展開し、**[General]** をクリックします。

    ![全般](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "全般")

15. **[Public]** セクションで、**[Add]** をクリックします。

    ![[追加] のいずれかを](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "[追加] のいずれかを")

16. **[SAML configuration assistant]** ダイアログ ページで、次の手順を実行します。

    ![SAML Configuration Assistant](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "SAML Configuration Assistant")

    1.  ダウンロードしたメタデータ ファイルをアップロードするには、**[Federation Metadata]** で **[Browse]** をクリックします。
    2.  証明書ファイルをアップロードするには、**[Certificate (RSA)]** で **[Browse]** をクリックします。
    3.  TOPdesk サポート チームから入手したロゴのファイルをアップロードするには、**[Logo icon]** の下の **[Browse]** をクリックします。
    4.  **[User name attribute]** テキストボックスに、「****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**」と入力します。
5.  **[Display name]** テキスト ボックスに、構成の名前を入力します。
    6.  **[保存]** をクリックします。

17. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configure Single Sign-On")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが TOPdesk - Secure にログインできるようにするには、そのユーザーを TOPdesk - Secure にプロビジョニングする必要があります。TOPdesk - Secure の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **TOPdesk - Secure** 企業サイトに管理者としてサインオンします。

2.  上部のメニューで、**[TOPdesk]、[New]、[Support Files]、[Operator]** の順にクリックします。

    ![演算子](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "演算子")

3.  **[New Operator]** ダイアログで、次の手順を実行します。

    ![新しい演算子](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "新しい演算子")

    1.  [General] タブをクリックします。
    2.  **[General]** セクションの **[Surname]** テキストボックスに、プロビジョニングする有効な Azure Active Directory アカウントの姓を入力します。
    3.  **[Location]** セクションでアカウントの **[Site]** を選択します。
    4.  **[TOPdesk Login]** セクションの **[Login Name]** テキストボックスに、ユーザーのログイン名を入力します。
    5.  **[保存]** をクリックします。

>[AZURE.NOTE]他の TOPdesk - Secure ユーザー アカウントの作成ツールまたは TOPdesk - Secure から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを TOPdesk - Secure に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **TOPdesk - Secure** アプリ統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Yes")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0114_2016-->