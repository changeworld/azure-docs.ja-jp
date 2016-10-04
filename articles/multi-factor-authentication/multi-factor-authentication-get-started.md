<properties 
	pageTitle="Azure Multi-Factor Authentication の概要"
	description="保護しようとしている対象とユーザーが位置する場所を尋ねることで、ユーザーに最適な多要素認証セキュリティ ソリューションを選択します。次に、クラウド、MFA Server、または AD FS を選択します。"
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
	ms.topic="get-started-article"
	ms.date="08/15/2016"
	ms.author="kgremban"/>

#ユーザーに適した多要素のセキュリティ ソリューションの選択

さまざまな種類の Azure Multi-Factor Authentication があるため、使用に適したバージョンを把握するには、2 つの要素を判別する必要があります。その要素とは次のとおりです。

-	<a href="#-1">セキュリティで保護しようとしている対象</a>
-	<a href="#-2">ユーザーが位置する場所</a>

次のセクションでは、この判断についてガイダンスを提供します。

<h2 id="-1">セキュリティで保護しようとしている対象</h2>

適切な多要素認証ソリューションを判別するには、まず 2 番目の認証方法で保護しようとしている対象に関する疑問に答える必要があります。Azure のアプリケーションですか。 またはリモート アクセス システムなどですか。何をセキュリティで保護しようとしているかを判断すると、多要素認証を有効にする必要がある場所に関する疑問に答えることができます。


セキュリティで保護しようとしている対象| クラウドでの Multi-Factor Authentication|Multi-Factor Authentication Server
------------- | :-------------: | :-------------: |
ファースト パーティの Microsoft アプリ|* |* |
アプリ ギャラリー内の SaaS アプリ|* |* |
Azure AD アプリケーション プロキシ経由で公開される IIS アプリケーション|* |* |
Azure AD アプリケーション プロキシ経由で公開されない IIS アプリケーション | |* |
VPN、RDG などのリモート アクセス| |* |



<h2 id="-2">ユーザーが配置される場所</h2>

次に、ユーザーが配置される場所に応じて、使用する適切なソリューションを判断できます。これは、クラウドでの多要素認証か、MFA Server を使用するオンプレミスの多要素認証のどちらかです。



ユーザーの位置| 解決策
------------- | :------------- |
Azure Active Directory| クラウドでの Multi-Factor Authentication|
AD FS によるフェデレーションを使用した Azure AD とオンプレミスの AD| クラウドでの MFA と MFA Server の両方を使用可能
DirSync を使用する Azure AD とオンプレミスの AD、Azure AD Sync、Azure AD Connect。パスワード同期なし|クラウドでの MFA と MFA Server の両方を使用可能
DirSync を使用する Azure AD とオンプレミスの AD、Azure AD Sync、Azure AD Connect。パスワード同期あり|クラウドでの Multi-Factor Authentication
オンプレミスの Active Directory|Multi-Factor Authentication Server

次の表ではクラウドでの Multi-Factor Authentication の機能と、Multi-Factor Authentication Server の機能を比較しています。

 | クラウドでの Multi-Factor Authentication | Multi-Factor Authentication Server
------------- | :-------------: | :-------------: |
2 番目の要素としてのモバイル アプリ通知 | ● | ● |
2 番目の要素としてのモバイル アプリ確認コード | ● | ●
第 2 要素としての音声通話 | ● | ●
第 2 要素としての単方向 SMS | ● | ●
第 2 要素としての双方向 SMS | | ●
第 2 要素としてのハードウェア トークン | | ●
MFA をサポートしていないクライアントのアプリ パスワード | ● |  
認証方法の管理制御 | ● | ●
PIN モード | | ●
不正アクセスのアラート | ● | ●
MFA レポート | ● | ●
ワンタイム バイパス | | ●
音声通話のカスタムあいさつ文 | ● | ●
音声通話のカスタマイズ可能な発信元 ID | ● | ●
信頼できる IP | ● | ●
信頼済みデバイスの MFA の記憶 | ● |  
条件付きアクセス | ● | ●
キャッシュ | | ●

クラウドの多要素認証を使用するか、オンプレミスの MFA Server を使用するかを決定しました。これで、Azure Multi-Factor Authentication の設定と使用を開始できます。**適したシナリオのアイコンを選択してください。**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

<!---HONumber=AcomDC_0928_2016-->