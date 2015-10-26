<properties
   pageTitle="チュートリアル: Azure Active Directory と NetSuite の統合 | Microsoft Azure"
   description="Azure Active Directory で NetSuite を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/15/2015"
   ms.author="liviodlc"/>

#チュートリアル: Azure Active Directory と NetSuite を統合する方法

このチュートリアルでは、Azure Active Directory (Azure AD) に NetSuite 環境を接続する方法を説明します。NetSuite へのシングル サインオンを構成する方法、ユーザー プロビジョニングの自動化を有効にする方法、NetSuite へのアクセス権をユーザーに割り当てる方法について説明します。

##前提条件

1. [Azure 管理ポータル](https://manage.windowsazure.com)から Azure Active Directory にアクセスするには、まず有効な Azure サブスクリプションが必要です。

2. [NetSuite](http://www.netsuite.com/portal/home.shtml) サブスクリプションに対する管理者アクセス権が必要です。どちらのサービスにも無料試用版のアカウントを使用できます。

##手順 1. NetSuite をディレクトリに追加する

1. [Azure 管理ポータル](https://manage.windowsazure.com)の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Select Active Directory from the left navigation pane.][0]

2. **[ディレクトリ]** の一覧から、NetSuite を追加するディレクトリを選択します。

3. 上部のメニューで **[アプリケーション]** をクリックします。

	![Click on Applications.][1]

4. ページの下部にある **[追加]** をクリックします。

	![Click Add to add a new application.][2]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![Click Add an application from the gallery.][3]

6. **検索ボックス**に、「**NetSuite**」と入力します。次に、結果の一覧から **[NetSuite]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![Add NetSuite.][4]

7. これで、NetSuite の [クイック スタート] ページが表示されます。

	![NetSuite's Quick Start page in Azure AD][5]

##手順 2. シングル サインオンを有効にする

1. Azure AD の NetSuite の [クイック スタート] ページで、**[シングル サインオンの構成]** ボタンをクリックします。

	![The configure single sign-on button][6]

2. ダイアログが開き、ユーザーが NetSuite にサインオンする方法についてたずねる画面が表示されます。 **[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

	![Select Azure AD Single Sign-On][7]

	> [AZURE.NOTE]さまざまなシングル サインオンのオプションの詳細については、[ここをクリック](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)してください。

3. **[アプリケーション設定の構成]** ページで、**[サインオン URL]** フィールドに、NetSuite のテナントの URL を次のいずれかの形式を使用して入力します。
	- `https://<tenant-name>.netsuite.com`
	- `https://<tenant-name>.na1.netsuite.com`
	- `https://<tenant-name>.na2.netsuite.com`
	- `https://<tenant-name>.sandbox.netsuite.com`
	- `https://<tenant-name>.na1.sandbox.netsuite.com`
	- `https://<tenant-name>.na2.sandbox.netsuite.com`

	![Type in your tenant URL][8]

4. **[NetSuite でのシングル サインオン構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。

	![Download the metadata.][9]

5. ブラウザーで新しいタブを開き、Netsuite の会社のサイトに管理者としてサインインします。

6. ページの上部にあるツール バーで、**[Setup]**、**[Setup Manager]** の順にクリックします。

	![Go to Setup Manager][10]

7. **[Setup Tasks]** の一覧で、**[Integration]** をクリックします。

	![Go to Integration][11]

8. **[Manage Authentication]** セクションで、**[SAML Single Sign-on]** をクリックします。

	![Go to SAML Single Sign-on][12]

9. **[SAML Setup]** ページで、次の手順を実行します。

	- Azure Active Directory で **[リモート ログイン URL]** の値をコピーし、NetSuite の **[Identity Provider Login Page]** フィールドに貼り付けます。

		![The SAML Setup page.][13]

	- NetSuite で、**[Primary Authentication Method]** を選択します。

	- **[SAMLV2 Identity Provider Metadata]** フィールドで、**[Upload IDP Metadata File]** を選択します。**[Browse]** をクリックし、手順 4. でダウンロードしたメタデータ ファイルをアップロードします。

		![Upload the metadata][16]

	- **[Submit]** をクリックします。

9. Azure AD でシングル サインオンの構成確認のチェック ボックスをオンにして、NetSuite にアップロードした証明書を有効にします。その後、**[次へ]** をクリックします。

	![Check the confirmation checkbox][14]

10. ダイアログの最後のページでは、このシングル サインオン構成のメンテナンスに関連するエラーと警告の電子メール通知を受信する場合、電子メール アドレスを入力します。

	![Type in your email address.][15]

11. **[完了]** をクリックしてダイアログを閉じます。次に、ページの上部にある **[属性]** をクリックします。

	![Click on Attributes.][17]

12. **[ユーザー属性の追加]** をクリックします。

	![Click on Add User Attribute.][18]

13. **[属性名]** フィールドに「`account`」と入力します。**[属性値]** フィールドに、NetSuite アカウント ID を入力します。アカウント ID を確認する方法を次に示します。

	![Add your NetSuite Account ID.][19]

	- NetSuite で、上部のナビゲーション メニューから **[Setup]** をクリックします。
	- 左側のナビゲーション メニューの **[Setup Tasks]** セクションで、**[Integration]** セクションをクリックし、**[Web Services Preferences]** をクリックします。
	- NetSuite アカウント ID をコピーし、Azure AD の **[属性値]** フィールドに貼り付けます。

		![Get your account ID][20]

14. Azure AD で、**[完了]** をクリックして、SAML 属性の追加を完了します。下部のメニューで、**[変更の適用]** をクリックします。

	![変更を保存します。][21]

15. ユーザーは NetSuite にシングル サインオンする前に、まず、NetSuite で適切なアクセス許可が割り当てられている必要があります。次の手順に従って、アクセス許可を割り当てます。

	- 上部のナビゲーション メニューで、**[Setup]**、**[Setup Manager]** の順にクリックします。

	![Go to Setup Manager][10]

	- 左側のナビゲーション メニューで、**[Users/Roles]**、**[Manage Roles]** の順にクリックします。

	![Go to Manage Roles][22]

	- **[New Role]** をクリックします。

	- 新しいロールの **[Name]** を入力し、**[Single Sign-On Only]** チェック ボックスをオンにします。

	![Name the new role.][23]

	- **[保存]** をクリックします。

	- 上部のメニューで、**[Permissions]** をクリックします。**[Setup]** をクリックします。

	![Go to Permissions][24]

	- **[Set Up SAM Single Sign-on]**、**[Add]** の順にクリックします。

	- **[保存]** をクリックします。

	- 上部のナビゲーション メニューで、**[Setup]**、**[Setup Manager]** の順にクリックします。

	![Go to Setup Manager][10]

	- 左側のナビゲーション メニューで、**[Users/Roles]**、**[Manage Users]** の順にクリックします。

	![Go to Manage Users][25]

	- テスト ユーザーを選択します。**[Edit]** をクリックします。

	![Go to Manage Users][26]

	- [Roles] ダイアログ ボックスで、作成したロールを選択し、**[Add]** をクリックします。

	![Go to Manage Users][27]

	- **[保存]** をクリックします。

19. 構成をテストするには、この後にある「[NetSuite にユーザーを割り当てる](#step-4-assign-users-to-netsuite)」というタイトルのセクションを参照してください。

##手順 3. 自動化されたユーザー プロビジョニングを有効にする

1. Azure Active Directory の NetSuite の [クイック スタート] ページで、**[ユーザー プロビジョニングの構成]** をクリックします。

	![Configure user provisioning][28]

2. 表示されるダイアログ ボックスで、NetSuite の管理者の資格情報を入力し、**[次へ]** をクリックします。

	![Type in your NetSuite admin credentials.][29]

3. プロビジョニング エラーの通知を受け取る場合は、確認ページで電子メール アドレスを入力します。

	![Confirm.][30]

4. **[完了]** をクリックしてダイアログを閉じます。

##手順 4. NetSuite にユーザーを割り当てる

1. 構成をテストするために、ディレクトリに新しいテスト アカウントを作成します。

2. NetSuite の [クイック スタート] ページで **[ユーザーの割り当て]** ボタンをクリックします。

	![Click on Assign Users][31]

3. テスト ユーザーを選択し、画面の下部にある **[割り当て]** ボタンをクリックします。

 - 自動化されたユーザー プロビジョニングを有効にしていない場合は、次のような確認メッセージが表示されます。

		![Confirm the assignment.][32]

 - ユーザー プロビジョニングの自動化を有効にしている場合は、ユーザーに必要な NetSuite でのロールの種類の定義を要求するプロンプトが表示されます。新しくプロビジョニングされたユーザーは、数分後に NetSuite 環境に表示されます。

4. シングル サインオンの設定をテストするために、アクセス パネル ([https://myapps.microsoft.com](https://myapps.microsoft.com/)) を開き、テスト アカウントにサインインし、**[NetSuite]** をクリックします。

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png

<!---HONumber=Oct15_HO3-->