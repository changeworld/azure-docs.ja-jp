<properties
	pageTitle="アプリケーション プロキシによるハイブリッド アクセスの有効化 | Microsoft Azure"
	description="Azure Active Directory 経由でネットワークの外部から、プライベート ネットワーク内で実行されているアプリケーションにアクセスできるようにします。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="femila"/>

# アプリケーション プロキシによるハイブリッド アクセスの有効化
Microsoft Azure Active Directory (AD) アプリケーション プロキシを使用すると、プライベート ネットワーク内に置かれたアプリケーションへの、任意の場所および任意のデバイスからのアクセスを安全に許可することができます。アプリケーション プロキシ コネクタは、環境内にインストールすると、Azure AD で簡単に構成することができます。

Web アプリケーションへのアクセスを有効にするための要件は 1 つだけです。それは、コネクタがインストールされているサーバーに Web アプリケーションがアクセスできる必要があるということです。つまり、コネクタが Web アプリケーションにアクセスできるのであれば、コネクタをアプリケーション サーバー自体にも、環境内の他のサーバーにもインストールすることができます。

##動作のしくみ
### 簡単な概要
1. コネクタは、オンプレミスのネットワークにデプロイされます。(冗長性とスケールのために複数のコネクタをデプロイすることができます)。
2. コネクタはクラウド サービスに接続されます。
3. コネクタとクラウド サービスはユーザー トラフィックをアプリケーションにルーティングします。

 ![Azure AD アプリケーション プロキシの図](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

### 詳細
1. ユーザーがアプリケーション プロキシ経由でアプリケーションにアクセスすると、認証のための Azure AD ログオン ページが表示されます。
2. ログオンに成功すると、トークンが生成され、ユーザーに送信されます。
3. ユーザーはここでアプリケーション プロキシにトークンを送信します。アプリケーション プロキシは、そのトークンからユーザー プリンシパル名 (UPN) とセキュリティ プリンシパル名 (SPN) を取得し、コネクタに要求を送信します。
4. コネクタは、ユーザーの代理として内部 (Windows) 認証に使用できる Kerberos チケットを要求します。これは Kerberos の制約付き委任として知られています。
5. Kerberos チケットは Active Directory から取得されます。
6. チケットは、アプリケーション サーバーに送信されて検証されます。
7. 応答がアプリケーション プロキシ経由でエンドユーザーに送信されます。

## 関連記事
- [Azure AD アプリケーション プロキシの有効化](active-directory-application-proxy-enable.md#step-1-enable-application-proxy-in-azure-ad)
- [Azure AD アプリケーション プロキシ経由でアプリケーションを公開する](active-directory-application-proxy-publish.md)

<!---HONumber=Oct15_HO4-->