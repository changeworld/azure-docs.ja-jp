<properties
	pageTitle="Microsoft Authenticator アプリに関する FAQ"
	description="Microsoft 認証アプリと Multi-factor Authentication に関してよく寄せられる質問の一覧を提供します。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="pblachar, librown"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="kgremban"/>

# Microsoft Authenticator アプリケーションに関する FAQ

Microsoft Authenticator アプリは、Azure Authenticator アプリに置き換わり、Azure 多要素認証を使用するときに推奨されるアプリです。このアプリは、Windows Phone、Android、iOS で利用できます。

## よく寄せられる質問

- **セキュリティ コードに Microsoft Authenticator アプリケーションを既に使用しています。ワンクリック プッシュ通知に切り替えるにはどうすればよいですか。**

	Microsoft アカウントを個人のアカウントに使用していて、プッシュ通知に切り替える必要がある場合は、自分のアカウントをもう一度追加する必要があります。これは、このアプリではワンタイム パスワードを使用しているためです。デバイスを自分のアカウントで再登録し、プッシュ通知を設定します。

	自分のアカウントで 2 段階認証が有効になっていない場合は、「[2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)」を参照して、2 段階認証が目的に合っているかどうかを判断してください。

- **iPhone または iPad でワンクリック プッシュ通知を使用できるようになるのはいつですか。**

	この機能は、Microsoft アカウントに一般提供される 8 月末までベータ版です。ベータ プログラムに参加するには、msauthenticator@microsoft.com にメールを送信してください。名、姓、および Apple ID をメッセージに含めてください。

- **ワンクリック プッシュ通知は Microsoft 以外のアカウントでも機能しますか。**

	残念ですが、プッシュ通知は、Microsoft アカウントと Azure Active Directory アカウントでのみ機能します。職場または学校で Azure AD アカウントを使用している場合は、この機能を無効にしている可能性があります。

- **デバイスをバックアップから復元しましたが、アカウント コードが見つからないか、機能していません。なぜでしょうか。**

	セキュリティのため、現時点ではアプリのバックアップからアカウントは復元されません。iOS アプリをバックアップから復元した場合、アカウントは引き続き表示されますが、サインインの検証を受け取ることも、セキュリティ コードを生成することもできません。アプリケーションを復元したら、アカウントを削除し、もう一度追加してください。

## 関連トピック

- [Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)](multi-factor-authentication-faq.md)
- Microsoft アカウントの [2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
- [認証アプリに関する FAQ](https://support.microsoft.com/help/12414/microsoft-account-identity-verification-apps-faq)

<!---HONumber=AcomDC_0921_2016-->