<properties pageTitle="チュートリアル: Azure Active Directory と IdeaScale の統合 | Microsoft Azure" description="Azure Active Directory で IdeaScale を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と IdeaScale の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=529830)をクリックしてください。
  
このチュートリアルでは、Azure と IdeaScale の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   IdeaScale でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを完了すると、IdeaScale に割り当てた Azure AD ユーザーは、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  IdeaScale のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-ideascale-tutorial/IC790838.png "シナリオ")
##IdeaScale のアプリケーション統合の有効化
  
このセクションでは、IdeaScale のアプリケーション統合を有効にする方法を説明します。

###IdeaScale のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-ideascale-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-ideascale-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に「**IdeaScale**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-ideascale-tutorial/IC790841.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[IdeaScale]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで IdeaScale に対する認証を行うことができるようにする方法を説明します。IdeaScale のシングル サインオンを構成するには、証明書からサムプリント値を取得する必要があります。この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **[IdeaScale]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configure Single Sign-On")

2.  **[ユーザーの IdeaScale へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページの **[IdeaScale のサインオン URL]** テキストボックスに、ユーザーが IdeaScale アプリケーションにサインオンするときに使用する URL (例: **https://company.IdeaScale.com*")) を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-ideascale-tutorial/IC790845.png "アプリケーション URL の構成")

4.  **[IdeaScale でのシングル サインオンの構成]** ページで、メタデータをダウンロードするために、**[メタデータのダウンロード]** をクリックし、コンピューターでメタデータ ファイルをローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configure Single Sign-On")

5.  別の Web ブラウザーのウィンドウで、IdeaScale 企業サイトに管理者としてログインします。

6.  **[コミュニティの設定]** に移動します。

    ![コミュニティの設定](./media/active-directory-saas-ideascale-tutorial/IC790847.png "コミュニティの設定")

7.  **[セキュリティ]、[シングル サインオン設定]** の順にクリックします。

    ![シングル サインオンの設定](./media/active-directory-saas-ideascale-tutorial/IC790848.png "シングル サインオンの設定")

8.  **[シングル サインオンのタイプ]** で **[SAML 2.0]** を選択します。

    ![シングル サインオンのタイプ](./media/active-directory-saas-ideascale-tutorial/IC790849.png "シングル サインオンのタイプ")

9.  **[シングル サインオンの設定]** ダイアログで、次の手順を実行します。

    ![シングル サインオンの設定](./media/active-directory-saas-ideascale-tutorial/IC790850.png "シングル サインオンの設定")

    1.  Azure ポータルで、**[IdeaScale でのシングル サインオンの構成]** ダイアログ ページの **[エンティティ ID]** の値をコピーし、**[SAML IdP エンティティ ID]** テキストボックスに貼り付けます。
    2.  ダウンロードしたメタデータ ファイルの内容をコピーし、**[SAML IdP メタデータ]** テキストボックスに貼り付けます。
    3.  Azure ポータルで、**[IdeaScale でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[ログアウト成功 URL]** テキストボックスに貼り付けます。
    4.  **[変更を保存]** をクリックします。

10. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが IdeaScale にログインできるようにするには、そのユーザーを IdeaScale にプロビジョニングする必要があります。IdeaScale の場合、プロビジョニングは手動で行います。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **IdeaScale** 企業サイトに管理者としてログインします。

2.  **[コミュニティの設定]** に移動します。

    ![コミュニティの設定](./media/active-directory-saas-ideascale-tutorial/IC790847.png "コミュニティの設定")

3.  **[基本設定]、[メンバ管理]** の順にクリックします。

4.  **[メンバーの追加]** をクリックします。

    ![メンバ管理](./media/active-directory-saas-ideascale-tutorial/IC790852.png "メンバ管理")

5.  [新しいメンバーの追加] セクションで、次の手順を実行します。

    ![新しいメンバーの追加](./media/active-directory-saas-ideascale-tutorial/IC790853.png "新しいメンバーの追加")

    1.  **[電子メール アドレス]** テキストボックスに、プロビジョニングする有効な AAD アカウントの電子メール アドレスを入力します。
    2.  **[変更を保存]** をクリックします。

    >[AZURE.NOTE]Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

>[AZURE.NOTE]IdeaScale から提供されている他の IdeaScale ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを IdeaScale に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **IdeaScale ** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-ideascale-tutorial/IC790854.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]**の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-ideascale-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=Oct15_HO3-->