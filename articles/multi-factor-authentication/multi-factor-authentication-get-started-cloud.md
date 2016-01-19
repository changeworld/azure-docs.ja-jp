<properties 
	pageTitle="クラウドでの Azure Multi-Factor Authentication Server の概要" 
	description="クラウドで Azure MFA を開始する方法について説明する Azure Multi-Factor Authentication のページです。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="01/11/2016" 
	ms.author="billmath"/>

# クラウドでの Azure Multi-Factor Authentication Server の概要



<center>![Cloud](./media/multi-factor-authentication-get-started-cloud/cloud2.png)</center>

クラウドで多要素認証を使用することを決めたので、早速開始してみましょう。 Multi-Factor Authentication for Office 365 または Multi-Factor Authentication for Azure Administrators を使用している場合、手順 3 にスキップできます。


1. [Azure サブスクリプションへのサインアップ](https://azure.microsoft.com/pricing/free-trial/)
	- Azure サブスクリプションをまだ取得していない場合、サインアップする必要があります。Azure MFA を開始して使ってみるだけの場合、評価版のサブスクリプションを使用できます。
2. [Multi-Factor Auth プロバイダーの作成](#creating-an-azure-multi-factor-auth-provider)または[ユーザーへのライセンスの割り当て](multi-factor-authentication-get-started-cloud.md#assigning-an-azure-ad-premium-or-enterprise-mobility-license-to-users)
	- Azure Multi-Factor Auth プロバイダーを作成し、それをディレクトリに割り当てるか、または Azure MFA、Azure AD Premium、および/または EMS のライセンスをユーザーに割り当てます。Azure Multi Factor Authentication は Azure Active Directory Premium に含まれるため、Enterprise Mobility Suite にも含まれることになります。Azure MFA、Azure AD Premium、および EMS の適切なライセンスを所有している場合は、Multi-Factor Auth プロバイダーを作成しなくても、多要素認証を使用するすべての用途に対応することができます。ライセンスをユーザーに割り当てる方法については、以下のセクションを参照してください。
3. [ユーザーの Multi-Factor Authentication を有効にする](#turn-on-multi-factor-authentication-for-users)
	- Office 365 または Azure ポータルを通じてユーザーの Azure MFA を有効にします。これを行う方法については、以下のセクションを参照してください。
4. [電子メールをエンド ユーザーに送信して MFA に関して通知する](#send-email-to-end-users)
	- ユーザーがアカウントの多要素認証を有効にした場合、電子メールでそのことを通知するようお勧めします。ユーザーは、次回のサインインの際に処理を完了するよう求められます。これにより、何が期待されているかがわかります。電子メールのテンプレートの例については、以下のセクションを参照してください。



## Azure Multi-Factor Auth プロバイダーの作成
Azure Active Directory を持つグローバル管理者は、既定で多要素認証を使用できます。ただし、すべてのユーザーに多要素認証を拡張する場合や、グローバル管理者や Office 365 ユーザーが信頼できる IP、カスタムの案内応答、レポートなどの高度な機能を利用できるようにする場合は、Azure MFA の完全なバージョンを構成する必要があります。そのための 1 つの方法として、Multi-Factor Authentication プロバイダーを作成します。



### Multi-Factor Auth プロバイダーを作成するには
--------------------------------------------------------------------------------

1. Azure ポータルに管理者としてサインインします。
2. 左側で、[Active Directory] を選択します。
3. [Active Directory] ページの上部で [Multi-Factor Authentication Provider] をクリックします。下部にある **[新規]** をクリックします。
4. [App Services] の下の [Multi-Factor Auth Providers] を選択し、[簡易作成] を選択します。
5. 次のフィールドに入力し、[作成] をクリックします。
	1. 名前 – Multi-Factor Auth プロバイダーの名前。
	2. 使用モデル – Multi-Factor Authentication プロバイダーの使用モデル。
		- 認証ごと – 認証ごとに課金される購入モデル。通常、コンシューマー向けのアプリケーションで Azure Multi-factor Authentication を使用するシナリオで使用されます。
		- 有効ユーザーごと – 有効ユーザーごとに課金される購入モデル。通常、Office 365 などのアプリケーションにアクセスに従業員向けに使用されます。
	2. ディレクトリ – Multi-Factor Authentication プロバイダーに関連付けられている Azure Active Directory テナント。次の点に注意してください。
		- Multi-Factor Auth プロバイダーの作成に、Azure AD ディレクトリは必要はありません。Azure Multi-Factor Authentication Server または SDK のみを使用する計画の場合、これをブランクにすることができます。
		- 多要素認証をすべてのユーザーに展開する場合や、グローバル管理者が信頼できる IP、カスタムの案内応答、レポートなどの高度な機能を利用できるようにする場合は、Multi-Factor Auth プロバイダーを Azure AD ディレクトリに関連付ける必要があります。
		- Azure AD Connect、AAD Sync、または DirSync は、オンプレミスの Active Directory 環境を Azure AD ディレクトリと同期している場合にのみ必要です。オンプレミスの Active Directory インスタンスと同期されない Azure AD ディレクトリのみを使用する場合、それらは必要ありません。
		



5. 作成をクリックした後、Multi-Factor Authentication プロバイダーが作成され、「Multi-Factor Authentication プロバイダーは正常に作成されました」というメッセージが表示されます。[OK] をクリックします。

<center>![Cloud](./media/multi-factor-authentication-get-started-cloud/provider.png)</center>
## Azure MFA、Azure AD Premium、または Enterprise Mobility のライセンスをユーザーに割り当てる

Azure MFA、Azure AD Premium、または Enterprise Mobility Suite のライセンスを購入済みである場合、Multi-Factor Auth プロバイダーを作成する必要はありません。ユーザーにライセンスを割り当てるだけで、MFA に対するライセンスの有効化を開始することができます。

### Azure MFA、Azure AD Premium、または Enterprise Mobility Suite のライセンスを割り当てるには
--------------------------------------------------------------------------------

1. Azure ポータルに管理者としてサインインします。
2. 左側で、**[Active Directory]** を選択します。
3. [Active Directory] ページで、有効にするユーザーが存在するディレクトリをダブルクリックします。
4. ディレクトリ ページの上部にある **[ライセンス]** を選択します。
5. [ライセンス] ページで、[Azure Multi-Factor Authentication]、[Active Directory Premium]、または [Enterprise Mobility Suite] を選択し、[割り当て] をクリックします。![クラウド](./media/multi-factor-authentication-get-started-cloud/license2.png)
6. ダイアログ ボックスで、ライセンスを割り当てるユーザーまたはグループを選択し、チェック マーク アイコンをクリックして変更を保存します。






## ユーザーの多要素認証を有効にする

Azure Multi-factor Authentication のユーザー アカウントには、次の 3 つの異なる状態があります。

状態 | 説明 |非ブラウザー アプリに影響があるか| メモ 
:-------------: | :-------------: |:-------------: |:-------------: |
無効 | 新しいユーザーの既定の状態は、多要素認証に登録されていません。|いいえ|ユーザーは現在、多要素認証を使用していません。
有効 |ユーザーは多要素認証に登録されています。|いいえ。これらは登録プロセスが完了するまで機能し続けます。|ユーザーは有効ですが、登録プロセスが完了していません。次回サインインするときにプロセスを完了するよう求められます。
適用|ユーザーが登録されており、多要素認証を使用するための登録プロセスが完了しています。|はい。アプリ パスワードが作成され、使用されるまでは機能しません。 | ユーザーは登録を完了していない可能性があります。登録プロセスが完了している場合、多要素認証を使用しています。登録プロセスが完了していない場合、次回サインインするときにプロセスを完了するよう求められます。
Auth Provider を用意したかユーザーにライセンスを割り当てたので、次に、ディレクトリのユーザーの MFA を有効にします。ユーザーの MFA を有効にするには、次の手順を使用します。

### 多要素認証を有効にするには
--------------------------------------------------------------------------------
1.  Microsoft Azure 管理ポータルに管理者としてサインインします。
2.  左側の [Active Directory] をクリックします。
3.  [ディレクトリ] の下で、有効にするユーザーのディレクトリをクリックします。
4.  上部の [ユーザー] をクリックします。
5.  ページ下部の [多要素認証の管理] をクリックします。
6.  多要素認証を有効にするユーザーを検索します。上部でビューを変更することが必要になる場合があります。ユーザーの状態が無効であることを確認し、名前の横のボックスにチェック マークを入れます。
7.  これにより、右側に 2 つのオプション ([有効] と [ユーザー設定の管理]) が表示されます。[有効] をクリックします。これにより、ユーザーに対して行う必要がある次の手順を示すポップアップが表示されます。[多要素認証を有効にする] をクリックします。
8.  ユーザーを有効にした場合、非ブラウザー アプリを使用する方法およびロックアウトしないようにする方法を知らせる電子メールをユーザーに送信することをお勧めします。

<center>![Cloud](./media/multi-factor-authentication-get-started-cloud/user.png)</center>

Windows PowerShell を使用してユーザーの状態を変更するには、次の操作を使用できます。`$st.State` を変更して、上記の状態のいずれかと同等になるようにすることができます。

		$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
		$st.RelyingParty = "*"
		$st.State = “Enabled”
		$sta = @($st)
		Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta


## エンド ユーザーへの電子メールの送信

ユーザーを有効にした後、連絡先情報を提供する必要があることを知らせる電子メールをユーザーに送信することをお勧めします。以下は、ユーザーが視聴できるビデオへのリンクが含まれる使用可能な電子メール テンプレートです。

		Subject: ACTION REQUIRED: Your password for Outlook and other apps needs updated

		Body:

		For added security, we have enabled multi-factor authentication for your account. 

		Action Required: You will need to complete the enrollment steps below to make your account secure with multi-factor authentication.  

		What to expect once MFA is enabled:

		Multi-factor authentication requires a password that you know and a phone that you have in order to sign into browser applications and to access Office 365, Azure portals.

		For Office 365 non-browser applications such as outlook, lync, a mail client on your mobile device etc, a special password called an app password is required instead of your account password to sign in. App passwords are different than your account password, and are generated during the multi-factor authentication set up process. 

		Please follow these enrollment steps to avoid interruption of your Office 365 service:

			1.  Sign in to the Office 365 Portal at http://portal.microsoftonline.com.
			2.  Follow the instructions to set up your preferred multi-factor authentication method when signing into Office 365 using a web browser. 
			3.  Create one app password for each device.
			4.  Enter the same app password in all applicable apps on that device e.g. Outlook, Mail client, Lync, Word, Powerpoint, Excel, CRM etc. 
			5.  Update your Office client applications or other mobile applications to use an app password.

		You can visit http://aka.ms/mfasetup to create app passwords or change your MFA Setting.  Please bookmark this.

		NOTE: Before entering an app password, you will need to clear the sign-in information (delete sign-in info), restart the application,   and sign-in with the username and app password. Follow the steps documented : http://technet.microsoft.com/library/dn270518.aspx#apppassword.


		Watch a video showing these steps at http://g.microsoftonline.com/1AX00en/175.

		Best Regards,
		Your Administrator

## 次のステップ
クラウドでの多要素認証のセットアップが完了したので、「[Azure Multi-Factor Authentication の構成](multi-factor-authentication-whats-next.md)」に移動して次の手順に移動できます。 そこでは、レポート、不正行為のアラート、音声メッセージのカスタマイズ、および Azure Multi-Factor Authentication が提供するすべての機能について学びます。

<!---HONumber=AcomDC_0114_2016-->