<properties
	pageTitle="Azure Multi-factor Authentication で初めてサインインする"
	description="このページでは、初めてサインインするときのユーザー エクスペリエンスについて説明します。"
	services="multi-factor-authentication"
	keywords="azure ディレクトリの使用方法, クラウドの active directory, active directory のチュートリアル"
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
# Azure Multi-Factor Authentication のセットアップ エクスペリエンス

 管理者が、お客様の ID を検証するためにパスワードと電話からの応答の両方が必要となるようにアカウントを構成した場合、追加のセキュリティ確認設定が使用されます。追加のセキュリティ確認が必要となるように管理者がアカウントを構成した場合には、**自動登録プロセスが完了するまではサインインすることができません**。

## 多要素認証を使用する方法の決定

 アカウントの構成後に初めてサインインすると、自動録プロセスを開始するように求めるプロンプトが表示されます。このプロセスを開始するには、**[今すぐセットアップ]** をクリックします。

![セットアップ](./media/multi-factor-authentication-end-user-first-time/first.png)

登録プロセスを使用する場合、確認に使用する好きなメソッドを指定できます。以下の表のいずれかを指定できます。チュートリアルを含む追加情報については、いずれかのメソッドをクリックしてください。

メソッド|Description
:------------- | :------------- |
[携帯電話呼び出し](multi-factor-authentication-end-user-first-time-mobile-phone.md)| 認証電話に自動音声通話を行います。ユーザーは、呼び出しに応答し、電話のキーパッドの # を押して認証を行います。この電話番号は、オンプレミスの Active Directory には同期されません。
[携帯電話のテキスト メッセージ](multi-factor-authentication-end-user-first-time-mobile-phone.md)|ユーザーに確認コードを含むテキスト メッセージが送信されます。ユーザーには、確認コードを使用してテキスト メッセージに返信するか、確認コードをサインイン インターフェイスに入力するよう求めるプロンプトが表示されます。
[会社電話呼び出し](multi-factor-authentication-end-user-first-time-office-phone.md)|ユーザーに自動音声通話を行います。ユーザーは、呼び出しに応答し、電話のキーパッドの # を押して認証を行います。
[モバイル アプリ](multi-factor-authentication-end-user-first-time-mobile-app.md)|ユーザーのスマート フォンまたはタブレット上の Microsoft Authenticator アプリに通知をプッシュします。ユーザーは、アプリで [確認] をタップして認証を行います。または、アプリを OTP トークンとして使用してオフライン認証を行うこともできます。ユーザーは、サインイン画面にトークンを入力して認証を行います。<br><p> Microsoft Authenticator アプリは、多要素認証サービスが提供する追加のセキュリティを実現するため 2 種類のモードで動作します。次にそれらのモードを記します。<li>**通知** - このモードの場合、Microsoft Authenticator アプリはアカウントに対する未承認のアクセスを防止し、不正なトランザクションを停止します。電話または登録されたデバイスに対するプッシュ通知によって行われます。通知を確認し、適切である場合は [認証] を選択するだけです。または、[拒否] を選択することもできます。あるいは、[拒否] を選択して不正通知を報告することも可能です。不正通知の報告方法については、「Multi-Factor Authentication における [認証を拒否して不正を通報] 機能の使用法」をご覧ください。</li><p><li>**ワンタイム パスワード** - このモードでは、Microsoft Authenticator アプリを OATH 確認コードを生成するためのソフトウェア トークンとして使用できます。この確認コードにより、ユーザー名とパスワードと共に入力し、2 番目の形式の認証が行われます。</li><br><p> Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。

<!---HONumber=AcomDC_0921_2016-->