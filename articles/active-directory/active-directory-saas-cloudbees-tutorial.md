<properties pageTitle="チュートリアル: Azure Active Directory と CloudBees の統合 | Microsoft Azure" description="Azure Active Directory で CloudBees を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と CloudBees の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=528737)をクリックしてください。

このチュートリアルでは、Azure と CloudBees の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   CloudBees テナント

このチュートリアルを完了すると、CloudBees に割り当てた Azure AD ユーザーは、CloudBees 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/azure/dn308586.aspx)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  CloudBees のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-cloudbees-tutorial/IC790415.png "シナリオ")

##CloudBees のアプリケーション統合の有効化

このセクションでは、CloudBees のアプリケーション統合を有効にする方法について説明します。

###CloudBees のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-cloudbees-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-cloudbees-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-cloudbees-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-cloudbees-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**CloudBees**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-cloudbees-tutorial/IC790416.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[CloudBees]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Cloudbees](./media/active-directory-saas-cloudbees-tutorial/IC790417.png "Cloudbees")

##シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで CloudBees に対する認証を行うことができるようにする方法を説明します。CloudBees にシングル サインオンを構成するには、証明書から拇印の値を取得する必要があります。この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **CloudBees** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-cloudbees-tutorial/IC790418.png "Configure Single Sign-On")

2.  **[ユーザーの CloudBees へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-cloudbees-tutorial/IC790419.png "Configure Single Sign-On")

3.  **[アプリケーション URL の構成]** ページの **[CloudBees のサインオン URL]** ボックスに、ユーザーが CloudBees にサインインする際に使用する URL (例: **https://grandcentral.cloudbees.com/login*") を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-cloudbees-tutorial/IC790420.png "アプリケーション URL の構成")

4.  **[Google Apps でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをローカルでコンピューターに保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-cloudbees-tutorial/IC790421.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、CloudBees 企業サイトに管理者としてログインします。

6.  上部のメニューで **[設定]** をクリックします。

    ![設定](./media/active-directory-saas-cloudbees-tutorial/IC790422.png "設定")

7.  **[SSO 統合]** タブをクリックします。

8.  **[SAML 2.0 シングル サイン オンの設定]** セクションで、次の手順を実行します。

    ![シングル サインオンの設定](./media/active-directory-saas-cloudbees-tutorial/IC790423.png "シングル サインオンの設定")

    1.  Azure AD ポータルの **[CloudBees でのシングル サインオンの構成]** ページで、**[リモート ログイン URL]** 値をコピーして、**[リモート ログイン URL]** ボックスに貼り付けます。
    2.  エクスポートした証明書から **[拇印]** の値をコピーして、**[既存の証明書フィンガープリント]** ボックスに貼り付けます。
    
        >[AZURE.TIP]詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。

    3.  **[認証ドメイン]** ボックスに、会社のドメインを入力します。
    4.  **[保存]** をクリックします。

9.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![Configure Single Sign-On](./media/active-directory-saas-cloudbees-tutorial/IC790424.png "Configure Single Sign-On")

##ユーザー プロビジョニングの構成

Azure AD ユーザーが **CloudBees** にログインできるようにするには、そのユーザーを **CloudBees** にプロビジョニングする必要があります。**CloudBees** の場合、プロビジョニングは手動で行います。

###ユーザー アカウントを CloudBees にプロビジョニングするには、次の手順を実行します。

1.  **CloudBees** 企業サイトに管理者としてログインします。

2.  **[ユーザーの編集]** に移動します。

    ![ユーザー](./media/active-directory-saas-cloudbees-tutorial/IC790429.png "ユーザー")

3.  **[追加]** をクリックします。

    ![[追加] のいずれかを](./media/active-directory-saas-cloudbees-tutorial/IC790430.png "[追加] のいずれかを")

4.  **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/active-directory-saas-cloudbees-tutorial/IC790431.png "ユーザーの追加")

    1.  関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**電子メール**、**姓**、**名**、およびその他の属性を入力します。
    2.  **[ユーザーの追加]** をクリックします。

        >[AZURE.NOTE]ログインの手順が記載された電子メールが、アカウントの所有者に送信されます。

>[AZURE.NOTE]CloudBees から提供されている他の CloudBees ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを CloudBees に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **CloudBees** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-cloudbees-tutorial/IC790432.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![あり](./media/active-directory-saas-cloudbees-tutorial/IC767830.png "あり")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/azure/dn308586.aspx)」を参照してください。

<!---HONumber=August15_HO7-->