<properties
	pageTitle="Azure AD Domain Services: Azure AD Domain Services と自作のドメイン コント ローラーの比較 | Microsoft Azure"
	description="Azure Active Directory Domain Services と自作のドメイン コント ローラーの比較"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="maheshu"/>

# Azure AD Domain Services がユースケースに適しているかを判断する方法
Azure AD Domain Services を利用すると、ID インフラストラクチャのメンテナンスについて心配することなく、Azure インフラストラクチャ サービスでワークロードをデプロイできます。管理されたこのサービスは、自分でデプロイおよび管理する、典型的な Windows Server Active Directory デプロイとは異なります。このサービスは、デプロイの簡略化、正常性監視と修正の自動化、およびクラウドの ID インフラストラクチャの簡素化を目的としています。このサービスには定期的に、一般的なデプロイのシナリオのサポートが追加されます。

Azure AD Domain Services を使用するか、Azure で独自の AD インフラストラクチャを作成して管理する (自作) かを決定するには、次の手順に従ってください。

- [Azure AD Domain Services が提供する機能](active-directory-ds-features.md)のリストを参照します。

- [Azure AD Domain Services の一般的なデプロイ シナリオ](active-directory-ds-scenarios.md)を確認します。

- 最後に、[Azure AD Domain Services と、自作の AD のオプションとを比較](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure)します。


## Azure AD Domain Services と Azure での自作の AD ドメインの比較
次の表は、Azure AD Domain Services を使用するか、Azure で独自の AD インフラストラクチャを管理するかを決定する助けになります。

|機能|Azure AD Domain Services|Azure VM での AD の '自作'|
|---|:---:|:---:|
|[**管理されたサービス**](active-directory-ds-comparison.md#managed-service)|はい|なし|
|[**安全なデプロイ**](active-directory-ds-comparison.md#secure-deployments)|はい|管理者がデプロイを保護する必要があります。|
|[**DNS サーバー**](active-directory-ds-comparison.md#dns-server)|はい (管理されたサービス)|はい|
|[**ドメインまたはエンタープライズ管理者の特権**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|なし|はい|
|[**ドメイン参加**](active-directory-ds-comparison.md#domain-join)|はい|はい|
|[**NTLM と Kerberos を使用するドメインの認証**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|はい|はい|
|[**カスタムの OU 構造**](active-directory-ds-comparison.md#custom-ou-structure)|はい|はい|
|[**スキーマの拡張機能**](active-directory-ds-comparison.md#schema-extensions)|なし|はい|
|[**AD ドメイン/フォレストの信頼**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|なし|はい|
|[**LDAP の読み取り**](active-directory-ds-comparison.md#ldap-read)|はい|はい|
|[**セキュリティで保護された LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|はい|はい|
|[**LDAP 書き込み**](active-directory-ds-comparison.md#ldap-write)|なし|はい|
|[**グループ ポリシー**](active-directory-ds-comparison.md#group-policy)|シンプル|完全|
|[**地理的に分散したデプロイ**](active-directory-ds-comparison.md#geo-dispersed-deployments)|なし|はい|

#### 管理されたサービス
Azure AD Domain Services のドメインは、Microsoft が管理します。修正プログラムの適用、更新、監視、バックアップ、およびドメインの可用性の確保について心配する必要はありません。これらの管理タスクは、お客様の管理対象ドメインへのサービスとして、Microsoft Azure によって提供されます。

#### 安全なデプロイ
管理対象のドメインは、AD デプロイ向けの Microsoft のセキュリティのベスト プラクティスに従って、安全にロックダウンされています。これらのベスト プラクティスは、AD 製品チームが持つ、長年の AD デプロイのエンジニアリングとサポートの経験から発生しています。自作のデプロイの場合は、デプロイのロックダウンやセキュリティ確保のために、特定のデプロイ手順を実行する必要があります。

#### DNS サーバー
Azure AD Domain Services で管理されているドメインには、管理されている DNS サービスが含まれます。"AAD DC Administrators" グループのメンバーが、管理対象ドメインの DNS を管理できます。このグループのメンバーには、管理対象ドメインの完全な DNS 管理特権が与えられます。DNS の管理は、リモート サーバー管理ツール (RSAT) のパッケージに含まれる 'DNS 管理コンソール' を使用して実行できます。

#### ドメインまたはエンタープライズ管理者の特権
これらの管理者特権は、AAD DS 管理対象ドメインでは提供されません。インストールや実行にこれらの管理者特権を必要とするアプリケーションは、管理対象ドメインで実行できません。管理者特権の小さなサブセットは、‘AAD DC Administrators’ という委任された管理者グループのメンバーが使用できます。これらの権限には、DNS の構成、グループ ポリシーの構成、ドメインに参加しているマシンの管理者特権の取得などの権限が含まれます。

#### Domain Join
コンピューターを AD ドメインに参加させる方法と同じように、仮想マシンを管理対象ドメインに参加させられます。

#### NTLM と Kerberos を使用するドメインの認証
Azure AD Domain Services では、管理対象ドメインの認証に、自分の会社の資格情報を使用できます。資格情報は、Azure AD テナントと同期されて保持されます。同期されたテナントでは、Azure AD Connect によって、オンプレミスで加えられた資格情報への変更が Azure AD に同期されます。自作のドメインをセットアップした場合は、オンプレミスのアカウント フォレストとのドメインの信頼関係を設定して、ユーザーが自分の会社の資格情報で認証できるようにする必要があるかもしれません。または、AD レプリケーションを設定して、ユーザー パスワードが Azure ドメイン コントローラーの仮想マシンに同期させるようにする必要がある場合があります。

#### カスタムの OU 構造
'AAD DC Administrators' グループのメンバーが、管理対象ドメイン内でカスタム OU を作成できます。

#### スキーマの拡張機能
Azure AD Domain Services で管理されているドメインのベースのスキーマを拡張することはできません。したがって、AD スキーマへの拡張に依存するアプリケーションを AAD-DS ドメインに引き上げたり、移行したりすることはできません。

#### AD ドメインまたはフォレストの信頼
他のドメインとの信頼関係 (受信/送信) を設定する管理対象ドメインを構成することはできません。したがって、リソース フォレストのデプロイなどのシナリオ、またはAzure AD とパスワードを同期させない場合には、Azure AD Domain Services を使用できません。

#### LDAP の読み取り
管理対象ドメインは、LDAP 読み取りワークロードをサポートします。そのため、管理対象ドメインに対する LDAP 読み取り操作を実行するアプリケーションをデプロイできます。

#### セキュリティで保護された LDAP
インターネット経由を含む、管理対象ドメインへのセキュリティで保護された LDAP アクセスを提供する Azure AD Domain Services を構成できます。

#### LDAP 書き込み
管理対象ドメインでは、ユーザー オブジェクトの読み取りのみ可能です。そのため、ユーザー オブジェクトの属性に LDAP 書き込み操作を実行するアプリケーションは、管理対象ドメインでは動作しません。さらに、ユーザーのパスワードを管理対象ドメイン内から変更することはできません。また、管理対象ドメイン内でのグループ メンバーシップまたはグループ属性の変更は許可されていません。ただし、Azure AD (PowerShell/Azure portal 経由) またはオンプレミスの AD でのユーザー属性またはパスワードの変更はすべて、AAD-DS 管理対象ドメインに同期されます。

#### グループ ポリシー
高度なグループ ポリシーの構築は、AAD DS の管理対象ドメインではサポートされていません。たとえば、ドメイン内の各カスタム OU で個別の GPO を作成およびデプロイしたり、GP 対象向けに WMI フィルター処理を使用したりすることはできません。‘AADDC Computers’ と ‘AADDC Users’ コンテナーのそれぞれについて組み込みの GPO があり、グループ ポリシーを構成するようにカスタマイズできます。

#### 地理的に分散したデプロイ
Azure AD Domain Services の管理対象ドメインは、Azure の 1 つの仮想ネットワーク内のみで使用できます。ドメイン コントローラーを世界の複数の Azure リージョンで使用できるようにする必要がある場合は、Azure IaaS VM でドメイン コントローラーをセットアップするのが良いかもしれません。


## '自作' (DIY) AD デプロイ オプション
お客様のデプロイ ユースケースでは、Windows Server AD インストールで提供される機能の一部が必要かもしれません。このような場合は、次の自作 (DIY) オプションのいずれかを検討してください。

- **スタンドアロン クラウド ドメイン:** ドメイン コントローラーとして構成されている Azure 仮想マシンを使用して、スタンドアロンの ‘クラウド ドメイン’ をセットアップする。このインフラストラクチャは、オンプレミスの AD 環境には統合されません。このオプションでは、クラウドの VM にログインする、または VM を管理するために、’クラウド資格情報’ の別のセットが必要になります。

- **リソース フォレストのデプロイ:** ドメイン コント ローラーとして構成されている Azure の仮想マシンを使用して、リソース フォレスト トポロジ内にドメインを設定できます。次に、オンプレミスの AD 環境との AD 信頼関係を構成できます。クラウドのこのリソース フォレストに、コンピューター (Azure VM) をドメイン参加させることができます。ユーザー認証は、オンプレミスのディレクトリへの VPN/ExpressRoute 接続経由で発生します。

- ** オンプレミスのドメインを Azure に拡張:** Azure VM をオンプレミスの AD に参加させるために、VPN/ExpressRoute 接続を使用して、オンプレミスのネットワークに Azure Virtual Network を接続することができます。別の方法は、Azure でのオンプレミスのドメインのレプリカ ドメイン コントローラーを VM として昇格させることです。次に、それを設定し、オンプレミスのディレクトリに VPN/ExpressRoute 接続経由でレプリケートすることができます。このデプロイ モードは、オンプレミスのドメインを Azure に効果的に拡張します。

> [AZURE.NOTE] 自作オプションがデプロイ ユースケースに適していることを確認できます。どの機能によってユーザーが将来 Azure AD Domain Services を選択するかを理解するために、[フィードバックの共有](active-directory-ds-contact-us.md)を検討してください。このフィードバックは、デプロイのニーズとユースケースに合わせたサービスの拡張に活用させていただきます。

「[Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](https://msdn.microsoft.com/library/azure/jj156090.aspx)」は発行済みであり、自作インストールを容易にします。


## 関連コンテンツ
- [機能 - Azure AD Domain Services](active-directory-ds-features.md)

- [デプロイ シナリオ - Azure AD Domain Services](active-directory-ds-scenarios.md)

- [Azure Virtual Machines での Windows Server Active Directory の展開ガイドライン](https://msdn.microsoft.com/library/azure/jj156090.aspx)

<!---HONumber=AcomDC_0928_2016-->