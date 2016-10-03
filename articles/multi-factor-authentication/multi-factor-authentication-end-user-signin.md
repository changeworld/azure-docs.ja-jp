<properties
	pageTitle="Azure Multi-Factor Authentication を使用した Azure MFA サインイン エクスペリエンス"
	description="このページでは、Azure MFA で利用可能な各種サインイン方法を確認できる参照先情報を示します。"
	keywords="ユーザー認証, サインイン エクスペリエンス, 携帯電話でのサインイン, 会社の電話でのサインイン"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication を使用したサインイン エクスペリエンス
> [AZURE.NOTE]  このページで提供する次のドキュメントでは、一般的なサインイン エクスペリエンスを示します。サインインのヘルプについては、「[Azure Multi-Factor Authentication での問題](multi-factor-authentication-end-user-manage-settings.md)」を参照してください。



## サインインのときにどんなことが生じますか。
サインインの方法、および多要素認証の使用法によって、エクスペリエンスは異なります。このセクションでは、サインインするときに想定される事柄について記します。お客さまの操作内容に最も近いものを選択してください。


操作内容|Description
:------------- | :------------- |
[携帯電話または会社電話を使用してサインインする](#signing-in-with-mobile-or-office-phone) | 携帯電話または会社電話を使用してサインインする際の操作について説明します。
[通知を使用して Microsoft Authenticator アプリでサインインする](#signing-in-with-the-microsoft-authenticator-app-using-notification) | Microsoft Authenticator アプリを通知と一緒に使用する際の操作について説明します。
[確認コードを使用して Microsoft Authenticator アプリでサインインする](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|Microsoft Authenticator アプリを確認コードと一緒に使用する際の操作について説明します。
[別の方法を使用してサインインする](#signing-in-with-an-alternate-method)|別の方法を使用する場合の操作について説明します。

## 携帯電話または会社電話を使用してサインインする

多要素認証で携帯電話または会社電話を使用した場合のエクスペリエンスについて以下に記します。

### 会社の電話または携帯電話への呼び出しを使用してサインインする方法

- Office 365 などのアプリケーションまたはサービスにユーザー名とパスワードを使用してサインインします。
- Microsoft からの呼び出しがあります。

![Microsoft が呼び出す](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- 電話に出て、# キーを押します。

![リダイレクト先](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- これでサインインできます。</li>

## 通知を使用して Microsoft Authenticator アプリでサインインする

多要素認証で Microsoft Authenticator アプリを使用した場合、通知が送信されたときのエクスペリエンスについて以下に記します。

### Microsoft Authenticator アプリから送信される通知を使用してサインインするには

- Office 365 などのアプリケーションまたはサービスにユーザー名とパスワードを使用してサインインします。
- Microsoft から通知が送信されます

![Microsoft が通知を送信する](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- 電話に出て、確認キーを押します。PIN の入力が必要な企業の場合は、ここで入力を求められます。

![確認](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- これでサインインできます。


## 確認コードを使用して Microsoft Authenticator アプリでサインインする

多要素認証で Microsoft Authenticator アプリを使用した場合、確認コードを使用するときのエクスペリエンスについて以下に記します。

### Microsoft Authenticator アプリと確認コードを使用してサインインするには

- Office 365 などのアプリケーションまたはサービスにユーザー名とパスワードを使用してサインインします。
- Microsoft によって確認コードを求めるプロンプトが表示されます。

![検証コードを入力する](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- お客様の電話で Microsoft Authenticatior アプリを開き、サインインのためのボックスにコードを入力します。

![コードを取得する](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- これでサインインできます。


## 別の方法を使用してサインインする


次のセクションでは、主要な方法が使用できないときに、別の方法でサインインする方法を示します。

### 別の方法でサインインする方法

- Office 365 などのアプリケーションまたはサービスにユーザー名とパスワードを使用してサインインします。
- [別の確認オプションを使用する] を選択します。各種オプションが選択項目として示されます。オプションの数は、セットアップ済みの数によって異なります。

![別の方法を使用する](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- 代替方法を選択し、サインインします。

<!---HONumber=AcomDC_0921_2016-->