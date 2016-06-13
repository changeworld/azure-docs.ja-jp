<properties
	pageTitle="チュートリアル: Azure Active Directory と DocuSign の統合 | Microsoft Azure"
	description="Azure Active Directory と DocuSign の間でシングル サインオンを構成する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と DocuSign の統合

このチュートリアルでは、Azure と DocuSign の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

- 有効な Azure サブスクリプション
- DocuSign のテナント



このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. [DocuSign のアプリケーション統合の有効化](#enabling-the-application-integration-for-docusign) 


2. [シングル サインオンの構成](#configuring-single-sign-on)


3. [アカウント プロビジョニングの構成](#configuring-account-provisioning)


4. [ユーザーの割り当て](#assigning-users)




![シングル サインオンの構成][0]


 

## DocuSign のアプリケーション統合の有効化

このセクションでは、DocuSign のアプリケーション統合を有効にする方法について説明します。

### DocuSign のアプリケーション統合を有効にするには、次の手順を実行します。

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![シングル サインオンの構成][1]

2. [ディレクトリ] の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![シングル サインオンの構成][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. [実行する内容] ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![シングル サインオンの構成][4]


6. 検索ボックスに、「**Docusign**」と入力します。

	![シングル サインオンの構成][5]

7. 結果ウィンドウで **Docusign** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![シングル サインオンの構成][6]




## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Docusign に対する認証を行うことができるようにする方法を説明します。


### シングル サインオンを構成するには、次の手順に従います。

1. Azure クラシック ポータルの **Docusign アプリケーション統合**ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

	![シングル サインオンの構成][7]

2. **[ユーザーの Docusign へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択してから [次へ] をクリックします。

	![シングル サインオンの構成][8]

3. **[アプリケーション設定の構成]** ページで、次の手順を実行します。

	![シングル サインオンの構成][9]

	a. **[サインオン URL]** ボックスに、`https://<company name>.docusign.net/Member/MemberLogin.aspx?ssoname=<SSO instance name>` というパターンで Docusign テナントの URL を入力します。

	b.**[次へ]** をクリックします。


    > [AZURE.TIP] テナントのアプリ URL がわからない場合は、Docusign (SSOSetup@Docusign.com) に問い合わせて、テナントの SP によって開始される SSO URL を取得してください。
 

4. **[Docusign でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。

	![シングル サインオンの構成][10]


5. 別の Web ブラウザー ウィンドウで、**Docusign** 企業サイトに管理者としてログインします。


6. 上部のメニューでユーザーのメニューを展開し、**[基本設定]** をクリックしてから、左側のナビゲーション ウィンドウで **[アカウント管理]** を展開し、**[機能]** をクリックします。

	![シングル サインオンの構成][11]

7. **[SAML の構成]** をクリックしてから、**[SAML の構成]** リンクをクリックします。



8. **[SAML 2.0 の構成]** セクションで、次の手順を実行します。

	![シングル サインオンの構成][13]


    a.Azure クラシック ポータルで、**[Docusign でのシングル サインオンの構成]** ダイアログ ページの発行者の URL** の値をコピーし、**[ID プロバイダー エンドポイントの URL]** ボックスに貼り付けます。

    > [AZURE.IMPORTANT] この構成オプションを使用できない場合は、Docusign アカウント マネージャーに問い合わせるか、電子メール ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)) でSSO サポート チームに問い合わせてください。
 
    b.**[参照]** をクリックして、ダウンロードした証明書をアップロードします。


    c.**[名、姓、および電子メール アドレスを有効にする]** を選択します。


    d.**[保存]** をクリックします。


9. Azure クラシック ポータルで、**シングル サインオンの構成確認**を選択し、**[次へ]** をクリックします。

	![アプリケーション][14]

10. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。

	![アプリケーション][15]


 

## アカウント プロビジョニングの構成

このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを DocuSign に対して有効にする方法を説明します。

### ユーザー プロビジョニングを構成するには、次の手順に従います。

1. **Azure クラシック ポータル**の **DocuSign アプリケーション統合**ページで、**[アカウント プロビジョニングの構成]** をクリックして [ユーザー プロビジョニングの構成] ダイアログを開きます。

	![アカウント プロビジョニングの構成][30]
 

2. **[設定と管理者の資格情報]** ページで自動ユーザー プロビジョニングを有効にするには、十分な権限のある DocuSign アカウントの資格情報を入力してから **[次へ]** をクリックします。

	![アカウント プロビジョニングの構成][31]

3. **[接続テスト]** ダイアログで、**[テスト開始]** をクリックし、テストが正常に行われた場合は **[次へ]** をクリックします。

	![アカウント プロビジョニングの構成][32]

3. **[確認]** ページで **[完了]** をクリックします。

![アカウント プロビジョニングの構成][33]
 

## ユーザーを割り当てる

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Docusign に割り当てるには、次の手順を実行します。

1. **Azure クラシック ポータル**で、テスト アカウントを作成します。

2. **Docusign アプリケーション統合**ページで、**[ユーザーの割り当て]** をクリックします。

	![ユーザーの割り当て][40]
 

3. テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

	![ユーザーの割り当て][41]


ここで 10 分間待機し、アカウントが Docusign に同期されたことを確認する必要があります。

最初の検証手順として、Azure クラシック ポータルの Docusign アプリケーション統合ページの D でダッシュボードをクリックして、プロビジョニングの状態を確認できます。

![ユーザーの割り当て][42]

正常に完了したユーザー プロビジョニング サイクルは、関連する状態で示されます。

![ユーザーの割り当て][43]


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

<!---HONumber=AcomDC_0601_2016-->