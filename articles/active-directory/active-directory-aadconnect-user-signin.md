<properties
	pageTitle="Azure AD Connect - ユーザー サインイン | Microsoft Azure"
	description="Azure AD Connect ユーザー サインインのカスタム設定"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="billmath"/>



# Azure AD Connect ユーザーのサインオン オプション

Azure AD Connect では、ユーザーは同じパスワードを使用して、クラウドとオンプレミス両方のリソースにサインオンできます。これを有効にするには、複数あるさまざまな方法から選択することができます。


### パスワードの同期
パスワードの同期では、ユーザーのパスワードのハッシュが、オンプレミスの Active Directory から Azure AD に同期されます。オンプレミスでパスワードが変更またはリセットされると、新しいパスワードは Azure AD にすぐに同期され、ユーザーは常にクラウドのリソースでもオンプレミスと同じパスワードを使用できます。パスワードがクリア テキストの状態で Azure AD に送信されたり Azure AD に格納されたりすることはありません。パスワードの同期とパスワードの書き戻しを組み合わせて使用すると、Azure AD でセルフ サービス パスワードのリセットを有効にすることができます。

<center>![クラウド](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[パスワードの同期の詳細について](https://msdn.microsoft.com/library/azure/dn246918.aspx)


### Windows Server 2012 R2 ファームで新規または既存の AD FS を使用するフェデレーション
フェデレーション サインオンを設定すると、ユーザーは自分のオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインオンすることができます。また、企業ネットワークにアクセスしているときはパスワードを再入力する必要はありません。AD FS を使用するフェデレーション オプションでは、新しい AD FS をデプロイすることも、Windows Server 2012 R2 ファームの既存の AD FS を指定することもできます。既存のファームを指定する場合は、ユーザーがサインオンできるように、Azure AD Connect によってファームと Azure AD の間の信頼が構成されます。

<center>![クラウド](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### Windows Server 2012 R2 で AD FS を使用するフェデレーションをデプロイするには、以下が必要です。
新しいファームをデプロイする場合:

- フェデレーション サーバー用の Windows Server 2012 R2 サーバー。
- Web アプリケーション プロキシ用の Windows Server 2012 R2 サーバー。
- fs.contoso.com などの目的のフェデレーション サービス名に対する SSL 証明書を 1 つ含む .pfx ファイル。

新しいファームをデプロイするか、既存のファームを使用する場合:

- フェデレーション サーバー上のローカル管理者の資格情報。
- Web アプリケーション プロキシ ロールをデプロイするワークグループ (ドメインに参加していない) サーバー上のローカル管理者の資格情報。
- ウィザードを実行するコンピューターは、Windows リモート管理を使用して AD FS または Web アプリケーション プロキシをインストールする他のコンピューターに接続できる必要があります。

#### 以前のバージョンの AD FS またはサード パーティのソリューションを使用するサインオン
クラウド サインオンを以前のバージョンの AD FS (AD FS 2.0 など) またはサード パーティのフェデレーション プロバイダーを使用して既に構成している場合は、Azure AD Connect でのユーザーのサインインの構成をスキップすることができます。これにより、既存のソリューションをサインオンに使用しながら、最新の同期と Azure AD Connect のその他の機能を使用することができます。

### 組織のユーザーのサインイン方法の選択
多くの組織においては Office 365、SaaS アプリケーション、およびその他の Azure AD ベースのリソースへのユーザー サインオンを可能にするのみなので、その場合は既定のパスワードの同期オプションをお勧めします。ただし、組織によっては AD FS などフェデレーション サインオン オプションを使用する必要があります。次のような理由が考えられます。

- 組織は既に AD FS またはサード パーティのフェデレーション プロバイダーをデプロイしている
- セキュリティ ポリシーで、クラウドへのパスワード ハッシュ同期を禁止している
- 企業ネットワーク上のドメイン参加マシンからクラウドのリソースにアクセスするときに、ユーザーにとってシームレスな SSO (パスワードの再入力を要求しない) を実現する必要がある
- 次のような AD FS の特定の機能が必要である
	- サード パーティ プロバイダーまたはスマート カードを使用するオンプレミスのMulti-Factor Authentication (Windows Server 2012 R2 の AD FS 用のサード パーティの MFA プロバイダーについての説明を参照)
	- ソフト アカウント ロックアウトや AD パスワードと作業時間のポリシーなどの Active Directory の統合機能
	- デバイス登録、Azure AD 参加、または Intune MDM ポリシーを使用するオンプレミスとクラウド両方のリソースへの条件付きアクセス

<!---HONumber=Oct15_HO3-->