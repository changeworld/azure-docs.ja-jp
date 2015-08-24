<properties pageTitle="チュートリアル: Azure Active Directory と Citrix GoToMeeting の統合 | Microsoft Azure" description="Azure Active Directory で Citrix GoToMeeting を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Citrix GoToMeeting の統合  
適用対象: Azure

>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=522412)をクリックしてください。

このチュートリアルでは、Azure と Citrix GoToMeeting の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Citrix GoToMeeting のテナント

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Citrix GoToMeeting のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "構成")
##Citrix GoToMeeting のアプリケーション統合の有効化

このセクションでは、Citrix GoToMeeting のアプリケーション統合を有効にする方法を説明します。

###Citrix GoToMeeting のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Citrix GoToMeeting**」と入力します。

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  結果ウィンドウで **[Citrix GoToMeeting]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Citrix GoToMeeting に対する認証を行えるようにする方法を説明します。この手順の途中で、Base-64 でエンコードされた証明書を Citrix GoToMeeting テナントにアップロードする必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (バイナリ証明書をテキスト ファイルに変換する方法)」をご覧ください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **Citrix GoToMeeting** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの有効化](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "シングル サインオンの有効化")

2.  **[ユーザーの Citrix GoToMeeting へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択します。

    ![シングル サインオンの構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "シングル サインオンの構成")

3.  **[Citrix GoToMeeting でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "シングル サインオンの構成")

4.  別のブラウザーのウィンドウで、Citrix GoToMeeting テナントに管理者としてログインします。

5.  「[SAML 2.0 シングル サインオン (SSO) のセットアップ](https://login.citrixonline.com/saml/settings.html)」ページを開き、次の手順を実行します。

    ![SAML のセットアップ](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC689232.png "SAML のセットアップ")

    1.  **[手動で構成する]** を選択します。
    2.  Azure ポータルで、**[Citrix GoToMeeting でのシングル サインオンの構成]** ダイアログ ページの **[サインアウト ページの URL]** の値をコピーし、**[サインアウト ページの URL]** ボックスに貼り付けます。
    3.  Azure ポータルで、**[Citrix GoToMeeting でのシングル サインオンの構成]** ダイアログ ページの **[サインイン ページの URL]** の値をコピーし、**[サインイン ページの URL]** ボックスに貼り付けます。
    4.  ダウンロードした証明書から **Base-64 でエンコードされた**ファイルを作成します。  

        >[AZURE.TIP]詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    5.  **[証明書の置き換え]** をクリックし、**Base-64 でエンコードされた証明書ファイル**をアップロードします。
    6.  **[保存]** をクリックします。

6.  Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成

このセクションでは、Citrix GoToMeeting への Active Directory ユーザー アカウントのプロビジョニングを有効にする方法を説明します。

###ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  Microsoft Azure 管理ポータルの **Citrix GoToMeeting** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックして **[ユーザー プロビジョニングの構成]** ダイアログを開きます。

    ![Configure user provisioning](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configure user provisioning")

2.  **[Citrix GotoMeeting 管理ユーザー名]** ページで、Citrix GoToMeeting テナントに対する管理権限を持つ Citrix GoToMeeting アカウント名を入力し、**[次へ]** をクリックします。

    ![Configure user provisioning](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configure user provisioning")

3.  **[確認]** ページで、チェックマークをクリックして構成を保存します。

4.  **[検証]** ボタンをクリックして、構成を検証します。
##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、ユーザーにアプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Citrix GoToMeeting に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Citrix GoToMeeting** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確認します。

    ![あり](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "あり")

10 分間待機し、アカウントが Dropbox for Business に同期されていることを確認します。

最初の検証手順として、Azure 管理ポータルの **Citrix GoToMeeting** アプリケーション統合ページの D でダッシュボードをクリックして、プロビジョニングの状態を確認できます。

![ダッシュボード](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "ダッシュボード")

正常に完了したユーザー プロビジョニング サイクルは、関連する状態で示されます。

![統合状態](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "統合状態")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。

アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=August15_HO7-->