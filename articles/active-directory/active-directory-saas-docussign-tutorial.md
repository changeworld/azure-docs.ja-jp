<properties
	pageTitle="チュートリアル: Azure Active Directory と DocuSign の統合 | Microsoft Azure"
	description="Azure Active Directory と DocuSign の間でシングル サインオンを構成する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="markvi"/>


# チュートリアル: Azure Active Directory と DocuSign の統合

このチュートリアルでは、Azure と DocuSign の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

- 有効な Azure サブスクリプション
- DocuSign のテナント



このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. [DocuSign のアプリケーション統合の有効化](#enabling-the-application-integration-for-docusign) 


2. [シングル サインオンの構成](#configuring-single-sign-on)


3. [アカウント プロビジョニングの構成](#configuring-account-provisioning)


4. [ユーザーの割り当て](#assigning-users)




<br><br>![シングル サインオンの構成][0]<br>


 

## DocuSign のアプリケーション統合の有効化

このセクションでは、DocuSign のアプリケーション統合を有効にする方法について説明します。

### DocuSign のアプリケーション統合を有効にするには、次の手順を実行します。

1. Microsoft Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![シングル サインオンの構成][1]<br>

2. [ディレクトリ] の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br> ![シングル サインオンの構成][2]<br>

4. ページの下部にある **[追加]** をクリックします。<br><br>![アプリケーション][3]<br>

5. [実行する内容] ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br>![シングル サインオンの構成][4]<br>


6. 検索ボックスに、「**Docusign**」と入力します。<br><br>![シングル サインオンの構成][5]<br>

7. 結果ウィンドウで **[Docusign]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br><br>![シングル サインオンの構成][6]<br>




## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Docusign に対する認証を行うことができるようにする方法を説明します。


### シングル サインオンを構成するには、次の手順を実行します。

1. Azure AD ポータルの **Docusign アプリケーション統合**ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。<br><br>![シングル サインオンの構成][7]<br>

2. **[ユーザーの Docusign へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択してから [次へ] をクリックします。<br><br>![シングル サインオンの構成][8]<br>

3. **[アプリケーション URL の構成]** ページの **[Docusign サインオン URL]** テキスト ボックスに、Docusign テナントの URL を入力し、**[次へ]** をクリックします。URL のスキーマは、*https://<yourcompanyname>.docusign.net/Member/MemberLogin.aspx?ssoname=<yourSSOInstanceName>* <br><br>![シングル サインオンの構成][9]<br> です。


    > [AZURE.TIP]テナントのアプリ URL がわからない場合は、Docusign (SSOSetup@Docusign.com) に問い合わせて、テナントの SP によって開始される SSO URL を取得してください。
 

4. **[Docusign でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。<br><br>![シングル サインオンの構成][10]<br>


5. 別の Web ブラウザー ウィンドウで、**Docusign** 企業サイトに管理者としてログインします。


6. 上部のメニューでユーザーのメニューを展開し、**[基本設定]** をクリックしてから、左側のナビゲーション ウィンドウで **[アカウント管理]** を展開し、**[機能]** をクリックします。<br><br>![シングル サインオンの構成][11]<br>

7. **[SAML の構成]** をクリックしてから、**[SAML の構成]** リンクをクリックします。



8. **[SAML 2.0 の構成]** セクションで、次の手順を実行します。<br><br>![シングル サインオンの構成][13]<br>


    a.Azure ポータルで、**[Docusign でのシングル サインオンの構成]** ダイアログ ページの発行者の URL** の値をコピーし、**[ID プロバイダー エンドポイントの URL]** ボックスに貼り付けます。

    > [AZURE.IMPORTANT]この構成オプションを使用できない場合は、Docusign アカウント マネージャーに問い合わせるか、電子メール ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)) でSSO サポート チームに問い合わせてください。
 
    b.**[参照]** をクリックして、ダウンロードした証明書をアップロードします。


    c.**[名、姓、および電子メール アドレスを有効にする]** を選択します。


    d.**[保存]** をクリックします。


9. Azure AD ポータルで、**シングル サインオンの構成確認**を選択し、**[次へ]** をクリックします。<br><br>![アプリケーション][14]<br>

10. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![アプリケーション][15]<br>


 

## アカウント プロビジョニングの構成

このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを DocuSign に対して有効にする方法を説明します。

### ユーザー プロビジョニングを構成するには、次の手順に従います。

1. **Microsoft Azure 管理ポータル**の **DocuSign アプリケーション統合**ページで、**[アカウント プロビジョニングの構成]** をクリックして [ユーザー プロビジョニングの構成] ダイアログを開きます。<br><br>![アカウント プロビジョニングの構成][30]<br>
 

2. **[設定と管理者の資格情報]** ページで自動ユーザー プロビジョニングを有効にするには、十分な権限のある DocuSign アカウントの資格情報を入力してから **[次へ]** をクリックします。<br><br>![アカウント プロビジョニングの構成][31]<br>

3. **[接続テスト]** ダイアログで、**[テスト開始]** をクリックし、テストが正常に行われた場合は **[次へ]** をクリックします。<br><br>![アカウント プロビジョニングの構成][32]<br>

3. **[確認]** ページで **[完了]** をクリックします。

<br><br>![アカウント プロビジョニングの構成][33]<br>
 

## ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Docusign に割り当てるには、次の手順を実行します。

1. **Azure AD ポータル**で、テスト アカウントを作成します。

2. **Docusign アプリケーション統合**ページで、**[ユーザーの割り当て]** をクリックします。<br><br>![ユーザーの割り当て][40]<br>
 

3. テスト ユーザーを選び、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確認します。

<br><br>![ユーザーの割り当て][41]<br>


ここで 10 分間待機し、アカウントが Docusign に同期されたことを確認する必要があります。

最初の検証手順として、Microsoft Azure 管理ポータルの Docusign アプリケーション統合ページの D でダッシュボードをクリックして、プロビジョニングの状態を確認できます。<br><br>![ユーザーの割り当て][42]<br>

正常に完了したユーザー プロビジョニング サイクルは、次のように関連する状態で示されます。<br><br>![ユーザーの割り当て][43]<br>


シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。

アクセス パネルの詳細については、「アクセス パネルの概要」を参照してください。





## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_06.png
[11]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_07.png

[13]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_09.png
[14]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_18.png

<!---HONumber=Oct15_HO2-->