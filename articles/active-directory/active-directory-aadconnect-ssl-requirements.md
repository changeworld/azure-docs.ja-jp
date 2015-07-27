<properties 
	pageTitle="Azure AD Connect - SSL 証明書の要件" 
	description="AD FS を使用する場合の Azure AD Connect の SSL 証明書の要件" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect - SSL 証明書の要件

**重要:** AD FS ファームのすべてのノードだけでなくすべての Web アプリケーション プロキシ サーバーで同じ SSL 証明書を使用することを強くお勧めします。

- この証明書は x509 証明書である必要があります。 
- テスト ラボ環境のフェデレーション サーバーでは自己署名証明書を使用することができます。ただし、実稼働環境では、公的 CA から証明書を入手することをお勧めします。 
	- 公的に信頼されていない証明書を使用する場合は、各 Web アプリケーション プロキシ サーバーにインストールされている証明書がローカル サーバーとすべてのフェデレーション サーバーで信頼されていることを確認します。 
- CryptoAPI Next Generation (CNG) キーとキー記憶域プロバイダーに基づく証明書はサポートされません。つまり、KSP (キー記憶域プロバイダー) ではなく CSP (暗号化サービス プロバイダー) に基づく証明書を使用する必要があります。 
- 証明書の ID は、フェデレーション サービス名 (fs.contoso.com など) と一致する必要があります。 
	- ID は、dNSName タイプのサブジェクト代替名 (SAN) 拡張、または SAN エントリがない場合は共通名として指定されたサブジェクト名のどちらかになります。  
	- 複数の SAN エントリを証明書に表示できますが、そのうちの 1 つはフェデレーション サービス名に一致させます。 
	- 社内参加を使用する場合は、値 "enterpriseregistration." の後に組織のユーザー プリンシパル名 (UPN) サフィックス (enterpriseregistration.contoso.com など) が続く追加の SAN が必要です。 

ワイルドカード証明書がサポートされます。
 

<!---HONumber=July15_HO3-->