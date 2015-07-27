<properties
   pageTitle="チュートリアル: Azure Active Directory と Google Apps の統合 | Microsoft Azure"
   description="Azure Active Directory で Google Apps を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。"
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

#チュートリアル: Azure Active Directory と Google Apps を統合する方法

このチュートリアルでは、Azure Active Directory (Azure AD) に Google Apps 環境を接続する方法を説明します。Google Apps へのシングル サインオンを構成する方法、ユーザー プロビジョニングの自動化を有効にする方法、Google Apps へのアクセス権をユーザーに割り当てる方法について説明します。

##前提条件

1. [Azure 管理ポータル](https://manage.windowsazure.com)から Azure Active Directory にアクセスするには、まず有効な Azure サブスクリプションが必要です。

2. [Google Apps for Work](https://www.google.com/work/apps/) または [Google Apps for Education](https://www.google.com/edu/products/productivity-tools/) に対して有効なテナントが必要です。どちらのサービスにも無料試用版のアカウントを使用できます。

##手順 1. Google Apps をディレクトリに追加する

1. [Azure 管理ポータル](https://manage.windowsazure.com)の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Select Active Directory from the left navigation pane.][0]

2. **[ディレクトリ]** ボックスの一覧から、Google Apps を追加するディレクトリを選択します。

3. 上部のメニューで **[アプリケーション]** をクリックします。

	![Click on Applications.][1]

4. ページの下部にある **[追加]** をクリックします。

	![Click Add to add a new application.][2]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![Click Add an application from the gallery.][3]

6. **検索ボックス**に、「**Google Apps**」と入力します。次に、結果の一覧から **[Google Apps]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![Add Google Apps.][4]

7. これで、Google Apps の [クイック スタート] ページが表示されます。

	![Google Apps' Quick Start page in Azure AD][5]

##手順 2. シングル サインオンを有効にする

1. Azure AD の Google Apps の [クイック スタート] ページで、**[シングル サインオンの構成]** ボタンをクリックします。

	![The configure single sign-on button][6]

2. ダイアログが開き、ユーザーが Google Apps にサインオンする方法についてたずねる画面が表示されます。 **[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

	![Select Azure AD Single Sign-On][7]

	> [AZURE.NOTE]さまざまなシングル サインオンのオプションの詳細については、[ここをクリック](https://msdn.microsoft.com/library/azure/dn308588.aspx)してください。

3. **[アプリケーション設定の構成]** ページで、**[サインオン URL]** フィールドに、Google Apps テナントの URL を `https://mail.google.com/a/<yourdomain>` という形式を使用して入力します。

	![Type in your tenant URL][8]

4. **[Google Apps でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。

	![Download the certificate.][9]

5. ブラウザーで新しいタブを開き、管理者アカウントを使用して、[Google Apps の管理コンソール](http://admin.google.com/)にサインインします。

6. **[セキュリティ]** をクリックします。このリンクが表示されていない場合、画面下部の **[その他の設定]** に隠れていることがあります。

	![Click Security.][10]

7. **[セキュリティ]** ページで、**[シングル サインオン (SSO) の設定]** をクリックします。

	![Click SSO.][11]

8. 次の構成の変更を実行します。

	![Configure SSO][12]

	- **[サード パーティの ID プロバイダで SSO を設定する]** を選択します。

	- Azure AD で **[シングル サインオン サービス URL]** をコピーし、Google Apps の **[ログイン ページ URL]** フィールドに貼り付けます。

	- Azure AD で **[シングル サインアウト サービス URL]** をコピーし、Google Apps の **[ログアウト ページの URL]** フィールドに貼り付けます。

	- Azure AD で **[パスワードの URL の変更]** をコピーし、Google Apps の **[パスワード変更 URL]** フィールドに貼り付けます。

	- Google Apps の **[認証の確認]** で、手順 4. でダウンロードした証明書をアップロードします。

	- **[変更を保存]** をクリックします。

9. Azure AD でシングル サインオンの構成確認のチェック ボックスをオンにして、Google Apps にアップロードした証明書を有効にします。その後、**[次へ]** をクリックします。

	![Check the confirmation checkbox][13]

10. ダイアログの最後のページでは、このシングル サインオン構成のメンテナンスに関連するエラーと警告の電子メール通知を受信する場合、電子メール アドレスを入力します。

	![Type in your email address.][14]

11. **[完了]** をクリックしてダイアログを閉じます。構成をテストするには、この後にある「[Google Apps にユーザーを割り当てる](#step-4-assign-users-to-google-apps)」というタイトルのセクションを参照してください。

##手順 3. 自動化されたユーザー プロビジョニングを有効にする

> [AZURE.NOTE]Google Apps へのユーザー プロビジョニングを自動化する方法としては、[Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en) を使用して Google Apps にオンプレミスの Active Directory ID をプロビジョニングする方法もありますが、このチュートリアルでは Azure Active Directory (クラウド) のユーザーとメールが有効なグループを Google Apps にプロビジョニングします。

1. 管理者アカウントを使用して、[Google Apps の管理コンソール](http://admin.google.com/)にサインインし、**[セキュリティ]** をクリックします。このリンクが表示されていない場合、画面下部の **[その他の設定]** に隠れていることがあります。

	![Click Security.][10]

2. **[セキュリティ]** ページで、**[API リファレンス]** をクリックします。

	![Click API Reference.][15]

3. **[API アクセスを有効にする]** を選択します。

	![Click API Reference.][16]

	> [AZURE.IMPORTANT]Google Apps にプロビジョニングするすべてのユーザーは、Azure Active Directory でのユーザー名がカスタム ドメインに関連付けられている*必要があります*。たとえば Google Apps では、bob@contoso.onmicrosoft.com のようなユーザー名は使用できませんが、bob@contoso.com のようなユーザー名は使用できます。Azure AD でプロパティを編集することによって、既存のユーザーのドメインを変更できます。Azure Active Directory と Google Apps の両方でカスタム ドメインを設定する方法を次に示します。

4. まだ Azure Active Directory にカスタム ドメイン名を追加していない場合は、次の手順に従います。

	- [Azure 管理ポータル](https://manage.windowsazure.com)の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。ディレクトリの一覧で、ディレクトリを選択します。 

	- 最上部のメニューで、**[ドメイン]** をクリックし、**[カスタム ドメインの追加]** をクリックします。

		![カスタム ドメインの追加][17]

	- **[ドメイン名]** フィールドにドメイン名を入力します。Google Apps で使用するのと同じドメイン名にする必要があります。入力したら、**[追加]** をクリックします。

		![Type in your domain name.][18]

	- **[次へ]** をクリックし、確認ページに移動します。このドメインを所有していることを確認するために、このページに表示されている値に従ってドメインの DNS レコードを編集する必要があります。**[レコードの種類]** オプションで選択する種類に応じて、**MX レコード**と **TXT レコード**のどちらを使用して確認するかを選択できます。Azure AD でドメイン名を確認する、より包括的な手順の詳細については、「[Azure AD への独自のドメイン名の追加](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409)」を参照してください。

		![Verify your domain name.][19]

	- ディレクトリに追加するすべてのドメインに対して、上記の手順を繰り返します。

5. すべてのドメインを Azure AD で確認したので、今度は Google Apps で確認する必要があります。Google Apps にまだ登録されていない各ドメインに対して、次の手順を実行します。

	- [Google Apps の管理コンソール](http://admin.google.com/)で、**[ドメイン]** をクリックします。

		![Click on Domains][20]

	- **[ドメインやドメイン エイリアスを追加]** をクリックします。

		![Add a new domain][21]

	- **[別のドメインを追加]** を選択し、追加するドメインの名前を入力します。

		![Type in your domain name][22]

	- **[続行してドメインの所有権を確認]** をクリックします。次に、手順に従って、ドメイン名を所有していることを確認します。Google Apps でドメインを確認する包括的な手順については、[Google Apps でサイトの所有者であることを確認する](https://support.google.com/webmasters/answer/35179)方法に関するページを参照してください。

	- Google Apps に追加するすべての追加ドメインに対して、上記の手順を繰り返します。

	> [AZURE.WARNING]Google Apps のテナントのプライマリ ドメインを変更する場合や Azure AD でシングル サインオンが構成済みである場合、「[手順 2. シングル サインオンを有効にする](#step-two-enable-single-sign-on)」の手順 3. を繰り返す必要があります。

6. [Google Apps の管理コンソール](http://admin.google.com/)で、**[管理者の役割]** をクリックします。

	![Click on Google Apps][26]

7. ユーザー プロビジョニングの管理にどの管理者アカウントを使用するかを決定します。使用するアカウントの **[管理者の役割]** で、役割の **[権限]** を編集します。このアカウントをプロビジョニングに使用できるように、**[管理 API の権限]** がすべて有効になっていることを確認します。

	![Click on Google Apps][27]

	> [AZURE.NOTE]運用環境を構成している場合は、特にこの手順で Google Apps の新しい管理者アカウントを作成することをお勧めします。管理者アカウントには、必要な API 特権を持つ管理者の役割が関連付けられている必要があります。

8. Azure Active Directory の最上部のメニューで、**[アプリケーション]** をクリックし、**[Google Apps]** をクリックします。

	![Click on Google Apps][23]

9. Google Apps の [クイック スタート] ページで、**[ユーザー プロビジョニングの構成]** をクリックします。

	![Configure user provisioning][24]

10. 表示されるダイアログ ボックスで、**[ユーザー プロビジョニングを有効にする]** をクリックして、プロビジョニングの管理に使用する Google Apps の管理者アカウントを認証します。

	![Enable provisioning][25]

11. Google Apps のテナントに対して変更を行うためのアクセス許可を Azure Active Directory に付与することを確認します。

	![Confirm permissions.][28]

12. **[完了]** をクリックしてダイアログを閉じます。

##手順 4. Google Apps にユーザーを割り当てる

1. 構成をテストするために、ディレクトリに新しいテスト アカウントを作成します。

2. Google Apps の [クイック スタート] ページで **[ユーザーの割り当て]** ボタンをクリックします。

	![Click on Assign Users][29]

3. テスト ユーザーを選択し、画面の下部にある **[割り当て]** ボタンをクリックします。

 - 自動化されたユーザー プロビジョニングを有効にしていない場合は、次のような確認メッセージが表示されます。

		![Confirm the assignment.][30]

 - ユーザー プロビジョニングの自動化を有効にしている場合は、ユーザーに必要な Google Apps での役割の種類の定義を要求するプロンプトが表示されます。新しくプロビジョニングされたユーザーは、数分後に、Google Apps の環境に表示されます。

4. シングル サインオンの設定をテストするために、アクセス パネル ([https://myapps.microsoft.com](https://myapps.microsoft.com/)) を開き、テスト アカウントにサインインし、**[Google Apps]** をクリックします。

##関連項目

- [SaaS アプリケーションの統合に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
- [Azure AD でのアプリケーションへのアクセス](https://msdn.microsoft.com/library/azure/dn308590.aspx)
- [アクセス パネルの概要](https://msdn.microsoft.com/library/azure/dn308586.aspx)

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png

<!---HONumber=July15_HO3-->