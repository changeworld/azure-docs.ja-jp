<properties 
    pageTitle="チュートリアル: Azure Active Directory と Work.com の統合 | Microsoft Azure" 
    description="Azure Active Directory で Work.com を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Work.com の統合
  
このチュートリアルでは、Azure と Work.com の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Work.com でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、Work.com に割り当てた Azure AD ユーザーは、Work.com 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Work.com のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-work-com-tutorial/IC794105.png "シナリオ")

##Work.com のアプリケーション統合の有効化
  
このセクションでは、Work.com のアプリケーション統合を有効にする方法について説明します。

###Work.com のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-work-com-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-work-com-tutorial/IC749321.png "アプリケーションの追加")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-work-com-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Work.com**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-work-com-tutorial/IC794106.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Work.com]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Work.com に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、証明書を Work.com.com にアップロードする必要があります。

>[AZURE.NOTE]シングル サインオンを構成する場合でも、カスタムの Work.com ドメイン名をセットアップする必要があります。少なくともドメイン名を定義し、ドメイン名をテストしてから、組織全体にデプロイする必要があります。

###シングル サインオンを構成するには、次の手順に従います。

1.  管理者として Work.com テナントにログインします。

2.  **[セットアップ]** に移動します。

    ![セットアップ](./media/active-directory-saas-work-com-tutorial/IC794108.png "セットアップ")

3.  左側のナビゲーション ウィンドウの **[管理]** セクションで、**[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックして**[マイ ドメイン]** ページを開きます。

    ![マイ ドメイン](./media/active-directory-saas-work-com-tutorial/IC767825.png "マイ ドメイン")

4.  ドメインが正しく設定されていることを確認するには、“**ステップ 4 ユーザーへのデプロイ**” で “**マイ ドメインの設定**” を確認します。

    ![ユーザーにデプロイされたドメイン](./media/active-directory-saas-work-com-tutorial/IC784377.png "ユーザーにデプロイされたドメイン")

5.  別の Web ブラウザー ウィンドウで、Azure クラシック ポータルにログインします。

6.  **Work.com** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configure Single Sign-On")

7.  **[ユーザーの Work.com へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configure Single Sign-On")

8.  **[アプリケーション URL の構成]** ページの **[Work.com サインオン URL]** テキストボックスに、ユーザーが Work.com アプリケーションにサインオンするときに使用する URL (例: ”http://company.my.salesforce.com*”) を入力し、[次へ] をクリックします。

    ![Configure App URL](./media/active-directory-saas-work-com-tutorial/IC794111.png "アプリケーション URL の構成")

9.  **[Work.com でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、コンピューターで証明書ファイルをローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configure Single Sign-On")

10. Work.com テナントにログインします。

11. **[セットアップ]** に移動します。

    ![セットアップ](./media/active-directory-saas-work-com-tutorial/IC794108.png "セットアップ")

12. **[セキュリティ制御]** メニューを展開表示し、**[シングル サインオンの設定]** をクリックします。

    ![シングル サインオンの設定](./media/active-directory-saas-work-com-tutorial/IC794113.png "シングル サインオンの設定")

13. **[シングル サインオンの設定]** ダイアログ ページで、次の手順を実行します。

    ![SAML 有効](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML 有効")

    1.  **[SAML Enabled]** を選択します。
    2.  **[新規]** をクリックします。

14. **[SAML シングル サインオンの設定]** セクションで、次の手順に従います。

    ![SAML シングル サインオンの設定](./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML シングル サインオンの設定")

    1.  **[名前]** テキスト ボックスに、構成の名前を入力します。  

        >[AZURE.NOTE]**[名前]** の値を指定すると、**[API 名]** ボックスが自動的に入力されます。

    2.  Azure クラシック ポータルの **[Work.com でのシングル サインオンの構成]** ダイアログ ページで、**[発行者の URL]** の値をコピーし、**[発行者]** ボックスに貼り付けます。
    3.  **[参照]** をクリックして、ダウンロードした証明書をアップロードします。
    4.  **[エンティティ ID]** ボックスに、「**https://salesforce-work.com**」と入力します。
    5.  **[SAML ID の種類]** として、**[アサーションにはユーザー オブジェクトからのフェデレーション ID が含まれます]** を選択します。
    6.  **[SAML ID の場所]** として、**[ID を Subject ステートメントの NameIdentifier 要素にする]** チェックボックスをオンにします。
    7.  Azure クラシック ポータルで、**[Work.com でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[ID プロバイダー ログイン URL]** ボックスに貼り付けます。
    8.  Azure クラシック ポータルで、**[Work.com でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[ID プロバイダー ログアウト URL]** ボックスに貼り付けます。
    9.  **[サービス プロバイダーが開始した要求のバインド]** で **[HTTP POST]** を選択します。
    10. [**Save**] をクリックします。

15. Work.com クラシック ポータルで、左側のナビゲーション ウィンドウの **[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックして **[マイ ドメイン]** ページを開きます。

    ![マイ ドメイン](./media/active-directory-saas-work-com-tutorial/IC794115.png "マイ ドメイン")

16. **[マイ ドメイン]** ページの **[Login Page Branding (ログイン ページのブランド)**] をクリックして、**[編集]** をクリックします。

    ![ログイン ページのブランド](./media/active-directory-saas-work-com-tutorial/IC767826.png "ログイン ページのブランド")

17. **[Login Page Branding (ログイン ページのブランド)]** ページの **[認証サービス]** セクションに、**[SAML SSO 設定]** の名前が表示されます。それを選んで、**[保存]** をクリックします。

    ![ログイン ページのブランド](./media/active-directory-saas-work-com-tutorial/IC784366.png "ログイン ページのブランド")

18. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configure Single Sign-On")

##ユーザー プロビジョニングの構成
  
Azure Active Directory ユーザーがサインインできるように、Azure Active Directory ユーザーを Work.com に対してプロビジョニングする必要があります。  
Work.com の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  Work.com 企業サイトに管理者としてサインオンします。

2.  **[セットアップ]** に移動します。

    ![セットアップ](./media/active-directory-saas-work-com-tutorial/IC794108.png "セットアップ")

3.  **[ユーザーの管理] の [ユーザー]** に移動します。

    ![Manage Users](./media/active-directory-saas-work-com-tutorial/IC784369.png "ユーザーの管理")

4.  **[新しいユーザー]** をクリックします。

    ![すべてのユーザー](./media/active-directory-saas-work-com-tutorial/IC794117.png "すべてのユーザー")

5.  [ユーザーの編集] セクションで、次の手順を実行します。

    ![ユーザーの編集](./media/active-directory-saas-work-com-tutorial/IC794118.png "ユーザーの編集")

    1.  関連するテキストボックスにプロビジョニングする有効な Azure Active Directory アカウントの **[姓]**、**[エイリアス]**、**[電子メール]**、**[ユーザー名]**、**[ニックネーム]** の属性を入力します。
    2.  **[ロール]**、**[ユーザー ライセンス]**、**[プロファイル]** を選択します。
    3.  [**Save**] をクリックします。

        >[AZURE.NOTE]Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE]Work.com から提供されている他の Work.com ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Work.com に割り当てるには、次の手順を実行します。

1.  Azure クラシック ポータルで、テスト アカウントを作成します。

2.  Work.com アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-work-com-tutorial/IC794119.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![Yes](./media/active-directory-saas-work-com-tutorial/IC767830.png "Yes")
  
ここで 10 分間待機し、アカウントが Work.com に同期されたことを確認します。
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。

<!---HONumber=AcomDC_0914_2016-->