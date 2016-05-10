<properties
	pageTitle="Azure Active Directory ドメイン サービス プレビュー: FAQ | Microsoft Azure"
	description="Azure Active Directory ドメイン サービスについてよく寄せられる質問"
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
	ms.date="03/31/2016"
	ms.author="maheshu"/>

# Azure Active Directory ドメイン サービス プレビュー: FAQ

このページでは、Azure Active Directory ドメイン サービス プレビューに関してよく寄せられる質問への回答が記載されています。常に最新情報をチェックしてください。

### トラブルシューティング ガイド
Azure AD Domain Services を構成または管理する際に生じる一般的な問題の解決策については、「[トラブルシューティング ガイド](active-directory-ds-troubleshooting.md)」を参照してください。


### 構成

#### 1 つの Azure AD ディレクトリに対して複数のドメインを作成することはできますか。
いいえ。1 つの Azure AD ディレクトリに対して Azure AD Domain Services によって対応されるドメインは 1 つだけ作成できます。

#### Azure AD Domain Services をサブスクリプション内の複数の仮想ネットワークで利用できまですか。
このサービスそのものが、そのようなシナリオを直接サポートすることはありません。Azure AD Domain Services は一度に 1 つの仮想ネットワークでのみ利用できます。ただし、Azure AD Domain Services を他の仮想ネットワークに公開するために複数の仮想ネットワーク間の接続を構成できます。記事「[Azure で仮想ネットワークに接続する](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)」の説明に従って、これを実行できます。

#### PowerShell を使用して Azure AD ドメイン サービスを有効にできますか。
PowerShell/Azure AD Domain Services の自動デプロイは、現時点では利用できません。

#### Azure AD Domain Services は新しい Azure ポータルで利用できますか。
いいえ。Azure AD ドメイン サービスは、 [Azure クラシック ポータル](https://manage.windowsazure.com)でのみ構成できます。今後、[Azure ポータル](https://portal.azure.com)までサポートを拡大する予定です。

#### Azure AD ドメイン サービスの管理対象ドメインにドメイン コント ローラーを追加することはできますか。
いいえ。管理対象ドメインは Azure AD ドメイン サービスによって提供されるドメインです。このドメインに対してドメイン コントローラーをプロビジョニング、構成、または管理する必要はありません。これらの管理作業は Microsoft によるサービスとして提供されます。そのため、管理対象ドメインに追加のドメイン コント ローラー (読み取り/書き込みも読み取り専用も) を追加することはできません。

### 管理と操作

#### リモート デスクトップを使用して管理対象ドメインのドメイン コントローラーに接続できますか。
いいえ。これは管理対象ドメインであるため、リモート デスクトップ経由でドメインのドメイン コントローラーに接続することはできません。"AAD DC 管理者" グループのメンバーは、Active Directory 管理センター (ADAC) や AD PowerShell などの AD 管理ツールで管理対象ドメインを管理できます。これらのツールは、"リモート サーバー管理ツール" 機能を使用して、管理対象ドメインに参加している Windows サーバーにインストールされます。

#### Azure AD Domain Services を有効にしています。このドメインに参加しているドメイン コンピューターでは、どのユーザー アカウントを使用できますか。
管理グループ ("AAD DC 管理者" など) に追加したユーザー アカウントのいずれかは、ドメインに参加しているコンピューターで使用できます。さらに、このグループのユーザーには、ドメインに参加しているコンピューターへのリモート デスクトップ アクセス権が付与されます。

#### Azure AD Domain Services によって提供されるドメインでドメイン管理者特権を使用できますか。
いいえ。これは管理されたサービスであるため、ドメインに対する管理者特権は付与されません。つまり、"ドメイン管理者" 特権と "エンタープライズ管理者" 特権はどちらも、ドメインでは利用できません。Azure AD ディレクトリ内の既存のドメイン管理者グループまたはエンタープライズ管理者グループにも、ドメインに関するドメイン/エンタープライズ管理者特権は付与されません。

#### LDAP または他の AD 管理ツールを使用して、Azure AD Domain Services によって用意されるドメインのグループ メンバーシップを変更できますか。
いいえ。Azure AD Domain Services によってサービスされるドメインのグループ メンバーシップは変更できません。ユーザー属性に対しても同様です。ただし、Azure AD またはオンプレミスのドメインのいずれかで、グループ メンバーシップまたはユーザー属性を変更できます。このような変更は、Azure AD Domain Services に自動的に同期されます。

#### Azure AD ドメイン サービスによって提供されるドメインのスキーマは拡張できますか。
いいえ。スキーマは、Microsoft が管理対象ドメインを管理することで管理されます。Azure AD ドメイン サービスでは、スキーマの拡張機能はサポートされていません。

#### Azure AD ドメイン サービスで提供される DNS レコードは変更できますか。
はい。"AAD DC 管理者" グループに属するユーザーには "DNS 管理者" 特権が付与されているため、管理対象ドメインの DNS レコードを変更することができます。これらのユーザーは、管理対象ドメインに参加している Windows Server を実行しているコンピューター上で DNS マネージャー コンソールを使用して、DNS を管理できます。DNS マネージャー コンソールを使用するには、"リモート サーバー管理ツール" オプション機能の一部である "DNS サーバー ツール" をサーバーにインストールします。[DNS の管理、監視、およびトラブルシューティングを行うためのユーティリティ](https://technet.microsoft.com/library/cc753579.aspx)の詳細については、TechNet をご覧ください。

### 課金と可用性

#### これは有料のサービスですか。
このサービスは、パブリック プレビュー期間中は特別割引価格で利用できます。正規料金の課金は、サービスが一般公開 (GA) されたときに開始されます。詳細については、価格に関するページを参照してください。

#### サービスの無料試用版はありますか。
このサービスは、Azure の無料試用版に含まれています。[1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

#### Enterprise Mobility Suite (EMS) の一部として Azure AD Domain Services を取得できますか。
いいえ。Azure AD Domain Services は従量課金の Azure サービスであり、EMS には含まれていません。Azure AD ドメイン サービスは、Azure AD のすべての SKU (Free、Basic、Premium) で利用でき、使用状況に応じて 1 時間単位で課金されます。

#### このサービスは、どの Azure のリージョンで利用できますか。
Azure AD Domain Services を利用できる Azure のリージョンの一覧については、[リージョンに関するページ](active-directory-ds-regions.md)を参照してください。

#### Azure AD Domain Services はいつ一般公開される予定ですか。
現時点では、このサービスの一般公開予定は決まっていません。

<!---HONumber=AcomDC_0427_2016-->