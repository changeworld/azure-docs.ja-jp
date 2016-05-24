<properties 
	pageTitle="クラウドでの Microsoft Azure Multi-Factor Authentication の概要" 
	description="クラウドで Azure MFA を開始する方法について説明する Microsoft Azure Multi-Factor Authentication のページです。" 
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
	ms.date="05/12/2016" 
	ms.author="billmath"/>

# クラウドでの Azure Multi-Factor Authentication Server の概要
次の記事では、クラウドで Azure Multi-Factor Authentication の使用を開始する方法について説明します。

> [AZURE.NOTE]  次のドキュメントでは、**Azure クラシック ポータル**を使用してユーザーを有効にする方法について説明しています。Office 365 ユーザー向けに Azure Multi-Factor Authentication を設定する方法についての情報をお探しの場合は、[Office 365 の多要素認証の設定](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=ja-JP&rs=ja-JP&ad=US)に関するページを参照してください。

![MFA in the Cloud](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## 前提条件
ユーザーに対して Azure Multi-Factor Authentication を有効にするには、次の前提条件を満たす必要があります。




- [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/pricing/free-trial/) - Azure サブスクリプションをまだ取得していない場合は、サインアップが必要です。Azure MFA を開始して使ってみるだけの場合、評価版のサブスクリプションを使用できます。
2. [Multi-Factor Auth プロバイダーを作成](multi-factor-authentication-get-started-auth-provider.md)して自分のディレクトリに割り当てるか、[ライセンスをユーザーに割り当てる](multi-factor-authentication-get-started-assign-licenses.md) 

> [AZURE.NOTE]  ライセンスの使用は、Azure MFA、Azure AD Premium、または Enterprise Mobility Suite (EMS) を所有するユーザーが対象となります。MFA は Azure AD Premium と EMS に含まれています。ライセンスが足りている場合は、認証プロバイダーの作成は不要です。
		

## ユーザーの多要素認証を有効にする
ユーザーの多要素認証を有効にするには、ユーザーの状態を無効から有効に変更するだけです。ユーザーの状態の詳細については、「[User States in Azure Multi-Factor Authentication (Azure Multi-Factor Authentication でのユーザーの状態)](multi-factor-authentication-get-started-user-states.md)」を参照してください。

ユーザーの MFA を有効にするには、次の手順を使用します。

### 多要素認証を有効にするには
--------------------------------------------------------------------------------
1.  **Azure クラシック ポータル**に管理者としてサインインします。
2.  左側の **[Active Directory]** をクリックします。
3.  **[ディレクトリ]** の下で、有効にするユーザーのディレクトリをクリックします。![Click Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  上部の **[ユーザー]** をクリックします。
5.  ページ下部の **[Multi-Factor Auth の管理]** をクリックします。![Click Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  これにより、ブラウザーの新しいタブが開きます。多要素認証を有効にするユーザーを検索します。上部でビューを変更することが必要になる場合があります。状態が**無効**になっていることを確認します。![Enable user](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  ユーザーの名前の横にあるチェック ボックスを**オン**にします。
7.  右側の **[有効化]** をクリックします。![Enable user](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  **[Multi-Factor Auth を有効にする]** をクリックします。![Enable user](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  ユーザーの状態が**無効**から**有効**に変更されたことがわかります。![Enable Users](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  有効にした後、電子メールでユーザーに通知することをお勧めします。ロックアウトされないよう、ブラウザー以外のアプリケーションの使い方も伝えるようにしてください。


## PowerShell を使用して多要素認証の有効化を自動化する

[Azure AD PowerShell](powershell-install-configuremd) を使用して[状態](multi-factor-authentication-whats-next.md)を変更するには、次のようにします。`$st.State` は、次のいずれかの状態と一致するように変更できます。


- 有効
- 適用
- 無効  

> [AZURE.IMPORTANT]  Disabled の状態から Enforced の状態に直接変更した場合は、ユーザーによる MFA 登録と[アプリ パスワード](multi-factor-authentication-whats-next.md#app-passwords)の取得が行われていないため、最新ではない認証クライアントは処理を停止するという点に注意してください。お使いの認証クライアントが最新ではなく、アプリ パスワードを必要とする場合は、Disabled から Enabled に状態を変更することをお勧めします。これにより、ユーザーはアプリ パスワードの登録と取得を行うことができます。
		
		$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
		$st.RelyingParty = "*"
		$st.State = “Enabled”
		$sta = @($st)
		Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

ユーザーを一括で有効にする方法の 1 つとして、PowerShell を使用します。現在、Azure ポータルには一括有効化の機能がないため、各ユーザーを個別に選択する必要があります。そのため、ユーザーの数が多いと作業量は膨大になります。上記のように PowerShell スクリプトを作成することで、ユーザーのリストをループ処理して各ユーザーを有効にすることができます。たとえば次のようになります。
    
    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
    	$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
    	$st.RelyingParty = "*"
    	$st.State = “Enabled”
    	$sta = @($st)
    	Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


ユーザーの状態の詳細については、「[User States in Azure Multi-Factor Authentication (Azure Multi-Factor Authentication でのユーザーの状態)](multi-factor-authentication-get-started-user-states.md)」を参照してください。

## 次のステップ
クラウド側の多要素認証を設定したら、デプロイ側の構成とセットアップを行います。「Azure Multi-Factor Authentication の構成」を参照してください。

<!---HONumber=AcomDC_0518_2016-->