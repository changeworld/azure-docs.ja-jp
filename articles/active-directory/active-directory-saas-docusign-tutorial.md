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
	ms.date="08/01/2016"
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


6. 検索ボックスに、「**DocuSign**」と入力します。

	![シングル サインオンの構成][5]

7. 結果ウィンドウで **[DocuSign]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![シングル サインオンの構成][6]


## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD のユーザー アカウントで DocuSign に対する認証を行うことができるようにする方法を説明します。


### シングル サインオンを構成するには、次の手順に従います。

1. Azure クラシック ポータルの **DocuSign アプリケーション統合**ページで **[シングル サインオンの構成]** をクリックし、[シングル サインオンの構成] ダイアログを開きます。

	![シングル サインオンの構成][7]

2. **[ユーザーの DocuSign へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択してから [次へ] をクリックします。

	![シングル サインオンの構成][8]

3. **[アプリケーション設定の構成]** ページで、次の手順を実行します。

	![シングル サインオンの構成][9]

	a.**[サインオン URL]** ボックスに、次のパターンを使用して DocuSign テナントの URL を入力します。運用環境の場合、URL のパターンは **"https://account.docusign.com/organizations/<ORGANIZATIONID>/saml2/login/sp/<IDPID>"** となります。デモ環境の場合、URL のパターンは **"https://account-d.docusign.com/organizations/<ORGANIZATIONID>/saml2/login/sp/<IDPID>"** となります。

	b.**[識別子]** ボックスに、次のパターンを使用して DocuSign 発行者の URL を入力します。運用環境の場合、URL のパターンは **"https://account.docusign.com/organizations/<ORGANIZATIONID>/saml2"** となります。デモ環境の場合、このURL のパターンは **"https://account-d.docusign.com/organizations/<ORGANIZATIONID>/saml2"** となります。

	c.**[次へ]** をクリックします。


    > [AZURE.TIP] テナントのアプリ URL がわからない場合は、DocuSign ([SSOSetup@Docusign.com](emailTo:SSOSetup@Docusign.com)) に問い合わせて、テナントの SP によって開始される SSO URL を取得してください。
 

4. **[DocuSign でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。

	![シングル サインオンの構成][10]


5. 別の Web ブラウザー ウィンドウで、**DocuSign 管理ポータル**に管理者としてログインします。


6. 左側のナビゲーション メニューの **[Domains (ドメイン)]** をクリックします。

	![シングル サインオンの構成][51]

7. 次に、右側のウィンドウで **[CLAIM DOMAIN (ドメインの要求)]** ボタンをクリックします。

	![シングル サインオンの構成][52]

8. ポップアップ ウィンドウで、会社のドメイン名を入力し、[claim (要求)] をクリックします。ドメインを確認し、状態がアクティブであることを確認してください。

	![シングル サインオンの構成][53]

9. 左側のナビゲーション メニューの **[Identity Providers (ID プロバイダー)]** をクリックします。

	![シングル サインオンの構成][54]

10. 右側のウィンドウで、[ADD IDENTITY PROVIDER (ID プロバイダーの追加)] ボタンをクリックします。SSO 設定ページが開きます。
	
	![シングル サインオンの構成][55]

11. [Identity Provider Settings (ID プロバイダーの設定)] ページでは、次の操作を実行します。

	a.構成に一意の名前を付けます。単語の間に空白を使用しないでください。

	b.**[Identity Provider Issuer (ID プロバイダー発行者)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[発行者の URL]** の値を入力します。

	c.**[Identity Provider Login URL (ID プロバイダーのログイン URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[リモート ログイン URL]** の値を入力します。

	d.**[Identity PRovider Logout URL (ID プロバイダーのログアウト URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[リモート ログアウト URL]** の値を入力します。

	e.**[Sign AuthN Request (認証要求に署名する)]** チェック ボックスをオンにします。

	f.**[Send AuthN request by (認証要求の送信方法)]** オプションが **[POST]** に設定されていることを確認します。

	g.**[Send logout request by (ログアウト要求の送信方法)]** オプションが **[POST]** に設定されていることを確認します。

	![シングル サインオンの構成][56]

12. **[Custom Attribute Mapping (カスタム属性マッピング)]** セクションで、Azure AD の要求とマッピングするフィールドを選択します。たとえばここでは、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** という値とマッピングされた **[emailaddress]** 要求を使用しました。これは、Azure AD の電子メール要求の既定の要求名です。

	> [AZURE.NOTE] Azure AD のユーザーを DocuSign のユーザー マッピングにマップする際は、適切なユーザー識別子を使用してください。適切なフィールドを選択し、組織の設定に基づく適切な値を入力してください。

	![シングル サインオンの構成][57]

13. **[Identity Provider Certificate (ID プロバイダー証明書)]** セクションで、**[ADD CERTIFICATE (証明書の追加)]** ボタンをクリックし、Azure AD アプリケーションの構成ウィザードからダウンロードした証明書をアップロードします。

	![シングル サインオンの構成][58]

14. **[Save (保存)]** ボタンをクリックして、すべての設定を保存します。

15. **[Identity Providers (ID プロバイダー)]** セクションで、**[Actions (アクション)]** ボタンをクリックし、**[Endpoints (エンドポイント)]** をクリックします。

	![シングル サインオンの構成][59]

16. **[View SAML 2.0 Endpoints (SAML 2.0 エンドポイントの表示)]** セクションで、次の手順に従います。

	a.**[Service Provider Issuer URL (サービス プロバイダーの発行者 URL)]** をコピーし、Azure AD 構成ウィザードの **[識別子]** ボックスに貼り付けます。

	b.**[Service Provider Login URL (サービス プロバイダーのログイン URL)]** をコピーし、Azure AD 構成ウィザードの **[サインオン URL]** ボックスに貼り付けます。

	![シングル サインオンの構成][60]

	c.**[Close (閉じる)]** をクリックします。

15. Azure クラシック ポータルで、**シングル サインオンの構成確認**を選択し、**[次へ]** をクリックします。

	![アプリケーション][14]

10. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。

	![アプリケーション][15]
 

## アカウント プロビジョニングの構成

このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを DocuSign に対して有効にする方法を説明します。

### ユーザー プロビジョニングを構成するには、次の手順に従います。

1. **Azure クラシック ポータル**の **DocuSign アプリケーション統合**ページで、**[アカウント プロビジョニングの構成]** をクリックして [ユーザー プロビジョニングの構成] ダイアログを開きます。

	![アカウント プロビジョニングの構成][30]

2. **[設定と管理者資格情報]** ページで自動ユーザー プロビジョニングを有効にするには、十分な権限のある DocuSign アカウントの資格情報を入力してから **[次へ]** をクリックします。

	![アカウント プロビジョニングの構成][31]

3. **[Test connection (接続テスト)]** ダイアログで、**[テスト開始]** をクリックし、テストが正常に行われた場合は **[次へ]** をクリックします。

	![アカウント プロビジョニングの構成][32]

3. **[確認]** ページで **[完了]** をクリックします。

	![アカウント プロビジョニングの構成][33]
 

## ユーザーを割り当てる

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを DocuSign に割り当てるには、次の手順を実行します。

1. **Azure クラシック ポータル**で、テスト アカウントを作成します。

2. **DocuSign アプリケーション統合**ページで、**[ユーザーの割り当て]** をクリックします。

	![ユーザーの割り当て][40]
 

3. テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

	![ユーザーの割り当て][41]


ここで 10 分間待機し、アカウントが DocuSign に同期されたことを確認する必要があります。

最初の検証手順として、Azure クラシック ポータルの DocuSign アプリケーション統合ページの D でダッシュボードをクリックして、プロビジョニングの状態を確認できます。

![ユーザーの割り当て][42]

正常に完了したユーザー プロビジョニング サイクルは、関連する状態で示されます。

![ユーザーの割り当て][43]


シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。

アクセス パネルの詳細については、「アクセス パネルの概要」を参照してください。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png

<!---HONumber=AcomDC_0803_2016-->