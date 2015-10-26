<properties pageTitle="チュートリアル: Azure Active Directory と Box の統合 | Microsoft Azure" description="Azure Active Directory で Box を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />




#チュートリアル: Azure Active Directory と Box の統合


>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=522410)をクリックしてください。
  
このチュートリアルでは、Azure と Box の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Box のテスト テナント
  
このチュートリアルを完了すると、Box に割り当てた Azure AD ユーザーは、Box 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Box のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-box-tutorial/IC769537.png "シナリオ")



##Box のアプリケーション統合の有効化
  
このセクションでは、Box のアプリケーション統合を有効にする方法について説明します。

###Box のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-box-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-box-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-box-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索**ボックスに、「**Box**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-box-tutorial/IC701023.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Box]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Box に対する認証を行えるようにする方法を説明します。<br>この手順の途中で、メタデータを Box.com にアップロードする必要があります。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **Box** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-box-tutorial/IC769538.png "シングル サインオンの構成")

2.  **[ユーザーの Box へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-box-tutorial/IC769539.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページで、**[Box テナント URL]** ボックスに、Box テナント URL (例: https://<mydomainname>.box.com) を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-box-tutorial/IC669826.png "アプリケーション URL の構成")

4.  **[Box でのシングル サインオンの構成]** ページで、メタデータをダウンロードするために、**[メタデータのダウンロード]** をクリックし、データ ファイルをコンピューターのローカルに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-box-tutorial/IC669824.png "シングル サインオンの構成")

5.  メタデータ ファイルを Box サポート チームに転送します。サポート チームは、シングル サインオンを構成する必要があります。

6.  シングル サインオンの構成確認を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-box-tutorial/IC769540.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
このセクションでは、Box への Active Directory ユーザー アカウントのプロビジョニングを有効にする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1. Microsoft Azure 管理ポータルの **Box** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックして **[ユーザー プロビジョニングの構成]** ダイアログを開きます。<br> <br> ![自動ユーザー プロビジョニングの有効化](./media/active-directory-saas-box-tutorial/IC769541.png "自動ユーザー プロビジョニングの有効化")

2. **[Box へのユーザー プロビジョニングを有効にする]** ダイアログ ページで、**[ユーザー プロビジョニングを有効にする]** をクリックします。<br><br> ![自動ユーザー プロビジョニングの有効化](./media/active-directory-saas-box-tutorial/IC769544.png "自動ユーザー プロビジョニングの有効化")

3. **[Box へのアクセスを許可するにはログインしてください]** ページで、必要な資格情報を入力し、**[認証する]** をクリックします。<br><br> ![自動ユーザー プロビジョニングの有効化](./media/active-directory-saas-box-tutorial/IC769546.png "自動ユーザー プロビジョニングの有効化")


4. **[Box のアクセス許可]** をクリックしてこの操作を承認し、Azure 管理ポータルに戻ります。<br><br> ![自動ユーザー プロビジョニングの有効化](./media/active-directory-saas-box-tutorial/IC769549.png "自動ユーザー プロビジョニングの有効化")

5. 構成を終了するには、[完了] をクリックします。<br><br> ![自動ユーザー プロビジョニングの有効化](./media/active-directory-saas-box-tutorial/IC769551.png "自動ユーザー プロビジョニングの有効化")



##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Box に割り当てるには、次の手順を実行します。

1. Azure AD ポータルで、テスト アカウントを作成します。

2. **Box** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。<br><br> ![ユーザーの割り当て](./media/active-directory-saas-box-tutorial/IC769552.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確認します。<br><br> ![あり](./media/active-directory-saas-box-tutorial/IC767830.png "あり")
  

10 分間待機し、アカウントが Box に同期されたことを確認します。

最初の検証手順として、Azure 管理ポータルの Box アプリケーション統合ページの D でダッシュボードをクリックして、プロビジョニングの状態を確認できます。

<br><br> ![ダッシュボード](./media/active-directory-saas-box-tutorial/IC769553.png "ダッシュボード")

正常に完了したユーザー プロビジョニング サイクルは、関連する状態で示されます。

<br><br> ![統合状態](./media/active-directory-saas-box-tutorial/IC769555.png "統合状態")


Box テナントでは、同期済みのユーザーは、**[管理コンソール]** の **[管理対象のユーザー]** に表示されます。

<br><br> ![統合状態](./media/active-directory-saas-box-tutorial/IC769556.png "統合状態")


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!---HONumber=Oct15_HO3-->