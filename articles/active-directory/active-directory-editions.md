<properties
	pageTitle="Azure Active Directory のエディション | Microsoft Azure"
	description="Azure Active Directory の無料エディションと有料エディションの選択について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="curtand"/>

# Azure Active Directory のエディション

Azure Active Directory は、クラウドで包括的な ID およびアクセス管理機能を提供するサービスです。開発者向けにディレクトリ サービス、高度な ID 管理、アプリケーション アクセス管理、および標準に準拠した機能豊富なプラットフォームが集約されています。詳細については、[このビデオを参照してください](http://azure.microsoft.com/documentation/videos/teched-europe-2014-cloud-identity-microsoft-azure-active-directory-explained/)。

Microsoft Azure Active Directory の多くの無料機能セットが基になっている Azure Active Directory Premium および Basic エディションでは、ID およびアクセスの管理に対するニーズがさらに厳しい企業のためにいっそう高度な機能セットが提供されています。これらのエディションの価格オプションについては、「[Azure Active Directory 料金](http://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。Azure をサブスクライブするときは、Azure Active Directory の以下の無料および有料エディションから選択できます。

- **Free** - Azure Active Directory の Free エディションは、すべての Azure サブスクリプションに含まれています。ライセンスの取得やインストールは必要ありません。このエディションでは、ユーザー アカウントを管理し、オンプレミス ディレクトリと同期し、Azure、Office 365、および Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox などの多くの人気のある SaaS アプリケーション間でのシングル サインオンを実現することができます。
- **Basic** - Azure Active Directory Basic エディションでは、クラウド優先のニーズを持つタスク ワーカーのために、アプリケーション アクセスおよびセルフ サービス ID 管理の要件が提供されます。Azure Active Directory Basic エディションでは、Azure Active Directory Free の全機能に加えて、グループ ベースのアクセス管理、クラウド アプリケーション向けのセルフサービスのパスワード リセット、Azure Active Directory アプリケーション プロキシ (Azure Active Directory を使用してオンプレミス Web アプリケーションを発行するため)、エンタープライズおよびコンシューマー向けクラウド アプリケーションを起動するためのカスタマイズ可能な環境、アップタイム 99.9% のエンタープライズ レベルの SLA が提供されます。Azure Active Directory Basic エディションのライセンスを所有する管理者は、Azure Active Directory Premium 試用版もアクティブ化できます。
- **Premium** - Azure Active Directory Premium Edition では、Azure Active Directory Free および Basic エディションの全機能に加えて、以下で説明する機能豊富なエンタープライズ レベルの ID 管理機能が提供されます。

すぐに Active Directory Premium にサインアップして使用する方法については、「[Azure Active Directory Premium の概要](active-directory-get-started-premium.md)」を参照してください。

> [AZURE.NOTE]Azure Active Directory Premium と Azure Active Directory Basic は、現在、中国ではサポートされていません。詳細については、[Azure Active Directory フォーラム](http://feedback.azure.com/forums/169401-azure-active-directory)からお問い合わせください。

## Azure Active Directory Basic の機能

Active Directory Basic エディションは Azure Active Directory の有料プランであり、以下の機能が含まれます。

- **企業のブランド化** - エンド ユーザー エクスペリエンスをよりよくするために、会社のロゴや配色パターンを組織のサインインおよびアクセス パネル ページに追加できます。ロゴを追加した後は、異なる言語およびロケール用にローカライズ版のロゴを追加することもできます。詳細については、「[サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)」を参照してください。
- **グループ ベースのアプリケーション アクセス** - グループを使用してユーザーをプロビジョニングし、何千もの SaaS アプリケーションにユーザー アクセスを一括して割り当てます。これらのグループは、クラウド内でのみ作成することも、オンプレミスの Active Directory から同期化されている既存のグループを利用することもできます。詳細については、「[Assign access for a group to a SaaS application in Azure AD (Azure AD で SaaS アプリケーションへのアクセスをグループに割り当てる)](active-directory-accessmanagement-group-saasapps.md)」を参照してください。
- **セルフ サービスのパスワード リセット** - Azure では、ディレクトリ管理者は常にパスワードをリセットできます。Azure Active Directory Basic では、ディレクトリ内のすべてのユーザーは Office 365 と同じサインイン エクスペリエンスを使用してパスワードをリセットできるので、ユーザーがパスワードを忘れてヘルプデスクに問い合わせることが減ります。詳細については、「[Azure AD でのパスワード管理](https://msdn.microsoft.com/library/azure/dn510386.aspx)」を参照してください。
- **99.9% のエンタープライズ SLA** - Azure Active Directory Basic サービスでは 99.9% 以上の可用性が保証されます。
- [**Azure Active Directory アプリケーション プロキシ**](https://msdn.microsoft.com/library/azure/dn768214.aspx) - Azure Active Directory を使用してクラウドから SharePoint や Exchange/OWA などのオンプレミス アプリケーションに安全にアクセスできます。

## Azure Active Directory Premium の機能

Active Directory Premium Edition は、Azure Active Directory の有料プランであり、Free エディションと Basic エディションのすべての機能に加えて、以下の機能が含まれています。

- **セルフ サービスのグループ管理** - Azure Active Directory Premium では、グループの作成、他のグループへのアクセスの要求、他のユーザーが要求を承認できるようにグループの所有権の委任、グループのメンバーシップの管理などを、ユーザーができるようにすることで、日常的なグループの管理が簡略化されます。

    詳細については、「[Azure AD でのユーザーのセルフ サービス グループ管理](https://msdn.microsoft.com/library/azure/dn641267.aspx)」を参照してください。

- **高度なセキュリティ レポートとアラート** - より高度な異常性を示す詳細なログおよび一貫性のないアクセス パターンのレポートを表示することにより、クラウド アプリケーションへのアクセスを監視および保護できます。高度なレポートは Machine Learning に基づいており、アクセスのセキュリティを強化して潜在的な脅威に対応するための新しい知見を得ることができます。

    詳細については、「[アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)」を参照してください。

- **Multi-factor Authentication (多要素認証)** - Multi-factor Authentication が Premium に含まれるようになり、オンプレミス アプリケーション (VPN、RADIUS など)、Azure、Microsoft Online Services (Office 365、Dynamics CRM Online など)、および Azure Active Directory に既に統合されている多数の非 MS Cloud Services に安全にアクセスできます。Azure Active Directory の ID に対する Multi-factor Authentication を有効にするだけで、ユーザーは次回サインイン時に追加検証の設定を求められます。

    詳細については、「[Multi-Factor Authentication を Azure Active Directory に追加する](https://msdn.microsoft.com/library/azure/dn249466.aspx)」を参照してください。

- **Microsoft Identity Manager (MIM)** - Premium には、ハイブリッド ID ソリューションの任意の組み合わせをサポートするためにオンプレミス ネットワーク内で MIM サーバー (および CAL) を使用する権限を付与するオプションがあります。これは、オンプレミスのディレクトリとデータベースを Azure Active Directory に直接同期する必要がある場合に便利なオプションです。使用できる FIM サーバーの数に制限はありませんが、MIM CAL は Azure Active Directory Premium ユーザー ライセンスの割り当てに基づいて許可されます。

    詳細については、「[Deploy MIM 2010 R2 (MIM 2010 R2 のデプロイ)](https://www.microsoft.com/server-cloud/products/forefront-identity-manager/features.aspx)」を参照してください。

- **99.9% のエンタープライズ SLA** - Azure Active Directory Premium サービスでは 99.9% 以上の可用性が保証されます。

    詳細については、「[Active Directory Premium の SLA](http://azure.microsoft.com/support/legal/sla/)」を参照してください。

- **ライトバック機能付きパスワード リセット** - セルフ サービスのパスワード リセットはオンプレミス ディレクトリにライトバックできます。

- [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx): オンプレミスの Active Directory インフラストラクチャで正常性を監視し、使用状況の分析を取得します。



## エディションの比較: すべてのエディションに共通する機能

- サービスとしてのディレクトリ。Free エディションの場合、500 K オブジェクトに制限されています。ただし、Azure Active Directory のディレクトリ サービスを利用する Office 365、Microsoft Intune、その他の Microsoft オンライン サービスに対しては、500 K オブジェクトの制限は適用されません。Basic および Premium エディションにはオブジェクトの制限はありません。
- UI または Windows PowerShell コマンドレットを使用するユーザーとグループの管理
- デバイス登録
- SaaS およびカスタム アプリケーションへのシングル サインオン (SSO) ベースのユーザー アクセスのためのアクセス パネル ポータル。Azure Active Directory Free および Azure Active Directory Basic では、各 SaaS アプリへのアクセスが割り当てられているエンド ユーザーは、自身のアクセス パネルに最大 10 個のアプリが表示されており、それらのアプリにシングル サインオン アクセスできます (あらかじめ管理者によって SSO で構成されていることを前提とします)。Free でも管理者が SSO を構成してユーザー アクセスを割り当てることができる SaaS アプリの数に制限はありませんが、エンド ユーザーのアクセス パネルに一度に表示されるアプリは 10 個のみです。Azure Active Directory Premium にはアプリケーションの制限はありません。
- ユーザー ベースのアプリケーション アクセス管理とプロビジョニング
- クラウド ユーザーに対するセルフ サービスのパスワード変更
- ディレクトリ同期ツール: オンプレミスの Active Directory と Azure Active Directory の間の同期用
- 標準的なセキュリティ レポート

## エディションの比較: Premium と Basic の機能

- 高可用性 SLA アップタイム (99.9%)
- グループ ベースのアプリケーション アクセス管理とプロビジョニング
- サインインおよびアクセス パネル ページでの会社のロゴと色のカスタマイズ
- クラウド ユーザーに対するセルフ サービスのパスワード リセット
- アプリケーション プロキシ: オンプレミスの Web アプリケーションに対するセキュリティで保護されたリモート アクセスとシングル サインオン

## エディションの比較: Premium のみの機能

- クラウド ユーザーに対するセルフ サービスのグループ管理
- オンプレミスへのライトバックを備えたセルフ サービスのパスワード リセット
- Microsoft Identity Manager (MIM) サーバー ライセンス – オンプレミスのデータベースおよびディレクトリと Azure Active Directory との間の同期用
- 詳細な異常セキュリティ レポート (Machine Learning に基づく)
- [Cloud App Discovery](http://channel9.msdn.com/Series/EMS/Azure-Cloud-App-Discovery)
- 高度なアプリケーション使用状況レポート
- クラウド ユーザー向けの Multi-factor Authentication サービス
- オンプレミス ユーザー向けの Multi-factor Authentication サーバー
- Azure Active Directory Connect Health

## 現時点でパブリック プレビュー段階の機能

以下の機能は、現在パブリック プレビュー段階であり、間もなく追加される予定です。

- [管理単位](https://msdn.microsoft.com/library/azure/dn832057.aspx): Azure Active Directory の新しいリソース コンテナーであり、管理アクセス許可をユーザーのサブセットに委任したり、ユーザーのサブセットにポリシーを適用したりする場合に使用できます。
- Azure Active Directory に[独自の SaaS アプリケーション](https://msdn.microsoft.com/library/azure/dn893637.aspx)を追加します。
- Facebook、Twitter、LinkedIn にパスワードをロールオーバーします。詳細については、[こちらの記事](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)を参照してください。
- 動的グループ メンバーシップ。詳細については、[こちらの記事](https://msdn.microsoft.com/library/azure/dn913807.aspx)を参照してください。
- [条件付きアクセス](https://msdn.microsoft.com/library/azure/dn906877.aspx): アプリケーションごとの多要素認証です。
- HR アプリケーション統合: Workday
- Privileged Identity Management: Privileged Identity Management では、サービス レベル アグリーメントと法令遵守の要件を満たすために監視が強化されています。
- アプリケーションのセルフ サービス要求: 管理者が SaaS アプリの一覧をユーザーに提供して、ユーザーはその一覧から使用するアプリを選択できます。アプリケーションは、すぐに使用できる場合も、承認後に使用できる場合もあります。
- Azure Reporting API: Azure Active Directory のすべてのセキュリティ レポートのデータは、他の監視ツールや SIEM ツールで使用できます。


## 参照トピック

- [Azure Active Directory Premium の概要](active-directory-get-started-premium.md)
- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)
- [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)

<!---HONumber=Sept15_HO2-->