<properties pageTitle="チュートリアル: Azure Active Directory と Learningpool の統合 | Microsoft Azure" description="Azure Active Directory で Learningpool を使用してシングル サインオンや自動化されたプロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Learningpool の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=533920)をクリックしてください。
  
このチュートリアルの目的は、Azure と Learningpool の統合を示すことです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Learningpool でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを終了すると、Learningpool に割り当てた Azure AD ユーザーは、Learningpool の企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Learningpool のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーを割り当てる

![シナリオ](./media/active-directory-saas-learningpool-tutorial/IC791166.png "シナリオ")
##Learningpool のアプリケーション統合の有効化
  
このセクションでは、Learningpool のアプリケーション統合を有効にする方法を説明します。

###Learningpool のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-learningpool-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-learningpool-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に「**Learningpool**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-learningpool-tutorial/IC795073.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Learningpool]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Learningpool に対する認証を行えるようにする方法を説明します。
  
Learningpool アプリケーションでは、特定の形式の SAML アサーションを使用されます。このため、カスタム属性マッピングを **SAML トークン属性**の構成に追加する必要があります。次のスクリーンショットはその例です。

![SAML トークンの属性](./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML トークンの属性")

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **Learningpool** アプリケーション統合ページの一番上のメニューで **[属性]** をクリックして、**[SAML トークンの属性]** ダイアログを開きます。

    ![属性](./media/active-directory-saas-learningpool-tutorial/IC795075.png "属性")

2.  必要な属性のマッピングを追加するには、次の手順に従います。

    ###

    |属性名 |属性値 |
	|------------------------------|---------------------------|

     urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname
	|-------------------------------|--------------------------|  
	 urn:oid:2.5.4.42|User.givenname   
    |urn:oid:0.9.2342.19200300.100.1.3|User.mail
    |urn:oid:2.5.4.4|User.surname

    1.  上記のテーブルの各データ行で、**[ユーザー属性の追加]** をクリックします。
    2.  **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    3.  **[属性値]** リストから、その行に対して表示される属性値を選択します。
    4.  **[完了]** をクリックします。

3.  **[変更の適用]** をクリックします。

4.  ブラウザーで、**[戻る]** をクリックして、**[クイック スタート]** ダイアログをもう一度開きます。

5.  **[シングル サインオンの構成]** ダイアログ ボックスを開くには、 **[シングル サインオンの構成]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-learningpool-tutorial/IC795076.png "シングル サインオンの構成")

6.  **[ユーザーの Learningpool へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configure Single Sign-On")

7.  **[アプリ URL の構成]** ページの **[Learningpool のサインオン URL]** ボックスに、ユーザーが Learningpool アプリケーションにサインオンする際に使用する URL (例: *https://parliament.preview.learningpool.com/auth/shibboleth/index.php)) を入力し、**[次へ]** をクリックします。

    ![アプリ URL の構成](./media/active-directory-saas-learningpool-tutorial/IC795078.png "アプリ URL の構成")

8.  **[Learningpool でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、証明書ファイルをコンピューターのローカルに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configure Single Sign-On")

9.  メタデータ ファイルを Learningpool サポート チームに転送します。

    >[AZURE.NOTE]シングル サインオンは、Learningpool サポート チームが有効にする必要があります。

10. Azure AD ポータルで、[シングル サインオンの構成確認] を選び、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configure Single Sign-On")
##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが Learningpool にログインできるようにするには、ユーザーを Learningpool にプロビジョニングする必要があります。
  
Learningpool へのユーザー プロビジョニングの構成にあたって必要な操作はありません。ユーザーは、Learningpool サポート チームが作成する必要があります。

>[AZURE.NOTE]Learningpool から提供されている他の Learningpool ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Learningpool に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Learningpool** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-learningpool-tutorial/IC795081.png "ユーザーの割り当て")

3.  テスト ユーザーを選び、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

    ![あり](./media/active-directory-saas-learningpool-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=August15_HO7-->