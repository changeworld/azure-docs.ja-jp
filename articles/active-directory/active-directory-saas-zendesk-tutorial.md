<properties pageTitle="チュートリアル: Azure Active Directory と Zendesk の統合 | Microsoft Azure" description="Azure Active Directory で Zendesk を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#チュートリアル: Azure Active Directory と Zendesk の統合
>[AZURE.TIP]フィードバックを送るには、[ここ](http://go.microsoft.com/fwlink/?LinkId=522569)をクリックしてください。
  
このチュートリアルでは、Azure と Zendesk の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Zendesk テナント
  
このチュートリアルを完了すると、Zendesk に割り当てた Azure AD ユーザーは、Zendesk 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Zendesk のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-zendesk-tutorial/IC773083.png "シナリオ")

##Zendesk のアプリケーション統合の有効化
  
このセクションでは、Zendesk のアプリケーション統合を有効にする方法について説明します。

###Zendesk のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-zendesk-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-zendesk-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス**に、「**Zendesk**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-zendesk-tutorial/IC773084.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[Zendesk]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##シングル サインオンの構成
  
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Zendesk に対する認証を行うことができるようにする方法を説明します。Zendesk にシングル サインオンを構成するには、証明書からサムプリント値を取得する必要があります。この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書のサムプリント値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。

###シングル サインオンを構成するには、次の手順を実行します。

1.  Azure AD ポータルの **[Zendesk]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオン](./media/active-directory-saas-zendesk-tutorial/IC773086.png "シングル サインオン")

2.  **[ユーザーの Zendesk へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-zendesk-tutorial/IC773087.png "シングル サインオンの構成")

3.  **[アプリケーション URL の構成]** ページの **[Zendesk サインイン URL]** ボックスに、"*https://\<テナント名>.zendesk.com*" というパターンの URL を入力し、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-zendesk-tutorial/IC773088.png "アプリケーション URL の構成")

4.  **[Zendesk でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルを **c:\\zendesk.cer** としてローカルに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-zendesk-tutorial/IC777534.png "シングル サインオンの構成")

5.  別の Web ブラウザー ウィンドウで、Zendesk 企業サイトに管理者としてログインします。

6.  **[管理者]** をクリックします。

7.  左側のナビゲーション ウィンドウで、**[設定]**、**[セキュリティ]** の順にクリックします。

    ![セキュリティ](./media/active-directory-saas-zendesk-tutorial/IC773089.png "セキュリティ")

8.  **[セキュリティ**] ページで、**[管理者とエージェント]** タブを選択します。

9.  **[シングルサインオン (SSO) と SAML]** を選択し、**[SAML]** を選択します。

10. Azure ポータルで、**[Zendesk でのシングル サインオンの構成]** ダイアログ ページの **[SAML SSO URL]** の値をコピーし、**[SAML SSO URL]** テキストボックスに貼り付けます。

11. Azure ポータルで、**[Zendesk でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[リモート ログアウト URL]** テキストボックスに貼り付けます。

    ![シングル サインオン](./media/active-directory-saas-zendesk-tutorial/IC773090.png "シングル サインオン")

12. エクスポートした証明書から **[サムプリント]** の値をコピーし、**[証明書フィンガープリント]** テキストボックスに貼り付けます。

	>[AZURE.TIP]詳細については、「[How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI) (証明書のサムプリント値を取得する方法)」をご覧ください。

13. **[保存]** をクリックします。

14. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-zendesk-tutorial/IC773093.png "シングル サインオンの構成")

##ユーザー プロビジョニングの構成
  
Azure AD ユーザーが **Zendesk** にログインできるようにするには、ユーザーを **Zendesk** にプロビジョニングする必要があります。**Zendesk** の場合、プロビジョニングは手動で行います。

###ユーザー アカウントを Zendesk にプロビジョニングするには、次の手順に従います。

1.  **Zendesk** テナントにログインします。

2.  **[顧客リスト]** タブを選択します。

3.  **[ユーザー]** タブを選択し、**[追加]** をクリックします。

    ![ユーザーの追加](./media/active-directory-saas-zendesk-tutorial/IC773632.png "ユーザーの追加")

4.  プロビジョニングする既存の Azure AD アカウントの電子メール アドレスを入力し、**[追加]** をクリックします。

    ![新しいユーザー](./media/active-directory-saas-zendesk-tutorial/IC773633.png "新しいユーザー")

>[AZURE.NOTE]Zendesk から提供されている他の Zendesk ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーの割り当て
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを Zendesk に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Zendesk** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-zendesk-tutorial/IC773094.png "ユーザーの割り当て")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![あり](./media/active-directory-saas-zendesk-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)」をご覧ください。

<!---HONumber=Oct15_HO3-->