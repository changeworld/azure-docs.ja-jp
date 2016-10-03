
<properties
	pageTitle="Azure Multi-Factor Authentication で確認コードを使用したモバイル アプリへのサインイン"
	description="このページでは、Azure MFA でモバイル アプリの確認コードを使用してサインインする方法を説明します。"
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
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication で確認コードを使用したモバイル アプリへのサインイン


多要素認証でモバイル アプリを使用した場合、確認コードを使用するときのエクスペリエンスについて以下に記します。

## モバイル アプリで確認コードを使用してサインインする方法

<ol>

<li>Office 365 などのアプリケーションまたはサービスにユーザー名とパスワードを使用してサインインします。</li>
<li>Microsoft によって確認コードを求めるプロンプトが表示されます。</li>


<center>![Setup](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)</center>

<li>お客様の電話で Azure Authenticatior アプリを開き、サインインのためのボックスにコードを入力します。</li>

<center>![Setup](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)</center>


<li>これでサインインできます。</li>

<!---HONumber=AcomDC_0921_2016-->