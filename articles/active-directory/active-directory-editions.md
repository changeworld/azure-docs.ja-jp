<properties
	pageTitle="Azure Active Directory のエディション | Microsoft Azure"
	description="Azure Active Directory の無料エディションと有料エディションの選択について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/17/2015"
	ms.author="markvi"/>

# Azure Active Directory のエディション

Microsoft のオンライン ビジネス サービスはすべて、サインオンやその他の ID のニーズを Azure Active Directory に依存しています。Microsoft のオンライン ビジネス サービス (Office 365、Microsoft Azure など) をサブスクライブすると、Azure Active Directory (Azure AD) と、以下で説明する Free エディションのすべての機能へのアクセス許可が与えられます。

Azure Active Directory は、従業員、パートナー、顧客向けにクラウドで包括的な ID およびアクセス管理機能を提供するサービスです。このサービスには、開発者向けにディレクトリ サービス、高度な ID 管理、標準に準拠した機能豊富なプラットフォーム、および独自のアプリケーションや事前に統合された多数のアプリケーションに対応したアプリケーション アクセス管理が集約されています。Azure Active Directory Free エディションでは、ユーザーとグループの管理、オンプレミスのディレクトリとの同期、Azure、Office 365、および Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox などの多くの人気のある SaaS アプリケーション間でのシングル サインオンを実現することができます。Azure Active Directory の詳細については、「[Azure AD とは](active-directory-whatis.md)」を参照してください。


Azure Active Directory を強化するには、Azure Active Directory Basic エディションおよび Premium エディションを使用することで有料の機能を追加できます。Azure Active Directory の有料エディションは、既存の無料のディレクトリ上に構築されます。セルフサービス、拡張された監視機能、セキュリティ レポート、Multi-Factor Authentication (MFA)、セキュリティで保護されたモバイル ユーザーのアクセスなどエンタープライズ クラスの機能を提供します。

Office 365 サブスクリプションには、下の比較表に記載されているその他の Azure Active Directory 機能が含まれます。


> [AZURE.NOTE]これらのエディションの価格オプションについては、「[Azure Active Directory の価格](https://azure.microsoft.com/ja-JP/pricing/details/active-directory/)」を参照してください。<br>Azure Active Directory Premium と Azure Active Directory Basic は、現在、中国ではサポートされていません。詳細については、Azure Active Directory フォーラムからお問い合わせください。


- **Azure Active Directory Basic**: このエディションは、クラウド優先のニーズを持つタスク ワーカー向けに設計されており、クラウド中心のアプリケーション アクセスおよびセルフサービス ID 管理のソリューションを提供します。Azure Active Directory Basic エディションでは、グループベースのアクセス管理、クラウド アプリケーション向けのセルフサービスのパスワード リセット、Azure Active Directory アプリケーション プロキシ (Azure Active Directory を使用してオンプレミス Web アプリケーションを発行するため) などの生産性の強化とコスト削減の機能が提供され、すべてがアップタイム 99.9% のエンタープライズレベルの SLA によって保証されます。
 
- **Azure Active Directory Premium**: Azure Active Directory Premium エディションは、より要求の厳しい ID およびアクセスの管理を必要とする組織を支援することを目的として、機能豊富なエンタープライズレベルの ID 管理機能が追加され、ハイブリッド ユーザーがオンプレミスの機能とクラウドの機能にシームレスにアクセスできるようになっています。このエディションには、クラウド内のアプリケーション アクセス、セルフサービスの ID とアクセスの管理 (IAM)、ID 保護とセキュリティに関して、ハイブリッド環境のインフォメーション ワーカーと ID 管理者が必要とするすべてが含まれています。また、動的なグループやセルフサービス グループ管理のような高度な管理と委任のリソースをサポートします。さらに、Microsoft Identity Manager (オンプレミスの ID およびアクセス管理スイート) が含まれており、オンプレミス ユーザー向けのセルフ サービスのパスワード リセットなどのソリューションを実現するクラウドの書き戻し機能を提供します。

すぐに Active Directory Premium にサインアップして使用する方法については、「[Azure Active Directory Premium の概要](active-directory-get-started-premium.md)」を参照してください。


> [AZURE.NOTE]さまざまな Azure Active Directory の機能を "従量課金制" のエディションで使用できます。
>
>- Active Directory B2C は、コンシューマー向けアプリケーションの ID およびアクセスの管理ソリューションです。詳しくは、「[Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)」を参照してください。
 
>-	Azure Multi-Factor Authentication は、ユーザーごとまたは認証プロバイダーごとに使用できます。詳細については、「[Azure Multi-Factor Authentication とは](multi-factor-authentication.md)」を参照してください。


<br>




| 機能の種類| 特徴| Free エディション| Basic エディション| Premium エディション |
| --- | --- | --- | --- | --- |
| **共通機能**| サービスとしてのディレクトリ| ![○][12] 最大 500,000 ユーザー [1]| ![○][12] オブジェクトの制限なし| ![○][12] オブジェクトの制限なし|
| | [UI または Windows PowerShell コマンドレットを使用するユーザーとグループの管理](active-directory-administer.md)| ![○][12]| ![○][12]| ![○][12]|
| | [デバイス登録](active-directory-conditional-access-device-registration-overview.md)| ![○][12]| ![○][12]| ![○][12]|
| | [SaaS アプリケーションとカスタム アプリケーションへの SSO ベースのユーザー アクセスのためのアクセス パネル ポータル](active-directory-saas-access-panel-introduction.md)| ![○][12] ユーザーごとに最大 10 個のアプリケーション [2]| ![○][12] ユーザーごとに最大 1 個のアプリケーション [2]| ![○][12] アプリケーションの制限なし|
| | [ユーザー ベースのアプリケーション アクセス管理とプロビジョニング](active-directory-saas-app-provisioning.md)| ![○][12]| ![○][12]| ![○][12]|
| | クラウド ユーザーに対するセルフ サービスのパスワード変更| ![○][12]| ![○][12]| ![○][12]|
| | [Azure AD Connect - オンプレミスのディレクトリと Azure Active Directory 間の同期に使用](active-directory-aadconnect.md)| ![○][12]| ![○][12]| ![○][12]|
| | [標準的なセキュリティ レポート](active-directory-view-access-usage-reports.md)| ![○][12]| ![○][12]| ![○][12]|
| | [B2B コラボレーション (組織間のコラボレーション) (プレビュー)](active-directory-b2b-collaboration-overview.md)| ![○][12]| ![○][12]| ![○][12]|
| **Premium および Basic の機能**| [サインインおよびアクセス パネル ページでの会社のロゴと色のカスタマイズ](active-directory-add-company-branding.md)| Office 365 サブスクリプションに含まれる [4]| ![○][12]| ![○][12]|
| | [クラウド ユーザーに対するセルフ サービスのパスワード リセット](active-directory-passwords.md)| Office 365 サブスクリプションに含まれる [4]| ![○][12]| ![○][12]|
| | [アプリケーション プロキシ: オンプレミスの Web アプリケーションに対するセキュリティで保護されたリモート アクセスと SSO](active-directory-application-proxy-get-started.md)| | ![○][12]| ![○][12]|
| | [グループ ベースのアプリケーション アクセス管理とプロビジョニング](active-directory-accessmanagement-group-saasapps.md)| | ![○][12]| ![○][12]|
| | [高可用性 SLA アップタイム (99.9%)](https://azure.microsoft.com/ja-JP/support/legal/sla/)| Microsoft Online Services SLA [5]| ![○][12]| ![○][12]|
| **Premium のみの機能**| [高度なアプリケーション使用状況レポート](active-directory-view-access-usage-reports.md)| | | ![○][12]|
| | [クラウド ユーザーに対するセルフ サービスのグループ管理](Self-service group management for users in Azure AD)| | | ![○][12]|
| | [オンプレミスへのライトバックを備えたセルフ サービスのパスワード リセット](active-directory-passwords-getting-started.md/#enable-users-to-reset-or-change-their-ad-passwords)| | | ![○][12]|
| | [Microsoft Identity Manager (MIM) ユーザー ライセンス - オンプレミスの ID とアクセスの管理用](http://www.microsoft.com/ja-JP/server-cloud/products/microsoft-identity-manager/default.aspx)| | | ![○][12] [3]|
| | [詳細な異常セキュリティ レポート (Machine Learning に基づく)](active-directory-view-access-usage-reports.md)| | | ![○][12]|
| | [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)| | | ![○][12]|
| | [クラウド ユーザー向けの Multi-factor Authentication サービス](multi-factor-authentication.md)| Office 365 サブスクリプションに含まれる [4]| | ![○][12]|
| | [オンプレミス ユーザー向けの Multi-factor Authentication サーバー](multi-factor-authentication.md)| | | ![○][12]|
| | オンプレミスの Active Directory インフラストラクチャの正常性を監視し、利用状況分析を取得する [Azure Active Directory Connect Health](active-directory-aadconnect-health.md)| | | ![○][12]|




[1] ディレクトリ サービスとして Azure Active Directory を利用する Office 365、Microsoft Intune、その他の Microsoft オンライン サービスには、500 K オブジェクトの制限は適用されません。

[2] Azure Active Directory Free と Azure Active Directory Basic では、各 SaaS アプリケーションへのアクセスが割り当てられているエンド ユーザーは、アクセス パネルに最大 10 個のアプリケーションを表示することができ、それらのアプリケーションへの SSO アクセスが可能です (エンド ユーザーの SSO を管理者があらかじめ構成している場合)。Free でも管理者が SSO を構成してユーザー アクセスを割り当てることができる SaaS アプリの数に制限はありませんが、エンド ユーザーのアクセス パネルに一度に表示されるアプリは 10 個だけです。

[3] Microsoft Identity Manager サーバー ソフトウェアの使用権は、Windows Server ライセンス (任意のエディション) と共に付与されます。Microsoft Identity Manager は Windows Server オペレーティング システム上で実行されるので、サーバーで Windows Server の有効なライセンス版が実行されていれば、そのサーバーに Microsoft Identity Manager をインストールして使用できます。Microsoft Identity Manager サーバーに他の個別のライセンスは不要です。

[4] Office 365 に含まれる Azure AD の有料機能は、Office 365 アプリケーションにアクセスするときのみ使用できるように制限されています。

[5] Microsoft の各オンライン ビジネス サービス、Office 365、Microsoft Intune、その他、ディレクトリ サービスに Azure Active Directory を使用するすべての Microsoft オンライン サービスに、Azure Active Directory の使用にも適用される独自のサービス SLA が付随しています。詳細については、[Microsoft online services SLA](https://gallery.technet.microsoft.com/online-SLA-ea09109e) に関するページを参照してください。




## 次の手順

- [Azure Active Directory Premium の概要](active-directory-get-started-premium.md)
- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)
- [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=Oct15_HO4-->