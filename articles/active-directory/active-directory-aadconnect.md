<properties
	pageTitle="Azure AD Connect: オンプレミス ID と Azure Active Directory の統合 | Microsoft Azure"
	description="Azure AD Connect は、Windows Server Active Directory などのオンプレミス ID システムを Azure Active Directory と統合し、ユーザーを Office 365、Azure、数千の SaaS アプリケーションに接続するツールです。このトピックでは、Azure AD Connect の機能について説明し、Azure AD Connect の動作、インストール方法、構成、カスタマイズに関する情報を提供します。"
    keywords="Azure AD Connect の紹介, Azure AD Connect の概要, Azure AD Connect とは, Active Directory のインストール"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/25/2016"
	ms.author="andkjell;billmath"/>

# オンプレミス ID と Azure Active Directory の統合
Azure AD Connect は、Windows Server Active Directory などのオンプレミス ID システムを Azure Active Directory と統合し、ユーザーを Office 365、Azure、数千の Saas アプリケーションに接続するツールです。このトピックでは、エンド ユーザーが既存の会社アプリへのアクセスに使っているのと同じ ID でクラウド サービスにアクセスできるようにするうえで必要なコンポーネントを準備およびデプロイする方法を包括的に示します。

![Azure AD Connect とは](./media/active-directory-aadconnect/arch.png)

## Azure AD Connect を使用する理由
オンプレミスのディレクトリと Azure AD を統合すると、クラウドとオンプレミス両方のリソースにアクセスするための共通の ID が提供されるため、ユーザーの生産性が向上します。この統合によって、ユーザーや組織には次の利点があります。

- ユーザーは、単一の ID を使ってオンプレミスのアプリケーションとクラウド サービス (Office 365 など) にアクセスできます。

- 単一のツールにより、同期とサインインのための容易なデプロイメントを実現できます。

- それぞれのシナリオに適した最新の機能が手に入ります。Azure AD Connect は、DirSync や Azure AD Sync など、旧バージョンの ID 統合ツールの後継ツールです。詳細については、「[ディレクトリ統合ツールの比較](active-directory-aadconnect-get-started-tools-comparison.md)」を参照してください。


### Azure AD Connect の動作

Azure Active Directory Connect は 3 つの主要な部分で構成されます。この 3 つの部分とは、同期サービス、オプションの Active Directory フェデレーション サービス、および [Azure AD Connect Health](active-directory-aadconnect-health.md) を使用して行う監視です。

<center>![Azure AD Connect スタック](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- 同期 - この部分は、以前 [Dirsync および Azure AD Sync](active-directory-aadconnect-get-started-tools-comparison.md) としてリリースされたコンポーネントと機能で構成されます。これは、ユーザーとグループの作成を担当している部分です。オンプレミス環境のユーザーとグループに関する情報とクラウド内の情報が一致することも保証します。
- AD FS - これは Azure AD Connect のオプションの部分であり、オンプレミスの AD FS インフラストラクチャを使用して、ハイブリッド環境をセットアップするために使用できます。この部分は、ドメイン参加 SSO、AD のログインのポリシーの適用、スマート カードやサード パーティの MFA などを含む複雑なデプロイメントに対応するために、組織で使用できます。
- 正常性の監視 - Azure AD Connect Health により、AD FS サーバーを強力に監視し、このアクティビティを Azure ポータルで一元的に表示できます。詳細については、「[Azure Active Directory Connect Health](active-directory-aadconnect-health.md)」を参照してください。

## Azure AD Connect のインストール

Azure AD Connect は [Microsoft ダウンロード センター](http://go.microsoft.com/fwlink/?LinkId=615771)からダウンロードできます。


| 解決策 | シナリオ |
| ----- | ----- |
| 開始する前に | <li>[Azure AD Connect: ハードウェアと前提条件](active-directory-aadconnect-prerequisites.md)</li> |
| [簡単設定](active-directory-aadconnect-get-started-express.md) | <li>単一のフォレスト AD がある場合に推奨される既定のオプションです。</li> <li>ユーザーは、パスワード同期により、同じパスワードを使ってサインインします。</li>
| [カスタマイズした設定](active-directory-aadconnect-get-started-custom.md) | <li>複数のフォレストがある場合に使用されます。多くのオンプレミス [トポロジ](active-directory-aadconnect-topologies.md)がサポートされます。</li> <li>フェデレーション用の ADFS などのサインイン オプションをカスタマイズするか、サード パーティの ID プロバイダーを使います。</li> <li>フィルター処理やライトバックなどの同期機能をカスタマイズします。</li>
| [DirSync からのアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>既存の DirSync サーバーが既に実行されている場合に使用します。</li>
| Azure AD Sync からのアップグレード | <li>これは、シームレスなインプレース アップグレードです。</li>


[インストール後に](active-directory-aadconnect-whats-next.md)想定どおりに動作していることをことを確認し、ユーザーにライセンスを割り当てる必要があります。

### Azure AD Connect のインストールの次のステップ

| トピック | |
| --------- | --------- |
| Azure AD Connect のダウンロード | [Azure AD Connect のダウンロード](http://go.microsoft.com/fwlink/?LinkId=615771) |
| Express 設定を使用したインストール | [Azure AD Connect の高速インストール](active-directory-aadconnect-get-started-express.md) |
| カスタマイズした設定を使用したインストール | [Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md) |
| DirSync からのアップグレード | [Azure AD 同期ツール (DirSync) からのアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| インストール後 | [インストールの確認とライセンスの割り当て](active-directory-aadconnect-whats-next.md) |

### Azure AD Connect のインストールの詳細

[運用](active-directory-aadconnectsync-operations.md)上の問題への備えも必要になることがあります。[障害](active-directory-aadconnectsync-operations.md#disaster-recovery)が発生したときに切り替えられるように、スタンバイ サーバーを用意するという方法もあります。頻繁に構成を変更する予定がある場合は、[ステージング モード](active-directory-aadconnectsync-operations.md#staging-mode) サーバーについて計画してください。

| トピック | |
| --------- | --------- |
| サポートされているトポロジ | [Azure AD Connect のトポロジ](active-directory-aadconnect-topologies.md) |
| 設計概念 | [Azure AD Connect の設計概念](active-directory-aadconnect-design-concepts.md) |
| インストールで使用するアカウント | [Azure AD Connect の資格情報とアクセス許可の詳細](active-directory-aadconnect-accounts-permissions.md) |
| 運用計画 | [Azure AD Connect Sync: 操作タスクおよび考慮事項](active-directory-aadconnectsync-operations.md) |
| ユーザーのサインイン オプション | [Azure AD Connect ユーザーのサインイン オプション](active-directory-aadconnect-user-signin.md) |

## 機能の構成
Azure AD Connect には、必要に応じて有効にすることができる機能や、既定で有効になっている機能があります。一部の機能については、シナリオやトポロジに応じて追加の構成が必要になることがあります。

[フィルター処理](active-directory-aadconnectsync-configure-filtering.md)は、Azure AD に同期するオブジェクトを制限する場合に使用します。既定では、すべてのユーザー、連絡先、グループ、Windows 10 コンピューターが同期されますが、ドメイン、OU、または属性に基づいて制限することができます。

[パスワード同期](active-directory-aadconnectsync-implement-password-synchronization.md)では、Active Directory のパスワード ハッシュを Azure AD に同期します。これにより、ユーザーは、同じパスワードをオンプレミスとクラウドで使用できるようになります。しかも、それを 1 か所で管理できます。オンプレミスの Active Directory が使用されるため、独自のパスワード ポリシーを使用することができます。

[パスワード ライトバック](active-directory-passwords-getting-started.md)により、ユーザーはクラウドでパスワードを変更およびリセットし、オンプレミスのパスワード ポリシーを適用できます。

[デバイスの書き戻し](active-directory-aadconnect-get-started-custom-device-writeback.md)により、Azure AD に登録されているデバイスをオンプレミスの Active Directory にライトバックできます。これにより、そのデバイスを条件付きアクセスに使用できるようになります。

[誤って削除されないように保護する](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)機能は既定で有効になっており、多数のクラウド ディレクトリが同時に削除されるのを防ぐことができます。既定では実行ごとに 500 件の削除が許可されていますが、組織のサイズに応じて変更できます。

### 機能を構成する次のステップ

| トピック | |
| --------- | --------- |
| フィルター処理の構成 | [Azure AD Connect Sync: フィルター処理の構成](active-directory-aadconnectsync-configure-filtering.md) |
| パスワードの同期 | [Azure AD Connect Sync: パスワード同期の実装](active-directory-aadconnectsync-implement-password-synchronization.md) |
| パスワード ライトバック | [パスワード管理の概要](active-directory-passwords-getting-started.md) |
| デバイスの書き戻し | [Azure AD Connect でのデバイスの書き戻しの有効化](active-directory-aadconnect-get-started-custom-device-writeback.md) |
| 誤って削除されないように保護する | [Azure AD Connect Sync: 誤って削除されないように保護する](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |

## Azure AD Connect Sync のカスタマイズ
Azure AD Connect Sync には、ほとんどのお客様とトポロジに対応した既定の構成が設定されています。とはいえ、既定の構成ではうまくいかず、調整が必要な場面も必ず存在します。このセクションとリンク先のトピックにまとめられているように、構成は変更できます。

これまでに同期トポロジを扱った経験がない場合は、[技術的概念](active-directory-aadconnectsync-technical-concepts.md)で説明されている基本情報と用語を確認してください。Azure AD Connect は、MIIS2003、ILM2007、FIM2010 が進化したものです。同じ要素もあるものの、多数の変更が加えられています。

この[既定の構成](active-directory-aadconnectsync-understanding-default-configuration.md)は、複数のフォレストが存在する可能性があることを前提としています。これらのトポロジでは、ユーザー オブジェクトが別のフォレスト内の連絡先として表されることがあります。ユーザーは、別のリソース フォレストにリンクされたメールボックスを持っている場合もあります。既定の構成の動作については、[ユーザーと連絡先](active-directory-aadconnectsync-understanding-users-and-contacts.md)に関するページを参照してください。

同期の構成モデルは、[宣言型のプロビジョニング](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)と呼ばれています。高度な属性のフローでは、[関数](active-directory-aadconnectsync-functions-reference.md)を使って属性の変換を表現します。Azure AD Connect に付属するツールを使って、構成全体を確認、検証できます。構成を変更する必要がある場合は、新しいリリースを採用しやすいように、[ベスト プラクティス](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)に従ってください。

### Azure AD Connect Sync のカスタマイズの次のステップ

| トピック | |
| --------- | --------- |
| 技術的概念 | [Azure AD Connect Sync: 技術的概念](active-directory-aadconnectsync-technical-concepts.md) |
| 既定の構成について | [Azure AD Connect Sync: 既定の構成について](active-directory-aadconnectsync-understanding-default-configuration.md) |
| ユーザーと連絡先について | [Azure AD Connect Sync: ユーザーと連絡先について](active-directory-aadconnectsync-understanding-users-and-contacts.md) |
| 宣言型のプロビジョニング | [Azure AD Connect Sync: 宣言型のプロビジョニングの式について](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| 宣言型のプロビジョニングの関数リファレンス | [Azure AD Connect Sync: 関数リファレンス](active-directory-aadconnectsync-functions-reference.md) |
| 既定の構成の変更 | [既定の構成の変更するためのベスト プラクティス](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |

## 詳細情報とリファレンス

| トピック | |
| --------- | --------- |
| バージョン履歴 | [バージョン履歴](active-directory-aadconnect-version-history.md) |
| DirSync、Azure ADSync、Azure AD Connect の比較 | [ディレクトリ統合ツールの比較](active-directory-aadconnect-get-started-tools-comparison.md) |
| 同期される属性 | [同期される属性](active-directory-aadconnectsync-attributes-synchronized.md) |
| Azure AD Connect Health を使用した監視 | [Azure AD Connect Health](active-directory-aadconnect-health.md) |
| よく寄せられる質問 | [Azure AD Connect の FAQ](active-directory-aadconnect-faq.md) |


**その他のリソース**


オンプレミスのディレクトリのクラウドへの拡張に関する 2015 年のプレゼンテーションをご覧ください。

[AZURE.VIDEO microsoft-ignite-2015-extending-on-premises-directories-to-the-cloud-made-easy-with-azure-active-directory-connect]

<!---HONumber=AcomDC_0128_2016-->