<properties 
    pageTitle="チュートリアル: Azure Active Directory と Jive の統合 | Microsoft Azure" 
    description="Azure Active Directory で Jive を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#チュートリアル: Azure Active Directory と Jive の統合

  
このチュートリアルでは、Azure と Jive の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Jive のテナント
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Jive のアプリケーション統合の有効化
2.  ユーザー プロビジョニングの構成

##Jive のアプリケーション統合の有効化
  
このセクションでは、Jive のアプリケーション統合を有効にする方法について説明します。

###Jive のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-jive-tutorial/IC700994.png "アプリケーション")

4.  **アプリケーション ギャラリー**を開くには、**[アプリケーションの追加]**、**[組織で使用するアプリケーションを追加]** の順にクリックします。

    ![どの操作を行いますか。](./media/active-directory-saas-jive-tutorial/IC700995.png "どの操作を行いますか。")

5.  **検索ボックス**に、「**Jive**」と入力します。

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  結果ウィンドウで **[Jive]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##ユーザー プロビジョニングの構成
  
このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを Jive に対して有効にする方法を説明します。この手順の一環として、Jive.com からの要求に必要なユーザー セキュリティ トークンを指定する必要があります。
  
次のスクリーン ショットは、Azure AD の関連ダイアログの例を示しています。

![ユーザー プロビジョニングの構成](./media/active-directory-saas-jive-tutorial/IC698794.png "ユーザー プロビジョニングの構成")

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  Microsoft Azure 管理ポータルの **Jive** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックして **[ユーザー プロビジョニングの構成]** ダイアログを開きます。

2.  **[自動ユーザー プロビジョニングを有効にするための Jive の資格情報を入力してください]** ページで、以下の構成設定を入力します。

    1.  **[JIVE 管理ユーザー名]** テキストボックスに、Jive.com の**システム管理者**プロファイルが割り当てられている Jive アカウント名を入力します。

    2.  **[JIVE 管理パスワード]** テキストボックスに、このアカウントのパスワードを入力します。

    3.  **[JIVE テナント URL]** テキストボックスに、Jive テナント URL を入力します。

        >[AZURE.NOTE]Jive テナント URL は、組織が Jive にログインするために使用する URL です。通常、URL の形式は **www.<組織名>.jive.com** のようになります。

    4.  **[検証]** をクリックして構成を確認します。

    5.  **[次へ]** をクリックして、**[確認]** ページを開きます。

3.  **[確認]** ページで、チェックマークをクリックして構成を保存します。
  
ここで、テスト アカウントを作成して 10 分間待機し、アカウントが Jive.com と同期されていることを確認します。

<!---HONumber=AcomDC_0121_2016-->