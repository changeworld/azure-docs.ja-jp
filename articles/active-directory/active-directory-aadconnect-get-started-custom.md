<properties
	pageTitle="Azure AD Connect: カスタム インストール | Microsoft Azure"
	description="このドキュメントでは、Azure AD Connect のカスタム インストール オプションについて説明します。この手順を使用すると、Azure AD Connect から Active Directory をインストールできます。"
	services="active-directory"
    keywords="Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/22/2016"
	ms.author="billmath;andkjell"/>

# Azure AD Connect のカスタム インストール
次のドキュメントは、Azure AD Connect のカスタム インストール オプションの使用について説明します。このオプションは、追加の構成オプションがある場合や、高速インストールでは設定されないオプションの機能が必要な場合に設定します。

## 関連ドキュメント
[オンプレミスの ID と Azure Active Directory の統合](active-directory-aadconnect.md)に関するドキュメントをまだお読みでない方のために、次の表に関連トピックへのリンクを示します。インストールを開始する前に、太字で表示した最初の 3 つのトピックをお読みいただく必要があります。

| トピック | |
| --------- | --------- |
| **Azure AD Connect のダウンロード** | [Azure AD Connect のダウンロード](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **ハードウェアと前提条件** | [Azure AD Connect: ハードウェアと前提条件](active-directory-aadconnect-prerequisites.md#hardware-requirements-for-azure-ad-connect) |
| **インストールで使用するアカウント** | [Azure AD Connect アカウントとアクセス許可](active-directory-aadconnect-accounts-permissions.md) |
| Express 設定を使用したインストール | [Azure AD Connect の高速インストール](active-directory-aadconnect-get-started-express.md) |
| DirSync からのアップグレード | [Azure AD 同期ツール (DirSync) からのアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| インストール後 | [インストールの確認とライセンスの割り当て](active-directory-aadconnect-whats-next.md) |

## 必須コンポーネントのインストール
同期サービスをインストールするとき、オプションの構成セクションをオフのままにすると、Azure AD Connect によってすべて自動的に設定されます。これには、SQL Server 2012 Express LocalDB インスタンスの設定、適切なグループの作成、およびそれらへのアクセス許可の割り当てが含まれます。既定値を変更する場合、次の表を使用すると、使用できるオプションの構成を理解できます。

![必須コンポーネント](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)

| オプションの構成 | 説明 |
| ------------- | ------------- |
| 既存の SQL Server を使用する | SQL Server 名とインスタンス名を指定することができます。使用するデータベース サーバーが既にある場合は、このオプションを選択します。SQL Server での参照が有効になっておらず、ポート番号を指定する必要がある場合は、**[インスタンス名]** ボックスに、インスタンス名、コンマ、ポート番号の順に入力してください。 |
| 既存のサービス アカウントを使用する | 既定では Azure AD Connect は、使用する同期サービス用のローカル サービス アカウントを作成します。パスワードは自動的に生成され、Azure AD Connect をインストールしているユーザーには不明です。リモート SQL Srver を使用する場合は、ドメインでのサービス アカウントとパスワードが必要です。このような場合は、使用するサービス アカウントを入力します。サービス アカウントのログインを作成するには、SQL の SA がインストールを実行してください。「[Azure AD Connect アカウントとアクセス許可](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation)」を参照してください |
| カスタム同期グループを指定する | 同期サービスがインストールされている場合、既定では、Azure AD Connect によって 4 つのグループがサーバーでローカルに作成されます。これらのグループは。管理者グループ、オペレーター グループ、参照グループ、およびパスワード再設定グループです。独自のグループを指定する場合は、ここから行うことができます。グループは、サーバー上にローカルに存在する必要があり、ドメイン内に置くことはできません。 |

## ユーザーのサインイン
必須コンポーネントをインストールすると、ユーザーが使用するシングル サインオンの方法を指定するように求められます。次の表に、指定できるオプションの簡単な説明を示します。サインイン方法の詳細については、「[ユーザーのサインイン](active-directory-aadconnect-user-signin.md)」を参照してください。

![ユーザーのサインイン](./media/active-directory-aadconnect-get-started-custom/usersignin.png)

シングル サインオン オプション | 説明
------------- | ------------- |
パスワードの同期 |ユーザーは、オンプレミスのネットワークへのログインに使用しているものと同じパスワードを使用して、Microsoft クラウド サービス (Office 365、Dynamics CRM、Windows InTune など) にサインインできます。ユーザーのパスワードはパスワードのハッシュを使用して Azure と同期され、クラウドで認証が行われます。詳細については、「[パスワードの同期](active-directory-aadconnectsync-implement-password-synchronization.md)」を参照してください。
AD FS とのフェデレーション|ユーザーは、オンプレミスのネットワークへのログインに使用しているものと同じパスワードを使用して、Microsoft クラウド サービス (Office 365、Dynamics CRM、Windows InTune など) にサインインできます。ユーザーはサインインのためにオンプレミス AD FS インスタンスにリダイレクトされ、認証はオンプレミスで行われます。
構成しない| 機能はインストールも構成もされません。サード パーティのフェデレーション サーバーまたは別の既存のソリューションが既に設置されている場合は、このオプションを選択します。

## Azure AD に接続
[Azure AD に接続] 画面で、グローバル管理者のアカウントとパスワードを入力します。前のページで [**AD FS とのフェデレーション**] を選択した場合、フェデレーション用に有効にする予定があるドメイン内のアカウントでサインインしないでください。Azure AD ディレクトリに付属する既定の **onmicrosoft.com** ドメイン内のアカウントを使用することをお勧めします。

このアカウントは、Azure AD のサービス アカウントを作成するためにのみ使用され、ウィザードが完了した後は使用されません。![ユーザーのサインイン](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

グローバル管理者アカウントで MFA が有効にされている場合は、サインイン ポップアップに再度パスワードを入力し、検証コードの入力など、MFA チャレンジを完了する必要があります。 ![ユーザーのサインイン MFA](./media/active-directory-aadconnect-get-started-custom/connectaadmfa.png)

グローバル管理者アカウントは [Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) も有効にされている可能性があります。

接続の問題によってエラーが発生する場合は、「[Azure AD Connect での接続に関する問題のトラブルシューティング](active-directory-aadconnect-troubleshoot-connectivity.md)」を参照してください。

## [同期] セクションのページ

### ディレクトリの接続
Azure AD Connect では、Active Directory ドメイン サービスに接続するには、十分なアクセス許可を持つアカウントの資格情報が必要です。このアカウントには既定の読み取りアクセス許可が必要なだけなので、通常のユーザー アカウントを指定できます。ただし、シナリオによっては、追加のアクセス許可が必要なことがあります。詳細については [Azure AD Connect アカウントとアクセス許可](active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account)に関するページを参照してください。

![ディレクトリの接続](./media/active-directory-aadconnect-get-started-custom/connectdir.png)

### ドメインと OU のフィルター処理
既定で、すべてのドメインと OU が同期されます。Azure AD に同期させたくないドメインまたは OU がある場合は、これらのドメインと OU を選択解除できます。![DomainOU フィルター処理](./media/active-directory-aadconnect-get-started-custom/domainoufiltering.png) ウィザードのこのページは、「[ドメイン ベースのフィルター処理](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering)」でも説明されている、ドメイン ベースのフィルター処理を構成します。

さらに、一部のドメインは、ファイアウォールの制限のために到達できないこともあります。これらのドメインは、既定で選択が解除され、警告が表示されます。![到達不能ドメイン](./media/active-directory-aadconnect-get-started-custom/unreachable.png) これが表示された場合、これらのドメインが実際に到達できず、これが予想されることであるか確認します。

### ユーザーを一意に識別
[フォレスト全体で一致] 機能を使用すると、AD DS フォレストのユーザーを Azure AD でどう表すかを定義することができます。ユーザーは、すべてのフォレストで 1 回だけ表すか、有効アカウントと無効アカウントを組み合わせることができます。

![一意](./media/active-directory-aadconnect-get-started-custom/unique.png)

設定 | 説明
------------- | ------------- |
[ユーザーはフォレスト全体で 1 回だけ表されます](active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) | すべてのユーザーは、Azure AD で個別のオブジェクトとして作成されます。<br> オブジェクトは、メタバースに結合されません。
[メール属性](active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) | このオプションは、異なるフォレスト間でメール属性が同じ値である場合に、ユーザーと連絡先を結合します。連絡先が GALSync を使用して作成されているときに、このオプションを使用することをお勧めします。
[ObjectSID および msExchangeMasterAccountSID/msRTCSIP-OriginatorSid](active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest)|このオプションは、アカウント フォレストで有効なユーザーと、Exchange リソース フォレストで無効なユーザーを結合します。これは、Exchange のリンクされたメールボックスとして知られています。このオプションは、Lync のみを使用し、Exchange がリソース フォレスト内にない場合にも使用できます。
sAMAccountName および MailNickName|このオプションは、ユーザーのサインイン ID を見つけるために必要な属性同士を結合します。
特有の属性|このオプションでは、独自の属性を選択することができます。**制限:** かならず、メタバース内に既に存在する属性を選択してください。カスタム属性 (メタバースにない) を選択すると、ウィザードは完了できません。

- **ソース アンカー** -sourceAnchor 属性はユーザー オブジェクトの有効期間中に変更できない属性です。オンプレミスのユーザーと Azure AD のユーザーをリンクするプライマリ キーです。この属性は変更できないため、適切な属性を使用するように計画する必要があります。適切な属性として考えられるのは objectGUID です。この属性は、ユーザー アカウントをフォレスト/ドメイン間で移動しなければ、変更されません。フォレスト間でアカウントを移動するマルチ フォレスト環境では、employeeID を持つ属性など、別の属性を使用する必要があります。ユーザーが結婚したり割り当てを変更したりした場合に変更される可能性のある属性は、使用すべきではありません。@ 記号が含まれる属性は使用できないので、電子メールや userPrincipalName は使用できません。この属性では大文字小文字も区別されるため、フォレスト間でオブジェクトを移動する場合は、大文字と小文字を維持してください。バイナリ属性の値は base64 でエンコードされますが、他のタイプの属性はエンコードされていない状態が保たれます。フェデレーション シナリオと一部の Azure AD インターフェイスでは、この属性は immutableID とも呼ばれます。ソース アンカーの詳細については、「[設計概念](active-directory-aadconnect-design-concepts.md#sourceAnchor)」を参照してください。

- **UserPrincipalName** - userPrincipalName 属性は、ユーザーが Azure AD と Office 365 にサインインするときに使用する属性です。使用するドメイン (UPN サフィックス) は、ユーザーを同期する前に、Azure AD で検証する必要があります。既定の userPrincipalName 属性のままにしておくことを強くお勧めします。この属性がルーティング不可能で検証できない場合は、サインイン ID を保持する属性として、電子メールなどの別の属性を選択することができます。これを**代替 ID** といいます。代替 ID の属性値は、RFC822 標準に従う必要があります。代替 ID は、サインイン ソリューションとして、パスワード シングル サインオン (SSO) とフェデレーション SSO の両方で使用できます。

>[AZURE.WARNING] 代替 ID の使用は、すべての Office 365 ワークロードと互換性があるわけではありません。詳細については、[代替ログイン ID の構成](https://technet.microsoft.com/library/dn659436.aspx)に関するページを参照してください。

### グループに基づく同期フィルタリング
グループのフィルタリングでは、Azure AD と Office 365 でオブジェクトの小さなサブセットのみを作成する小規模なパイロットを実行することができます。この機能を使用するには、オンプレミスの Active Directory でグループを作成して、直接メンバーとして Azure AD と同期させるユーザーやグループを追加します。このグループには後でユーザーを追加したりユーザーを削除したりして、Azure AD に表示するオブジェクトの一覧を管理することができます。同期するオブジェクトはすべて、グループの直接のメンバーである必要があります。これには、ユーザー、グループ、連絡先、およびコンピューター/デバイスが含まれます。入れ子になったグループのメンバーシップは解決されません。グループ メンバーにはグループ自体は含まれますが、そのメンバーは含まれません。

この機能を使用する場合、カスタム インストールの途中で次のようなページが表示されます。![フィルターの同期](./media/active-directory-aadconnect-get-started-custom/filter2.png)

>[AZURE.WARNING] この機能は、パイロット デプロイメントのサポートのみを目的としているので、本格的な運用環境のデプロイメントでは使用しないでください。

本格的な運用環境では、単一のグループを維持してすべてのオブジェクトを同期するのは困難です。代わりに、「[フィルター処理の構成](active-directory-aadconnectsync-configure-filtering.md)」で説明されている手法のいずれかを使用する必要があります。

### オプション機能
この画面では、特定のシナリオのためにオプション機能を選択することができます。次に、個別の機能のそれぞれについて簡単な説明を示します。

![オプション機能](./media/active-directory-aadconnect-get-started-custom/optional.png)

> [AZURE.WARNING] 現時点で DirSync または Azure AD Sync がアクティブになっている場合は、Azure AD Connect の書き戻し機能をアクティブにしないでください。

オプション機能 | 説明
-------------------    | ------------- |
Exchange ハイブリッドのデプロイメント |Exchange ハイブリッドのデプロイメント機能によって、オンプレミスと Office 365 の両方で、Exchange メールボックスの共存が可能となります。そのためには、Azure AD から特定の[属性](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback)のセットを、オンプレミスのディレクトリと同期します。
Azure AD アプリと属性フィルター|Azure AD アプリと属性フィルターを有効にして、ウィザードの後続のページで、同期する属性のセットを特定のセットに合わせることができます。このことを行うと、ウィザードで 2 つの追加の構成ページが開きます。詳細については、「[Azure AD アプリと属性フィルター](#azure-ad-app-and-attribute-filtering)」を参照してください。
パスワードの同期 | サインイン リューションとしてフェデレーションを選択した場合は、このオプションを有効にすることができます。バックアップ オプションとして、パスワード同期を使用できます。詳細については、「[パスワードの同期](active-directory-aadconnectsync-implement-password-synchronization.md)」を参照してください。
パスワード ライトバック|パスワード ライトバックを有効にすると、Azure AD で発信されるパスワードの変更が、オンプレミスのディレクトリに書き戻されます。詳細については、「[パスワード管理の概要](active-directory-passwords-getting-started.md)」を参照してください。
グループの書き戻し |**Office 365 グループ**機能を使用する場合は、これらのグループを配布グループとしてオンプレミス Active Directory に置くことができます。このオプションが使用できるのは、オンプレミスの Active Directory 内に Exchange が置かれている場合に限られます。詳細については、「[グループの書き戻し](active-directory-aadconnect-feature-preview.md#group-writeback)」を参照してください。
デバイスの書き戻し | 条件付きアクセスのシナリオの場合は、Azure AD 内のデバイス オブジェクトをオンプレミスの Active Directory に書き戻すことができます。詳細については、「[Azure AD Connect でのデバイスの書き戻しの有効化](active-directory-aadconnect-feature-device-writeback.md)」を参照してください。
ディレクトリ拡張属性の同期|ディレクトリ拡張属性の同期を有効にすると、指定した追加の属性が Azure AD に同期されます。詳細については、「[ディレクトリ拡張機能](active-directory-aadconnectsync-feature-directory-extensions.md)」を参照してください。

### Azure AD アプリと属性フィルター
Azure AD に同期する属性を限定する場合は、まず、使用するサービスを選択します。このページを構成する場合は、インストール ウィザードを再実行することによって新しいサービスを明示的に選択する必要があります。

![オプション機能](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

前の手順で選択したサービスに基づいて、次のページに、同期対象となるすべての属性が表示されます。この一覧は、同期されるすべてのオブジェクトの種類の組み合わせです。特定の属性について同期の必要がない場合は、該当する属性の選択を解除します。

![オプション機能](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

### ディレクトリ拡張機能の属性の同期
ディレクトリ拡張機能では、Active Directory で組織やその他の属性によって追加されたカスタム属性を使用して Azure AD のスキーマを拡張することができます。この機能を使用するには、[**オプション機能**] ページの [**ディレクトリ拡張機能の属性の同期**] を選択します。選択すると、追加の属性を選択できる次のページが表示されます。

![フィルターの同期](./media/active-directory-aadconnect-get-started-custom/extension2.png)

詳細については、「[ディレクトリ拡張機能](active-directory-aadconnectsync-feature-directory-extensions.md)」を参照してください。

## AD FS とのフェデレーションの構成
Azure AD Connect との AD FS の構成は、わずか数クリックで簡単です。セットアップの前に次が必要です。

- フェデレーション サーバー用の Windows Server 2012 R2 サーバー (リモート管理を有効に設定)
- Web アプリケーション プロキシ サーバー用の Windows Server 2012 R2 サーバー (リモート管理を有効に設定)
- 使用する予定のフェデレーション サービス名の SSL 証明書 (sts.contoso.com など)

### 新しい AD FS ファームの作成または既存の AD FS ファームの使用
既存の AD FS ファームを使用することも、新しい AD FS ファームを作成することもできます。新しく作成する場合は、SSL 証明書を提供する必要があります。SSL 証明書がパスワードで保護されている場合は、パスワードを入力するように求められます。

![AD FS ファーム](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

**注:** 既存の AD FS ファームを使用する場合は、いくつかのページをスキップし、AD FS と Azure AD の間の信頼関係を構成する画面に直接移動されます。

### AD FS サーバーの指定
ここでは、AD FS をインストールする特定のサーバーを入力します。容量計画のニーズに基づいて 1 つまたは複数のサーバーを追加することができます。追加するすべてのサーバーは、この構成を実行する前に、Active Directory ドメインに参加させる必要があります。テストおよびパイロット デプロイ用に単一の AD FS サーバーをインストールし、スケーリングのニーズに合わせて追加のサーバーをデプロイすることをお勧めします。追加のサーバーをデプロイするには、初期インストール後に、再度 Azure AD Connect を開き、AD FS を追加のサーバーにデプロイします。

> [AZURE.NOTE] この構成を実行する前に、すべてのサーバーを 1 つの AD ドメインに参加させてください。　

![AD FS サーバー](./media/active-directory-aadconnect-get-started-custom/adfs2.png)

### Web アプリケーション プロキシ サーバーの指定
ここでは、Web アプリケーション プロキシ サーバーとする、特定のサーバーを入力します。Web アプリケーション プロキシ サーバーは DMZ (エクストラネット接続) にデプロイされ、エクストラネットからの認証要求をサポートします。容量計画のニーズに基づいて 1 つまたは複数のサーバーを追加することができます。テストおよびパイロット デプロイ用に単一の Web アプリケーション プロキシ サーバーをインストールし、追加のサーバーをデプロイすることをお勧めします。追加のサーバーをデプロイするには、初期インストール後に、再度 Azure AD Connect を開き、Web アプリケーション プロキシを追加のサーバーにデプロイします。通常は、イントラネットからの認証を処理するために同数のプロキシ サーバーを設定することをお勧めします。

> [AZURE.NOTE]
<li> Azure AD Connect のインストールに使用しているアカウントが AD FS サーバーのローカル管理者ではない場合、十分なアクセス許可を持つアカウントの資格情報を入力するように求められます。</li>
<li> この手順を構成する前に、Azure AD Connect サーバーと Web アプリケーション プロキシ サーバーとの間に HTTP/HTTPS 接続があることを確認してください。</li>
<li> また、認証要求の通過を許可するために、Web アプリケーション サーバーと AD FS サーバーとの間に HTTP/HTTPS 接続が設定されていることを確認してください。</li>

![Web アプリ](./media/active-directory-aadconnect-get-started-custom/adfs3.png)

Web アプリケーション サーバーが AD FS サーバーとセキュリティで保護された接続を確立できるように、資格情報を入力するよう求められます。これらの資格情報は、AD FS サーバーのローカル管理者である必要があります。

![プロキシ](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

### AD FS サービスのサービス アカウントを指定します。
AD FS サービスには、ユーザーを認証し Active Directory のユーザー情報を参照するドメイン サービス アカウントが必要です。次の 2 種類のサービス アカウントがサポートされます。

- **グループ管理サービス アカウント** - Windows Server 2012 の Active Directory ドメイン サービスで導入されたサービス アカウントの種類です。この種類のアカウントは、AD FS のようなサービスを提供します。1 つのアカウントを使用し、アカウントのパスワードを定期的に更新する必要はありません。AD FS サーバーが所属するドメインに Windows Server 2012 ドメイン コント ローラーが既にある場合は、このオプションを使用します。
- **ドメイン ユーザー アカウント** -この種類のアカウントはパスワードの入力を求め、パスワードを変更するときに、パスワードを定期的に更新する必要があります。AD FS サーバーが所属するドメインに Windows Server 2012 ドメイン コント ローラーがない場合にのみ、このオプションを使用します。

グループ管理サービス アカウントを選択しており、Active Directory でこの機能を使用したことがない場合、エンタープライズ管理者の資格情報の入力も求められます。これらは、キー ストアを開始し、Active Directory で機能を有効にするために使用されます。

ドメイン管理者としてログインしている場合は、Azure AD Connect によってグループ管理サービス アカウントが自動作成されます。

![AD FS サービス アカウント](./media/active-directory-aadconnect-get-started-custom/adfs5.png)


### フェデレーションする Azure AD ドメインの選択
この構成を使用して、AD FS と Azure AD のフェデレーション関係をセットアップします。Azure AD にセキュリティ トークンを発行するように AD FS を構成し、この特定の AD FS インスタンスからのトークンを信頼するように Azure AD を構成します。このページでは、初期インストールで 1 つのドメインしか構成できません。追加のドメインは、Azure AD Connect を再度開きこのタスクを実行することで、いつでも構成できます。


![Azure AD ドメイン](./media/active-directory-aadconnect-get-started-custom/adfs6.png)


### フェデレーション用に選択された Azure AD ドメインの検証

オンプレミスのディレクトリとフェデレーションするドメインを選択すると、まだ検証されていない場合はドメインを検証するために必要な情報が Azure AD Connect から提供されます。ドメイン名の検証を完了するためにドメイン名レジストラーや DNS がホストされている場所で作成する必要のある DNS レコードが、下記のページに表示されます。</br>

![Azure AD ドメイン](./media/active-directory-aadconnect-get-started-custom/verifyfeddomain.png)

> [AZURE.NOTE] AD Connect では、構成段階でドメインの検証を試みます。ドメイン DNS がホストされている場所で必要な DNS レコードを追加せずに構成を続行すると、ウィザードでは構成を完了することができません。</br>

## ページの構成および確認
実際の構成はこのページで行われます。

> [AZURE.NOTE]
インストールを続行する前に、フェデレーションを構成している場合は、[フェデレーション サーバーの名前解決](active-directory-aadconnect-prerequisites.md#name-resolution-for-federation-servers)を構成していることを確認してください。

![フィルターの同期](./media/active-directory-aadconnect-get-started-custom/readytoconfigure2.png)

### ステージング モード
ステージング モードでは、新しい同期サーバーをセットアップする処理を既存のサーバーと並行して行うことができます。クラウド内で 1 つのディレクトリにエクスポートする 1 台の同期サーバーの設置のみがサポートされます。ただし、別のサーバー (DirSync を実行するサーバーなど) から移動する場合は、ステージング モードで Azure AD Connect を有効にすることができます。有効にすると、同期エンジンは通常の方法でデータをインポートして同期しますが、Azure AD には何もエクスポートしません。また、パスワードの同期とパスワード ライトバックはオフになります。

![フィルターの同期](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)

ステージング モード中は、同期エンジンに必要な変更を加え、エクスポートされる内容を確認することができます。構成が適切な状態になったら、インストール ウィザードをもう一度実行し、ステージング モードを無効にします。ステージング モードを無効にすると、データを Azure AD にエクスポートできるようになります。1 つのサーバーのみをアクティブにしてエクスポートするために、このとき他のサーバーは無効にしてください。

詳細については、「[ステージング モード](active-directory-aadconnectsync-operations.md#staging-mode)」を参照してください。

### フェデレーション構成の確認
[検証] ボタンをクリックすると、Azure AD Connect で DNS 設定が確認されます。

![完了](./media/active-directory-aadconnect-get-started-custom/completed.png)

![確認](./media/active-directory-aadconnect-get-started-custom/adfs7.png)

さらに、次の検証手順を実行します。

- イントラネットのドメイン参加しているマシンの Internet Explorer からブラウザー サインインを検証する: https://myapps.microsoft.com に接続し、ログインしたアカウントでサインインを検証します。**注:** 組み込みの AD DS 管理者アカウントは同期されないため、検証には使用できません。
- エクストラネットの任意のデバイスからブラウザー サインインを検証する: 自宅のマシンまたはモバイル デバイスで https://myapps.microsoft.com に接続し、サインイン ID とパスワード資格情報を入力します。
- リッチ クライアントのサインインを検証する: https://testconnectivity.microsoft.com に接続し、**[Office 365]** タブ、**[Office 365 シングル サインオン テスト]** の順に選択します。

## 次のステップ
インストールが完了した後、 Synchronization Service Manager または同期規則エディターを使用する前に、サインアウトし、もう一度 Windows にサインインします。

Azure AD Connect がインストールされたので、[インストールを確認し、ライセンスを割り当てる](active-directory-aadconnect-whats-next.md)ことができます。

「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0302_2016-->