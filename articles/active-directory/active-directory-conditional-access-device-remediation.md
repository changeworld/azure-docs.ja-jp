<properties
	pageTitle="トラブルシューティング: 目的の場所にアクセスできない | Microsoft Azure"
	description="このトピックは、目的のアプリケーションにアクセスするために、実行できる修復手順があるかどうかを判断するのに役立ちます。"
	services="active-directory"
	keywords="デバイス ベースの条件付きアクセス, デバイス登録, デバイス登録の有効化, デバイス登録と MDM"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/23/2016"
	ms.author="markvi"/>


# トラブルシューティング: 目的の場所にアクセスできない

Office 365 SharePoint Online などのアプリケーションにアクセスする際にアクセス拒否ページが表示された場合は、どうすればよいのでしょうか。

このガイドは、目的のアプリケーションにアクセスするために、実行できる修復手順があるかどうかを判断するのに役立ちます。



デバイスで実行されているデバイス プラットフォームは何ですか。 この質問に対する回答によって、このトピック内の適切なセクションが決まります。
 

-	Windows デバイス

-	iOS デバイス (iPhone または iPad)

-	Android デバイス

## Windows デバイスからのアクセス

デバイスが Windows 10、Windows 8.1、Windows 8.0、Windows 7、Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 のいずれかの場合は、アプリケーションにアクセスしようとしたときに表示されたページを確認することで、適切な原因を特定します。

### デバイスが登録されていない

デバイスが Azure Active Directory に登録されていないものの、アプリケーションがデバイス ベースのポリシーで保護されている場合、次の内容のページが表示される可能性があります。

![シナリオ](./media/active-directory-conditional-access-device-remediation/01.png "シナリオ")

 

デバイスが組織の Active Directory ドメインに参加している場合は、次の手順を試すことができます。

1.	職場アカウント (Active Directory アカウント) を使用して Windows にサインインしていることを確認します。

2.	VPN または直接アクセスを使用して企業ネットワークに接続します。

3.	接続したら、Windows キーと L キーを使用して Windows セッションをロックします。

4.	職場アカウントの資格情報を入力して、Windows セッションのロックを解除します。

5.	しばらく待ってから、アプリケーションにもう一度アクセスしてみます。

6.	同じページが表示された場合は、管理者に連絡し、[詳細] リンクをクリックすると表示される詳細情報をお伝えください。

デバイスがドメインに参加しておらず、Windows 10 を実行している場合、次の 2 つの選択肢があります。

1. Azure AD Join を実行する。
2. 職場または学校アカウントを Windows に追加する。

この 2 つの違いについては、「[職場での Windows 10 デバイスの使用](active-directory-azureadjoin-windows10-devices.md)」を参照してください。

Azure AD Join を実行するには、次の手順を実行します (Windows Phone では実行できません)。

**Windows 10 Anniversary Update**

1.	設定アプリを起動します。

2.	[アカウント]、[Access work or school (職場または学校へのアクセス)] の順に移動します。

3.	[接続] をクリックします。

4.	ページの下部にある [Join this device to Azure AD (このデバイスを Azure AD に参加させる)] を選択します。

5.	組織に対して認証し、必要に応じて MFA を証明するものを提示して、完了するまで手順に従います。

6.	サインアウトしてから、職場アカウントを使用してサインインします。

7.	アプリケーションにもう一度アクセスしてみます。




**Windows 10 November 2015 Update**


1.	設定アプリを起動します。

2.	[システム]、[バージョン情報] の順に移動します。
	
3.	[Azure AD に参加] をクリックします。

4.	組織に対して認証し、必要に応じて MFA を証明するものを提示して、完了するまで手順に従います。

5.	サインアウトしてから、職場アカウント (Azure AD アカウント) を使用してサインインします。

6.	アプリケーションにもう一度アクセスしてみます。


職場または学校アカウントを追加するには、次の手順を実行します。

**Windows 10 Anniversary Update**

1.	設定アプリを起動します。

2.	[アカウント]、[Access work or school (職場または学校へのアクセス)] の順に移動します。

3.	[接続] をクリックします。

4.	組織に対して認証し、必要に応じて MFA を証明するものを提示して、完了するまで手順に従います。

5.	アプリケーションにもう一度アクセスしてみます。


**Windows 10 November 2015 Update**
	
1.	設定アプリを起動します。
2.	[アカウント]、[Your accounts (自分のアカウント)] の順に移動します。
3.	[Add work or school account (職場または学校アカウントを追加)] をクリックします。
4.	組織に対して認証し、必要に応じて MFA を証明するものを提示して、完了するまで手順に従います。
5.	アプリケーションにもう一度アクセスしてみます。

デバイスがドメインに参加しておらず、Windows 8.1 を実行している場合は、Workplace Join を実行し、次の手順に従って、Microsoft Intune に登録できます。

1.	PC 設定を起動します。

2.	[ネットワーク]、[社内] に移動します。

3.	[参加] をクリックします。

4.	組織に対して認証し、必要に応じて MFA を証明するものを提示して、完了するまで手順に従います。

5.	[オン] をクリックします。

6.	完了するまで待ちます。

7.	アプリケーションにもう一度アクセスしてみます。


## サポートされていないブラウザー

次のブラウザーからアプリケーションにアクセスすると、次に示すようなページが表示されます。

1.	Windows 10 または Windows Server 2016 の場合、Chrome や Firefox など、Microsoft Edge および Microsoft Internet Explorer 以外のブラウザー。

2.	Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 の場合、Firefox。
 

![シナリオ](./media/active-directory-conditional-access-device-remediation/02.png "シナリオ")


唯一の修復方法は、デバイス プラットフォームでサポートされているブラウザーを使用することです。

## iOS デバイスからのアクセス

iPhone または iPad の手順については、後日このページでもう一度ご確認ください。

## Android デバイスからのアクセス

Android フォンまたはタブレットの手順については、後日このページでもう一度ご確認ください。


## 次のステップ

[Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0824_2016-->