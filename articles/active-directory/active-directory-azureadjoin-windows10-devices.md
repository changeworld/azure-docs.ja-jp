<properties 
	pageTitle="職場での Windows 10 デバイスの使用 | Microsoft Azure" 
	description="Windows 10 で企業がデバイスをプロビジョニングして使用する際の異なる方法の比較を、ユーザーおよび IT 部門が利用できる機能のスナップショットで示しています。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="femila"/>

# 職場での Windows 10 デバイスの使用

Windows 10 では、ユーザーが職場のリソースにアクセスする、セキュリティで保護された便利なモデルを 3 つ提供しています。

1. **Azure AD Join**。Windows 10 からの新機能であり、Office 365 などのクラウドのリソースにアクセスする作業者を対象とした、セルフサービスの作業プロビジョニング エクスペリエンスです。
1. **Domain Join**。Windows 10 での Azure AD への接続時の改善で、オンプレミスのアプリやリソースに大規模な投資を行っている組織を対象としています。
1. **新しい単純化された BYOD エクスペリエンス**。個人のデバイス上の作業リソースへのアクセスを単純化するためにユーザーが職場アカウントを追加できます。

次の表では、Windows 10 で企業がデバイスをプロビジョニングして使用する際の異なる方法の比較を、ユーザーおよび IT 部門が利用できる機能のスナップショットで示しています。

| | Domain Join | Azure AD Join | 個人デバイス |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| 職場アカウントを使用した Windows デバイスでのサインイン | あり | はい | いいえ |
| Office 365 および Azure AD アプリでのユーザーの SSO。[1] シングル サインオン (一度サインインしたら再度資格証明を入力する必要なく企業のリソースにアクセスできる機能)。 | あり | あり | あり |
| Kerberos または NTLM アプリへのユーザーの SSO | あり | 制限あり | VPN 経由 |
| Microsoft Passport と Windows Hello による、職場アカウント用の強力な認証と便利なサインイン | あり | あり | あり |
| 職場アカウントを使用した企業の Windows ストアへのアクセス (Microsoft アカウントは不要です) | あり | あり | あり |
| 職場アカウントを使用した、企業に準拠したデバイス間でのユーザー設定のローミング | あり | あり | あり |
| 組織のデバイス ポリシーに準拠したデバイスからの組織のアプリへのアクセスの制限 | あり | あり | あり |
| 任意の場所から作業するためのユーザーによるデバイスのセルフサービス プロビジョニング | いいえ | あり | あり |
| デバイスを管理する機能 | GP/SCCM 経由の場合あり | あり | あり |

##職場所有のデバイス: Azure AD Join および Domain Join

Windows 10 では、職場所有のデバイスから職場のリソースにアクセスするモデルを 2 つ用意しています。

- Azure AD Join
- Domain Join

 いずれも組織のニーズと要件に応じた有効なオプションです。組織は、場合によっては両方のデプロイメント方法を使用した方がメリットがあることがあります。

## どのようなときに Azure Active Directory Join を使用するか

Azure AD Join は、Windows 10 の新しいセルフサービスの作業プロビジョニング エクスペリエンスです。主に、Office 365 などのクラウド内の作業リソースにアクセスする作業者を対象としています。企業は、これにより、PC、タブレット、および携帯電話を軽量に構成できまます。デバイスは、Windows プラットフォーム間で一貫性のあるコントロールを使用して、MDM を介して管理されます。

**Azure AD Join は次のいずれかの理由で使用されます。**

1.	デバイスを携帯する作業員用に、セルフサービスのプロビジョニングを有効にしたい場合。
2.	タブレットや携帯電話など職場所有のモバイル デバイスをユーザーに提供したい場合。
3.	季節労働者、請負業者や学生などのユーザーのグループを AD ではなく Azure AD で管理したい場合。
4.	リモートの支店の作業員に制限のあるオンプレミスのインフラに対する参加機能を提供したい場合。
5.	オンプレミスに AD がない場合。

一部の組織は、特にクラウドにリソースの多くまたはすべてを移行する際に、職場所有のデバイスをデプロイする主な手段として Azure AD Join を使用します。AD と Azure AD の両方があるハイブリッドな組織でも、ユーザーや部門によっていずれかの手段のデプロイを選択できます。学区および大学では、AD でスタッフを管理し、Azure AD で生徒を管理することができます。または一部の企業では、リモートのオフィスや営業部門で Azure AD を使用することができます。ハイブリッドな組織内では、Azure AD Join と Domain Join の両方の手段を使用できます。Azure AD Join は、作業環境にデバイスをデプロイするための優れた助けとなります。

**クラウド経由で企業リソースにアクセスすることが多い場合、組織には次のようなメリットがあります。**

- オンプレミスの ID インフラストラクチャの依存関係を削除できます。
- セルフサービス構成を可能にして、イメージング ソリューションから離れることにより、デバイスのデプロイ モデルを簡略化できます。
- MDM を使用して、さまざまなプラットフォーム上のすべてのデバイスを管理できます。

Azure AD Join の詳細については、「[Azure Active Directory Join を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-overview.md)」を参照してください。

## どのようなときに Domain Join を使用するか (または使用し続けるか)

Domain Join は、過去 15 年以上にわたり、組織がデバイスを接続して作業を行ってきた従来の方法です。この方法により、ユーザーは AD の職場アカウントを使用して自分のデバイスにサインインでき、IT 部門はこれらのデバイスを一元的に全面的に管理できました。通常、組織はイメージング メソッドに依存しデバイスをプロビジョニングし、また一般的に System Center Configuration Manager (SCCM) またはグループ ポリシーを使用してそれらを管理しています。

**Domain Join は、企業で次のいずれかの理由で使用されます。**

- NTLM または Kerberos を使用するデバイスに、Win32 アプリがデプロイされている場合。
- デバイスの管理に、GP または SCCM/DCM が必要な場合。
- 今後も社員のデバイスの構成にイメージング ソリューションを使用したい場合。

**Windows 10 の Domain Join では、Azure AD に接続すると、次のようなメリットもあります。**

- Microsoft Passport と Windows Hello による、職場アカウント用の強力なデバイスバインドの認証と便利なサインイン。
- 職場アカウントを使用した企業の Windows ストアへのアクセス (Microsoft アカウントは不要です)。
- 職場アカウントを使用した、デバイス間でのユーザー設定の企業準拠のローミング (Microsoft アカウントは不要です)。
- 組織のデバイス ポリシーに準拠したデバイスからの組織のアプリへのアクセスの制限。
- Azure AD Join の詳細については、「[Azure Active Directory Join を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-overview.md)」を参照してください。

##職場アカウントでの個人所有のデバイスでの作業の有効化

Windows 10 では、企業で BYOD をサポートできるよう、ユーザーに PC、タブレットまたは携帯電話に “職場または学校のアカウント” を追加できるようにしています。職場のアカウントを追加することにより、デバイスは Azure AD に登録され、デバイスは必要に応じて組織がデバイスに用に構成した MDM に登録されます。これらのデバイスは、ディレクトリに '登録済み' または'Azure AD Joined' と表示されます。この情報を使用し、IT 部門は必要に応じて職場所有のデバイスよりも個人所有のデバイスを使いやすくすることができる、別のポリシーを適用することも可能です。

ユーザーは、最初に職場のアプリケーションに、または手動で設定アプリケーションを使用してアクセスするとき、職場のアカウントを個人所有のデバイスに非常に簡単に追加できます。この職場のアカウントは、組織のリソースに SSO を提供します。

Azure AD Join の詳細については、「[Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](active-directory-azureadjoin-devices-group-policy.md)」を参照してください。

## Domain Join または Azure AD Join の有効化 

前述のすべての方法 (Domain Join、Azure AD Join、職場アカウントの追加) には Windows 10 のユーザー エクスペリエンスへのエントリ ポイントがあります。ただし、すべては IT 部署でエクスペリエンスが機能するように、この機能をインフラストラクチャで有効にする必要があります。

##Azure AD Join

一連の任意のユーザーに Azure AD Join をデプロイするには、以下が必要です。
---------------------------------------------------------------------------
- Azure AD サブスクリプションの所持。
- MDM 自動登録などさらに機能が必要な場合は、Azure AD Premium のサブスクリプションも必要です。
- Azure AD Premium のサブスクリプションの所持。
- MDM の所持 (Intune のサブスクリプションまたは Office 365 用または Azure AD を統合している任意のサード パーティ MDM ベンダーの MDM (詳細は、FAQ のセクションを参照))。
- ハイブリッド環境の場合、以下を実行することを強くお勧めします。
- オンプレミス ディレクトリを Azure AD に拡張するための Azure AD Connect のデプロイ。

##Domain Join

Domain Join は今まで通り動作しますが、Azure AD のメリットを得るには、以下を実行する必要があります。

- Azure AD サブスクリプションの所持。
- オンプレミス ディレクトリを Azure AD に拡張するための Azure AD Connect のデプロイ。
- ドメイン参加済みデバイスを Azure AD に接続するためのポリシー設定。
- Windows 10 での Domain Join の詳細については、「<link-to-DJ-in-Win10-deployment-guide>」を参照してください。
- ‘ドメイン参加済み’ デバイスへのアクセスを許可する、条件付きアクセスのためのポリシーの作成。互換デバイスを必要とする規則を有効にするには、以下が必要です。
- System Center Configuration Manager バージョン 1509 Technical Preview (TechNet のドキュメントおよびブログの投稿を参照してください)。

##BYOD および職場アカウントの追加

職場アカウントで BYOD を有効にするには、以下が必要です。

- Azure AD サブスクリプションの所持。
- MDM 自動登録などさらに機能が必要な場合は、Azure AD Premium のサブスクリプションも必要です。
- Azure AD Premium のサブスクリプションの所持。
- MDM の所持 (Intune のサブスクリプションまたは Office 365 用または Azure AD を統合している任意のサード パーティ MDM ベンダーの MDM (詳細は、FAQ のセクションを参照))。

##Microsoft Passport

さらに Microsoft Passport を有効にするには、以下が必要です。

- Microsoft Passport を使用した PKI インフラストラクチャ用の証明書ベースの認証のサポート。
- Azure AD Join および職場アカウント用の Microsoft Passport を使用した証明書ベース認証のサポート用の Intune サブスクリプション。
- Domain Join 用の Microsoft Passport を使用した証明書ベースの認証のサポート用に System Center Configuration Manager バージョン 1509 Technical Preview (TechNet のドキュメントおよびブログの投稿を参照してください)。
- 組織で Microsoft Passport を有効にするためのポリシーの設定。

PKI インフラストラクチャの代わりに、次を実行してキーに基づく Microsoft Passport を有効にすることができます。

- Windows Server 2016 ‘Production Preview 1’ DC のデプロイ (ドメインまたはフォレスト機能レベルは不要です。各 AD サイトにサービスを提供する冗長性用のいくつかの DC で十分です)。
- 組織で Microsoft Passport を有効にするためのポリシーの設定。
- Windows 10 での Microsoft Passport および Windows Hello の詳細については、「<link-to-MS-Passport-and-Windows-Hello-document>」を参照してください。

## よく寄せられる質問

###Azure AD にはどのサード パーティ MDM ベンダー製品を統合できますか。

Azure AD には Windows 10 での統合登録および条件付きアクセスが可能な次のベンダー製品を組み込むことができます。

- AirWatch by VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- SOTI オンプレミス MDM

###Windows 10 の Workplace Join について説明してください。
Windows 8.1 で Workplace Join は BYOD を有効にするために使用されていました。Windows 10 では、BYOD はこのドキュメントで既に説明している [職場アカウントを追加] を使用して有効になります。Azure AD に MDM を統合しない組織では、ユーザーは、**[設定]**、**[アカウント]**、**[職場のアクセス]** から手動で管理にデバイスを登録できます。

###ユーザーは Windows 10 のドメイン アカウントに Microsoft アカウント (MSA) を接続できますか。
Windows 10 ではできません。Windows 8.1 では、ドメインに参加しているデバイスを使用していたユーザーは、Live サービスで SSO などの特定のエクスペリエンスを有効にしたり、Windows ストアおよびユーザー設定のデバイス間でのユーザー設定のローミングを使用したりするために (Hotmail、Live、Outlook、XBox などの) MSA をドメイン アカウントに接続できました。Windows 10 では、MSA Connect の機能は提供終了しました。ユーザーは、追加のアカウントとして MSA を 1 つ以上追加して、Windows ストアなどのコンシューマー サービスで SSO を有効にできます。これは、**[設定]**、**[アカウント]**、**[アカウント]** で実行します。

MSA が接続されている Windows 8.1 ドメインに参加しているデバイスからアップグレードするユーザーは、使用するその他のアカウント一覧に、自動的に接続されている MSA が追加されます。


## 追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD Join の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->