<properties
	pageTitle="ファイアウォールの背後にあるキー コンテナーへのアクセス | Microsoft Azure"
	description="ファイアウォールの背後にあるアプリケーションからキー コンテナーにアクセスする方法について説明します。"
	services="key-vault"
	documentationCenter=""
	authors="amitbapat"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/13/2016"
	ms.author="ambapat"/>

# ファイアウォールの背後にあるキー コンテナーへのアクセス
### Q: Key Vault クライアント アプリケーションをファイアウォールの背後に配置する必要があります。キー コンテナーへのアクセスを有効にするために開く必要があるポート、ホスト、IP アドレスを教えてください。

キー コンテナーにアクセスするには、Key Vault クライアント アプリケーションが、各種の機能のために複数のエンドポイントにアクセスできる必要があります。

- 認証 (Azure Active Directory 経由)
- Azure Resource Manager を使用したキー コンテナーの管理 (作成/読み取り/更新/削除のほか、アクセス ポリシーの設定も含む)
- キー コンテナー自体に格納されているオブジェクト (キーとシークレット) へのアクセスと管理は、キー コンテナー固有のエンドポイントを経由して行われます (例: [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net))。

実際の構成と環境に応じて、いくつかのバリエーションがあります。

## ポート

認証、管理、データ プレーン アクセスの 3 つの機能すべてに関して、Key Vault への全トラフィックはポート 443 (HTTPS) を経由します。ただし CRL については、トラフィックがポート 80 (HTTP) を経由する場合があります。OCSP がサポートされているクライアントは、CRL には到達しませんが、[http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl) に到達する場合があります。

## 認証

Key Vault クライアント アプリケーションは、認証用の Azure Active Directory エンドポイントにアクセスする必要があります。使用されるエンドポイントは、AAD テナント構成とプリンシパルの種類 (ユーザー プリンシパル、サービス プリンシパル、アカウントの種類 (Microsoft アカウントや組織 ID など)) によって異なります。

| プリンシパルの種類 | エンドポイント:ポート |
|----------------|---------------|
| Microsoft アカウントを使用しているユーザー<br> (例: user@hotmail.com) | **グローバル:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login-us.microsoftonline.com:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443<br><br> および <br>login.live.com:443 |
| AAD と共に組織 ID を使用しているユーザー/サービス プリンシパル (例: user@contoso.com) | **グローバル:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login-us.microsoftonline.com:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443 |
| ADFS または他のフェデレーション エンドポイントと共に組織 ID を使用しているユーザー/サービス プリンシパル (例: user@contoso.com) | ADFS と組織 ID 用の上記すべてのエンドポイント、またはその他のフェデレーション エンドポイント |

ほかにも複雑なシナリオが存在する場合があります。詳細については、[Azure Active Directory の認証フロー](/documentation/articles/active-directory-authentication-scenarios/)に関するページ、「[Azure Active Directory とアプリケーションの統合](/documentation/articles/active-directory-integrating-applications/)」、[Active Directory の認証プロトコル](https://msdn.microsoft.com/library/azure/dn151124.aspx)に関するページを参照してください。

## キー コンテナーの管理

キー コンテナーの管理 (CRUD とアクセス ポリシーの設定) のために、Key Vault クライアント アプリケーションは Azure Resource Manager エンドポイントにアクセスする必要があります。

| 操作の種類 | エンドポイント:ポート |
|----------------|---------------|
| Key Vault コントロール プレーン操作<br> (Azure Resource Manager を使用) | **グローバル:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |
| Azure Active Directory Graph API | **グローバル:**<br> graph.windows.net:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> graph.windows.net:443<br><br> **Azure Germany:**<br> graph.cloudapi.de:443 |

## キー コンテナーの操作

すべてのキー コンテナー オブジェクト (キーとシークレット) の管理と暗号化の操作のために、Key Vault クライアントはキー コンテナー エンドポイントにアクセスする必要があります。キー コンテナーの場所によって、エンドポイント DNS サフィックスが異なります。キー コンテナー エンドポイントは、次の表で説明しているように <vault-name>.<region-specific-dns-suffix> の形式になります。

| 操作の種類 | エンドポイント:ポート |
|----------------|---------------|
| キーの暗号化操作、キーとシークレットの作成/読み取り/更新/削除、キー コンテナー オブジェクト (キー/シークレット) に対するタグと他の属性の設定/取得など、キー コンテナーの操作 | **グローバル:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## IP アドレス範囲

Key Vault サービスでは、PaaS インフラストラクチャなどの他の Azure リソースが使用されます。そのため、Key Vault サービス エンドポイントが常に持つことになる、特定の範囲の IP アドレスは提供されません。ただし、ファイアウォールで IP アドレス範囲のみがサポートされている場合は、「[Microsoft Azure Datacenter IP Ranges (Microsoft Azure データセンターの IP 範囲)](https://www.microsoft.com/download/details.aspx?id=41653)」のドキュメントを参照してください。認証と ID (Azure Active Directory) については、アプリケーションは、[認証と ID のアドレス](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)に関する記事に記載されているエンドポイントに接続できる必要があります。

## 次のステップ

- Key Vault に関する質問がある場合は、[Azure Key Vault フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)にアクセスしてください。

<!---HONumber=AcomDC_0921_2016-->