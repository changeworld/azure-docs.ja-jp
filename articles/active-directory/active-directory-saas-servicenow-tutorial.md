<properties 
    pageTitle="チュートリアル: Azure Active Directory と ServiceNow の統合 | Microsoft Azure" 
    description="Azure Active Directory で ServiceNow を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法を説明します。" 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#チュートリアル: Azure Active Directory と ServiceNow の統合
  
このチュートリアルでは、Azure と ServiceNow の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   ServiceNow のテナント
  
このチュートリアルを完了すると、ServiceNow に割り当てた Azure AD ユーザーは、ServiceNow 企業サイト (サービス プロバイダーが開始したサインオン) または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  ServiceNow のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-servicenow-tutorial/IC769496.png "シナリオ")
##ServiceNow のアプリケーション統合の有効化
  
このセクションでは、ServiceNow のアプリケーション統合を有効にする方法について説明します。

###ServiceNow のアプリケーション統合を有効にするには、次の手順に従います。

1.  Microsoft Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-servicenow-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-servicenow-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**ServiceNow**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-servicenow-tutorial/IC701016.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[ServiceNow]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで ServiceNow に対する認証を行えるようにする方法を説明します。

この手順の途中で、base-64 でエンコードされた証明書を Dropbox for Business テナントにアップロードする必要があります。この手順に慣れていない場合は、「[バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **ServiceNow** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749323.png "シングル サインオンの構成")

2.  **[ユーザーの ServiceNow へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749324.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[ServiceNow サインイン URL]** ボックスに、"*https://<InstanceName>.servicenow.com*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/IC769497.png "アプリケーション URL の構成")

4.  **[ServiceNow でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをローカルにコンピューターに保存して、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749325.png "シングル サインオンの構成")

5.  ServiceNow テナントで、左側にあるナビゲーション バーの **[プロパティ]** をクリックして、**[SAML 2.0 シングル サインオンのプロパティ]** ページを開きます。


6. **[SAML 2.0 シングル サインオンのプロパティ]** ページで、次の手順に従います。

     6\.1.**[外部認証を有効にする]** で **[はい]** を選択します。


     6\.2.**[ユーザー情報を使用して SAML2 セキュリティ トークンを発行する ID プロバイダー URL]** ボックスに、「**https://sts.windows.net/<テナントの GUID>/**」と入力します。


     6\.3.**[ID プロバイダーの AuthnRequest サービスのベース URL]** ボックスに、「**https://login.windows.net/<テナントの GUID>/saml2**」と入力します。


     6\.4.**[ID プロバイダーの SingleLogoutRequest サービスのベース URL]** ボックスに、「**https://login.windows.net/<テナントの GUID>/saml2**」と入力します。


     6\.5.**[ID プロバイダーの SingleLogoutRequest サービスのプロトコル バインド]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**」と入力します。

     6\.6.**[ログアウト リクエスト]** で **[はい]** をクリックします。

     6\.7.**[セッションが認証されなかったため SAML 2.0 シングル サインオンに失敗したとき、または最初のログイン時に、この URL にリダイレクト]** ボックスに、「**https://login.windows.net/<テナントの GUID>/saml2**」を参照してください。

  

7. **[サービス プロバイダー (Service-Now) のプロパティ]** セクションで、次の手順に従います。

     7\.1.**[Service-now インスタンス ホームページの URL]** ボックスに、ServiceNow インスタンス ホームページの URL を入力します。ServiceNow インスタンス ホームページの URL は、**ServiceNow テナント URL** と **/navpage.do**: **https://<InstanceName>.service-now.com/navpage.do** を連結したものです。<br><br> ![Service-now インスタンス ホームページ](./media/active-directory-saas-servicenow-tutorial/IC700342.png "Service-now インスタンス ホームページ")


     7\.2.**[エンティティ識別子、または発行者]** ボックスに、テナントの URL を入力します。

     7\.3.**[SAML2 トークンを受け入れる対象ユーザー URI]** ボックスに、テナントの URL を入力します。

     7\.4.**[SAMLResponse の件名の NameID 要素と照合するユーザー テーブル フィールド]** ボックスに、「**email**」と入力します。

     7\.5.**[SAMLResponse の件名の NameID 要素を返すときに使用する NameID ポリシー]** ボックスに、「**urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**」と入力します。

     7\.6 **[AuthnRequest ステートメントで AuthnContextClass 要求を作成する]** はオフのままにします。

     7\.7 **[ID プロバイダーへの SAML 2.0 AuthnRequest に含める AuthnContextClassRef メソッド]** ボックスに、「**http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**」と入力します。



8. **[詳細設定]** セクションで、次の手順に従います。

     8\.1.**[引き続き有効であると見なすための "notBefore" 制約より前、または "notOnOrAfter" 制約より後の長さ (秒単位)]** ボックスに、「**60**」と入力します。


9. 構成を保存するには、**[保存]** をクリックします。

10. 左側にあるナビゲーション バーで、**[証明書]** をクリックして **[証明書]** ページを開きます。



11. 証明書をアップグレードするには、[証明書] ページで、次の手順に従います。

     11\.1.**[新規]** をクリックします。

     11\.2.**[名前]** ボックスに、「**SAML 2.0**」と入力します。

     11\.3.**[アクティブ]** を選択します。

     11\.4.**[形式]** で **[PEM]** を選択します。

     11\.5.ダウンロードした証明書から base-64 でエンコードされたファイルを作成します。[AZURE.NOTE]詳細については、「[バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

     11\.6.**メモ帳**で、Base-64 エンコード ファイルを開き、このファイルの内容をクリップボードにコピーします。

     11\.7.**[PEM 証明書]** ボックスに、クリップボードの内容を貼り付けます。

     11\.8.**[Submit]** をクリックします。



12. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、[完了] をクリックして [シングル サインオンの構成] ダイアログを閉じます。<br><br> ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749326.png "シングル サインオンの構成")




##ユーザー プロビジョニングの構成
  
このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを ServiceNow に対して有効にする方法について説明します。


### ユーザー プロビジョニングを構成するには、次の手順に従います。

1. Microsoft Azure 管理ポータルの **ServiceNow** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックします。<br><br> ![ユーザー プロビジョニング](./media/active-directory-saas-servicenow-tutorial/IC769498.png "ユーザー プロビジョニング")


2. **[自動ユーザー プロビジョニングを有効にするための ServiceNow の資格情報を入力してください]** ページで、以下の構成設定を入力します： ユーザー プロビジョニングの構成

     2\.1.**[ServiceNow インスタンス名]** ボックスに、ServiceNow インスタンス名を入力します。

     2\.2.**[ServiceNow 管理ユーザー名]** ボックスに、ServiceNow 管理者アカウントの名前を入力します。

     2\.3.**[ServiceNow 管理パスワード]** ボックスに、このアカウントのパスワードを入力します。

     2\.4.**[検証]** をクリックして構成を確認します。

     2\.5.**[次へ]** をクリックして、**[次のステップ]** ページを開きます。

     2\.6.このアプリケーションのすべてのユーザーをプロビジョニングする場合は、[ディレクトリ内のすべてのユーザー アカウントをこのアプリケーションに自動的にプロビジョニングする] を選択します。<br><br> ![次のステップ](./media/active-directory-saas-servicenow-tutorial/IC698804.png "次のステップ")

     2\.7.**[次のステップ]** ページで、**[完了]** をクリックして構成を保存します。











##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを ServiceNow に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  ServiceNow アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-servicenow-tutorial/IC769499.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。

    ![あり](./media/active-directory-saas-servicenow-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!---HONumber=Nov15_HO1-->