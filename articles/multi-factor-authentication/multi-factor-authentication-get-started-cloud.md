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
	ms.date="08/24/2015"
	ms.author="billmath"/>

# クラウドでの Azure Multi-Factor Authentication Server の概要



<center>![Cloud](./media/multi-factor-authentication-get-started-cloud/cloud2.png)</center>

クラウドで多要素認証を使用することを決めたので、早速開始してみましょう。 Multi-Factor Authentication for Office 365 または Multi-Factor Authentication for Azure Administrators を使用している場合、手順 3 にスキップできます。また、このドキュメントでは、次に取り組みます。


1. **Azure サブスクリプションへのサインアップ**
	- Azure サブスクリプションをまだ取得していない場合、サインアップする必要があります。Azure MFA を開始して使い勝手を調べるだけの場合、評価版のサブスクリプションを使用できます。
2. **Multi-Factor Auth Provider を作成するか、Azure AD Premium または Enterprise Mobility Suite ライセンスをユーザーに割り当てます**
	- Azure Multi-Factor Auth Provider を作成するか、それをディレクトリーに割り当てるか、またはライセンスを Azure AD Premium ユーザーまたは EMS ユーザーに割り当てます。Azure Multi Factor Authentication は Azure Active Directory Premium に含まれるため、Enterprise Mobility Suite にも含まれることになります。Azure AD Premium または EMS がある場合、Multi-Factor Auth Provider を作成する必要はありません。むしろ、Azure AD Premium または EMS ユーザーの MFA を有効にするために、Azure AD Premium または EMS ライセンスをそのユーザーに割り当てる必要があります。こうすれば、管理者は管理ポータルを介して MFA をユーザーに割り当てることができます。ライセンスをユーザーに割り当てる方法については、以下のセクションを参照してください。
3. **ユーザーの Multi-Factor Authentication を有効にする** 
	- Office 365 または Azure ポータルを通じてユーザーの Azure MFA を有効にします。これを行う方法については、以下のセクションを参照してください。
4. **電子メールをエンド ユーザーに送信して MFA に関して通知する**
	- ユーザーがアカウントの多要素認証を有効にした場合、電子メールでそのことを通知するようお勧めします。ユーザーは、次回のサインインの際に処理を完了するよう求められます。これにより、何が期待されているかが分かります。電子メールのテンプレートの例については、以下のセクションを参照してください。



## Azure Multi-Factor Auth Provider の作成
Azure Active Directory テナントを持つグローバル管理者は、既定で多要素認証を使用できます。ただし、すべてのユーザーに多要素認証を拡張する場合、および/またはグローバル管理者が管理ポータル、カスタムの案内応答、およびレポート などの機能を利用できるようにする場合、Multi-Factor Authentication Provider を購入して構成する必要があります。



### Multi-Factor Auth Provider を作成するには
--------------------------------------------------------------------------------

1. Azure ポータルに管理者としてサインインします。
2. 左側で、[Active Directory] を選択します。
3. [Active Directory] ページの上部で [Multi-Factor Authentication Provider] をクリックします。下部にある **[新規]** をクリックします。
4. [App Services] の下の [アクティブな Auth Provider] を選択し、[簡易作成] を選択します。
5. 次のフィールドに入力し、[作成] をクリックします。
	1. 名前 – アクティブな Auth Provider の名前。
	2. 使用モデル – Multi-Factor Authentication Provider の使用モデル。
		- 認証ごと – 認証ごとに課金される購入モデル。通常、アプリケーションで Azure Multi-factor Authentication を使用するシナリオで使用されます。
		- 有効ユーザーごと – 有効ユーザーごとに課金される購入モデル。通常、Office 365 などのシナリオに使用されます。
	2. ディレクトリ – Multi-Factor Authentication Provider に関連付けられている Azure Active Directory テナント。次の点に注意してください。
		- Multi-Factor Auth Provider の作成に、Azure AD ディレクトリは必要はありません。Azure Multi-Factor Authentication Server または SDK のみを使用する計画の場合、これをブランクにすることができます。
		- すべてのユーザーに多要素認証を拡張する場合、および/またはグローバル管理者が管理ポータル、カスタムの案内応答、およびレポート などの機能を利用できるようにする場合、Multi-Factor Auth Provider を Azure AD ディレクトリに関連付ける必要があります。
		- DirSync または AAD Sync は、オンプレミスの Active Directory 環境を Azure AD ディレクトリと同期している場合にのみ必要です。Active Directory のオンプレミスのインスタンスと同期されない Azure AD ディレクトリのみを使用する場合、DirSync または AAD Sync は必要ありません。
		



5. 作成をクリックした後、Multi-Factor Authentication Provider が作成され、「Multi-Factor Authentication Provider は正常に作成されました」というメッセージが表示されます。[OK] をクリックします。

<center>![Cloud](./media/multi-factor-authentication-get-started-cloud/provider.png)</center>
## Azure AD Premium または Enterprise Mobility Suite のライセンスをユーザーに割り当てる

Azure AD Premium または Enterprise Mobility Suite がある場合、Multi-Factor Auth Provider を作成する必要はありません。必要なのは、ユーザー ライセンスの割り当てだけです。これにより、ユーザーの mfa の有効化を開始できます。

### Azure AD Premium または Enterprise Mobility Suite のライセンスを割り当てるには
--------------------------------------------------------------------------------
<ol>

<li>Azure ポータルに管理者としてサインインします。</li>
<li>左側で、**[Active Directory]** を選択します。</li>
<li>[Active Directory] ページで、有効にするユーザーが存在するディレクトリをダブルクリックします。</li>
<li>[ディレクトリ] ページの上部にある **[ライセンス]** を選択します。</li>
<li>[ライセンス] ページで、[Active Directory Premium] または [Enterprise Mobility Suite] を選択し、**[割り当て]** をクリックします。</li>

<center>![Cloud](./media/multi-factor-authentication-get-started-cloud/license.png)</center>

<li>ダイアログ ボックスで、ライセンスを割り当てるユーザーを選択し、チェック マーク アイコンをクリックして変更を保存します。</li>



## ユーザーの多要素認証を有効にする

Azure Multi-factor Authentication のユーザー アカウントには、次の 3 つの異なる状態があります。

状態 | 説明 |非ブラウザー アプリに影響があるか| メモ 
:-------------: | :-------------: |:-------------: |:-------------: |
無効 | 新しいユーザーの既定の状態は、多要素認証に登録されていません。|いいえ|ユーザーは現在、多要素認証を使用していません。
有効 |ユーザーは多要素認証に登録されています。|いいえ。これらは登録プロセスが完了するまで機能し続けます。|ユーザーは有効ですが、登録プロセスが完了していません。次回サインインするときにプロセスを完了するよう求められます。
適用|ユーザーが登録されており、多要素認証を使用するための登録プロセスが完了しています。|はい。アプリ パスワードが作成され、使用されるまでは機能しません。 | ユーザーは登録を完了していない可能性があります。登録プロセスが完了している場合、多要素認証を使用しています。登録プロセスが完了していない場合、次回サインインするときにプロセスを完了するよう求められます。
Auth Provider を用意したかユーザーにライセンスを割り当てたので、次に、ディレクトリのユーザーの mfa を有効にします。ユーザーの mfa を有効にするには、次の手順を使用します。

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

<!---HONumber=August15_HO9-->