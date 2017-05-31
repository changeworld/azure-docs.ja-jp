---
title: "Azure ID について | Microsoft Docs"
description: "組織に最適な ID ガバナンスの決定を行えるように、Microsoft Azure ID ソリューションに関する基本的な用語、概念、および推奨事項について説明します。"
keywords: 
author: jeffgilb
manager: femila
ms.date: 5/12/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 8d550a9c2a6d2f28687aefc3b9db97ca3c68eb82
ms.contentlocale: ja-jp
ms.lasthandoff: 05/16/2017


---
# <a name="understand-azure-identity-solutions"></a>Azure ID ソリューションについて
Microsoft Azure Active Directory (Azure AD) は、包括的で可用性の高い ID およびアクセス管理クラウド ソリューションであり、核となるディレクトリ サービス、高度な ID ガバナンス、およびアプリケーション アクセス管理を組み合わせたものです。 Azure AD により、日常的に使用する Office 365、Salesforce.com、Box、ServiceNow、Workday など、[Azure AD アプリケーション ギャラリー](https://azure.microsoft.com/marketplace/active-directory/all/)にある事前に統合された数千の商用アプリやカスタム アプリへの[シングル サインオン (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) が簡単に実現されます。

Azure ID は、基本的に、Azure サブスクリプションの作成時にそのサブスクリプションに自動的に関連付けられる単一の Azure AD ディレクトリによって提供されます。 次の図に示すように、Azure AD は、Azure の ID サービスとして、ユーザー、アプリ、グループなどのクラウドベースのリソースに対するすべての ID 管理機能とアクセス制御機能を個々のテナント (組織) に提供します。

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure には、複雑さのレベルがさまざまな IDaaS (Identity as a Service) を利用して個々の組織のニーズを満たすための方法がいくつか用意されています。 この記事の残りの部分では、把握が必要な用語や概念に加え、選べる Azure ID オプションの中から最も適したものを選択をするための推奨事項について説明します。

## <a name="terms-to-know"></a>知っておくべき用語

組織の Azure ID サービス ソリューションに関する決定を行うには、Azure ID サービスに関連してよく使用される用語の基本を押さえておく必要があります。

|知っておくべき用語| Description|
|-----|-----|
|Azure サブスクリプション |サブスクリプションは、Azure クラウド サービスの支払いに使用され、通常はクレジット カードにリンクされます。 サブスクリプションを複数持つことはできるものの、サブスクリプション間でリソースを共有するのは難しい場合があります。|
|Azure テナント | Azure AD テナントは、1 つの組織を代表するものです。 テナントは、Azure AD の信頼された専用インスタンスであり、組織が Azure、Intune、Office 365 などの Microsoft クラウド サービスのサブスクリプションにサインアップしたときに提供されて所有します。 テナントは、専用の環境 (シングル テナント) または他の組織との共有環境 (マルチテナント) でサービスにアクセスできます。|
|Azure AD ディレクトリ | それぞれの Azure テナントは、テナントのユーザー、グループ、およびアプリケーションが含まれる、信頼された専用 Azure AD ディレクトリを保持します。 これは、テナント リソースに対する ID およびアクセス管理機能を実行するために使用されます。 Azure、Microsoft Intune、Office 365 などの Microsoft クラウド サービスにサインアップすると、組織を表す一意の Azure AD ディレクトリが自動的にプロビジョニングされます。そのため、"*テナント*"、*Azure AD*、および "*Azure AD ディレクトリ*" の各用語が同じ意味で使用される場合があります。 |
|カスタム ドメイン | 最初に Microsoft クラウド サービスのサブスクリプションにサインアップしたときに、テナント (組織) に対応する *.onmicrosoft.com* ドメイン名が作成されます。 ただし、ほとんどの組織は、ビジネス用のドメイン名やエンド ユーザーが会社のリソースにアクセスするために使用するドメイン名を 1 つ以上所有しています。 Azure AD にカスタム ドメイン名を追加することで、*alice@contoso.onmicrosoft.com* ではなく *alice@contoso.com* のような、ユーザーが見慣れたドメイン名を使用できます。 |
|Azure AD アカウント | Azure AD または他の Microsoft クラウド サービス (Office 365 など) を使用して作成される ID です。 このアカウントは、テナントの Azure AD に保存され、組織のクラウド サービスのすべてのサブスクリプションで利用できます。 |
|Azure サブスクリプション管理者| アカウント管理者は、Azure サブスクリプションのサインアップまたは購入を行ったユーザーです。 [アカウント センター](https://account.windowsazure.com/Home/Index)にアクセスし、サブスクリプションの作成、サブスクリプションの取り消し、サブスクリプションの課金の変更、サービス管理者の変更などのさまざまな管理タスクを実行する権限を持ちます。 |
|Azure AD 全体管理者 | Azure AD 全体管理者は、すべての Azure AD 管理機能を利用できます。 既定では、Microsoft クラウド サービス サブスクリプションにサインアップしたユーザーが自動的に全体管理者になります。 複数の全体管理者を置くこともできますが、[他の管理者ロール](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)をユーザーに割り当てることができるのは全体管理者に限られます。 |
|Microsoft アカウント | Microsoft アカウント (個人ユーザー向け) - Outlook (Hotmail)、Messenger、OneDrive、MSN、Xbox LIVE、Office 365 など、すべてのコンシューマー向け Microsoft 製品とクラウド サービスにアクセスできます。 これらの ID は、Microsoft が運営する Microsoft コンシューマー ID アカウント システムを使用して作成および保存されます。|
|職場または学校アカウント | 職場または学校アカウント (企業または教育機関で使用するために管理者が発行) は、Azure、Intune、Office 365 など、大規模企業向け Microsoft クラウド サービスへのアクセスを提供します。|


## <a name="concepts-to-understand"></a>理解しておくべき概念

ID に関する基本的な用語を把握したら、十分な情報に基づいて Azure ID サービスに関する決定を行うことができるように、次の主要な概念を押さえておいてください。

|理解しておくべき概念 |Description|
|-----|-----|
|[Azure サブスクリプションを Azure Active Directory に関連付ける方法](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |すべての Azure サブスクリプションには、Azure AD ディレクトリとの間に、ユーザー、サービス、およびデバイスの認証を行うための信頼関係があります。 "*同じ Azure AD ディレクトリを複数のサブスクリプションが信頼することはできますが、1 つのサブスクリプションが信頼できる Azure AD ディレクトリは 1 つに限られます*"。 この信頼関係は、サブスクリプションと他の Azure リソース (Web サイト、データベースなど) の間の関係と異なります。後者は、サブスクリプションの子リソースにより近いものです。 サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられている Azure AD 以外のリソースへのアクセスも停止します。 ただし、Azure AD ディレクトリは Azure 内に残るため、別のサブスクリプションをそのディレクトリと関連付けて、テナント リソースの管理を継続できます。|
|[Azure AD ライセンスのしくみ](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Enterprise Mobility Suite、Azure AD Premium、Azure AD Basic を購入またはアクティブにすると、有効期間や前払いライセンスを含め、ディレクトリとサブスクリプションが更新されます。 サブスクリプションがアクティブになると、サービスの機能は、Azure AD 全体管理者が管理し、ライセンスを付与されたユーザーが使用できるようになります。 割り当てられているライセンスの数や使用可能なライセンスの数などのサブスクリプション情報は、Azure Portal の **[Azure Active Directory]** > **[ライセンス]** ブレードで確認できます。 この場所は、ライセンスの割り当ての管理にも最適です。|
|[Azure Portal でのロールベースのアクセス制御](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|Azure のロールベースのアクセス制御 (RBAC) を利用すれば、Azure リソースのアクセス権を詳細に管理できます。 アクセス許可が多すぎると、アカウントが攻撃者による悪用の対象になりかねません。 アクセス許可が少なすぎると、従業員が業務を効率的に遂行できなくなる可能性があります。 RBAC を使用すると、すべてのリソース グループに適用される 3 つの基本的なロール (所有者、共同作成者、閲覧者) に基づいて、必要かつ適切なアクセス許可を従業員に付与することができます。 また、特定のニーズを満たすために、最大 2,000 個の[カスタム RBAC ロール](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)を独自に作成することもできます。 |
|[ハイブリッド ID](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|ハイブリッド ID は、[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) を使用してオンプレミスの Windows Server Active Directory (AD DS) と Azure AD を統合することで実現されます。 これにより、Azure AD と統合された Office 365、Azure、オンプレミスのアプリまたは SaaS アプリケーションのユーザーに、共通の ID を提供できます。 ハイブリッド ID を使用すると、オンプレミス環境の ID とアクセスに関する処理をクラウドに効果的に拡張できます。|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Windows Server AD DS と Azure AD の違い
Windows 2000 Server で初めて導入されたオンプレミスの Windows Server Active Directory Domain Services (AD DS) について理解している方であれば、おそらく ID サービスの基本概念を理解していることと思います。 ただし、Azure AD は単なるクラウドのドメイン コントローラーではないことを理解することも重要です。Azure AD は、Azure で IDaaS (identity as a service) を提供する、これまでにない方法です。クラウドベースの機能を完全に取り入れて現代の脅威から組織を保護するために、まったく新しい考え方が求められます。 IT 部門では、セキュリティ境界を保護するために、長年 AD DS が使用されてきました。しかし、境界を持たない現代の大企業で従業員、顧客、パートナーの ID ニーズをサポートするには、新しいコントロール プレーンが必要です。 Azure AD がその ID コントロール プレーンです。

セキュリティは、企業のファイアウォールからクラウドに移行しました。クラウドでは、(オンプレミス ユーザーかクラウド ユーザーかを問わず) ユーザーに 1 つの共通の ID を提供することで、Azure AD が企業リソースとアクセスを保護します。 これにより、ユーザーは、作業を完了させるために必要なアプリに、ほぼすべてのデバイスから、柔軟な方法で安全にアクセスできます。さらに、機械学習機能と詳細なレポートに基づくリスクベースのデータ保護コントロールがシームレスに提供されます。これは、IT 部門が会社のデータを安全に保つうえで必要不可欠です。

### <a name="extend-office-365-management-and-security-capabilities"></a>Office 365 の管理およびセキュリティ機能の拡張
Office 365 は既にお使いですか。 Azure AD を使用して Office 365 の組み込みの管理機能とセキュリティ機能を拡張し、すべてのリソースへのアクセスを保護すれば、デジタル変換を促進できます。その結果、従業員全員が安全な環境で滞りなく高い生産性を発揮できます。 Office 365 で提供される機能に加えて、Azure AD の機能を使用する場合、すべてのアプリに対してシングル サインオンを可能にする 1 つの ID でアプリケーション ポートフォリオ全体のセキュリティを保護できます。 デバイスの状態だけでなく、ユーザーや場所、アプリケーション、リスクにも基づいて、条件付きアクセス機能を拡張することができます。 Multi-Factor Authentication (MFA) 機能を使用すると、必要なときにさらに多くの保護が得られます。 ユーザー権限の管理機能が強化され、オンデマンドのジャストインタイム管理アクセスが提供されます。 パスワードを忘れた場合のリセット、アプリケーションへのアクセス要求、グループの作成と管理など、Azure AD に用意されているセルフサービス機能により、ユーザーの生産性が向上し、ヘルプデスク チケットを作成する機会が少なくなります。

> [!TIP]
> Azure AD ID 管理を Office 365 で使用する方法については、 [こちらの電子書籍を入手](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html)してください。

## <a name="microsoft-identity-solutions"></a>Microsoft の ID ソリューション

Microsoft Azure には、ユーザーの ID を管理するための方法がいくつか用意されています。その ID が完全にオンプレミスで管理されるか、クラウドでのみ管理されるか、その間のどこかで管理されるかは問いません。 これらのオプションには、Azure の自作 (DIY) AD DS、Azure Active Directory (Azure AD)、ハイブリッド ID、Azure AD Domain Services があります。

クラウドで必要となるフットプリントが小さい企業の場合は、Azure の**自作 (DIY) AD DS** を使用できます。 このオプションは、Azure 上の仮想マシン (VM) としてのデプロイに適した多くの Windows Server AD DS シナリオに対応しています。 たとえば、リモート ネットワークに接続されている遠隔地のデータセンターで実行されているドメイン コントローラーとして Azure VM を作成できます。 そこで VM がリモート ユーザーからの認証要求をサポートすることで、認証のパフォーマンスを高めることができる場合があります。 このオプションは、Azure 上で少数のドメイン コントローラーと単一の仮想ネットワークをホストすることにより、コストのかかるディザスター リカバリー サイトに替わる比較的低コストの方法としても適しています。 最後に、Windows Server AD DS は使用するものの、オンプレミスのネットワークや社内の Windows Server Active Directory には依存しないアプリケーション (SharePoint など) があれば、Azure にデプロイすることを検討してください。 そのようなケースでは、分離したフォレストを Azure にデプロイすることにより、SharePoint サーバー ファームの要件を満たすことができます。 また、オンプレミス ネットワークとオンプレミス Active Directory への接続を必要とするネットワーク アプリケーションのデプロイもサポートされています。

**Azure Active Directory (Azure AD)** スタンドアロンは、完全にクラウドベースの IDaaS (Identity and access management as a Service) ソリューションです。 Azure AD は、ユーザーとグループを管理する堅牢な機能のセットを提供します。 オンプレミスとクラウドのアプリケーション (Office 365 のような Microsoft の Web サービスや、さまざまな Microsoft 以外のサービスとしてのソフトウェア (SaaS) アプリケーションなど) への安全なアクセスを支援します。 Azure AD には、Free、Basic、および Premium の 3 つのエディションが用意されています。 Azure AD は、組織の実効性を向上させるほか、セキュリティを、境界ファイアウォールを越えて、Azure 機械学習やその他の高度なセキュリティ機能によって保護された新しいコントロール プレーンに拡張します。

先進的な考えを持つ多くの CIO や企業は、会社の長期的な方向性を見越したうえで、オンプレミスかクラウドベースの ID ソリューションかという二者択一ではなく、**ハイブリッド ID** ソリューションを介して自社のオンプレミスのディレクトリをクラウドに拡張しようとしています。 ハイブリッド ID を使用することで、現在のみならず将来にわたって、ユーザーが業務を遂行するために必要なアプリケーションへの安全で生産性の高いアクセスを提供する、真にグローバルな ID およびアクセス管理ソリューションを実現できます。

> [!TIP]
> CIO たちがどのように Azure Active Directory を IT 戦略の中核に据えてきたかについては、[Azure Active Directory の CIO 向けガイド](https://aka.ms/AzureADCIOGuide)をダウンロードしてご確認ください。

最後に、**Azure AD Domain Services** は、AD DS を活用して Azure VM 構成のごく簡便な制御を実現するためのクラウドベースのオプションのほか、ネットワーク アプリケーションの開発とテストのためにオンプレミスの ID 要件を満たす手段も提供します。 Azure AD Domain Services の目的は、オンプレミスの AD DS インフラストラクチャを Azure AD Domain Services によって管理される Azure VM にリフトアンドシフトすることではありません。 AD DS の認証方法を必要とするオンプレミス アプリケーションの開発、テスト、およびクラウドへの移行をサポートするために、管理対象ドメイン内の Azure VM を使用する必要がある、ということです。

## <a name="common-scenarios-and-recommendations"></a>一般的なシナリオと推奨事項

以降では、ID とアクセスに関する一般的なシナリオをいくつか取り上げると共に、それぞれのシナリオで最適と思われる Azure ID オプションを紹介します。

  |ID のシナリオ| 推奨|
  |-----|-----|
  |Azure で少数の仮想マシンをサポートする必要があるものの、会社ではまだオンプレミス Active Directory (AD DS) に多額の投資を行っている。|最小限の数の仮想マシンをサポートし、オンプレミスで大規模な AD DS 投資を行う必要がある場合は、[DIY AD DS](https://msdn.microsoft.com/library/azure/jj156090.aspx) を使用して Azure VM を使用してください。 |
  |自社のビジネスはクラウドに基づいており、オンプレミスの ID ソリューションへの投資を行っていない。| オンプレミスへの投資のないクラウドのみのビジネスには、[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) が最適な選択肢です。|
  |組織でオンプレミスの Windows Server Active Directory に多額の投資を行ってきたものの、ID をクラウドに拡張したいと考えている。| 最も広く使われている Azure ID ソリューションは、[ハイブリッド ID](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview) です。 オンプレミスの AD DS に既に投資している場合は、Azure AD Connect を使用して ID を簡単にクラウドに拡張できます。|
  |アプリの開発とテストのためのオンプレミスの ID 要件を満たすために、ごく簡便な Azure VM の構成と制御を必要としている。|Azure VM 構成のごく簡便な制御を実現するために AD DS を利用する必要がある場合や、従来のディレクトリ対応のオンプレミス アプリケーションを開発したり、クラウドに移行したりする場合は、[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) を選択することをお勧めします。|

## <a name="next-steps"></a>次のステップ

これで、Azure ID の概念と選べるオプションについて理解できました。次に、以下のリソースを利用して、選択したオプションの実装を開始できます。

[Azure ID ソリューションに関する理解度をテストする](https://aka.ms/aad-understand-quiz)

[Azure の概念実証環境で詳しく学ぶ](https://aka.ms/aad-poc)

[Azure AD を運用環境にデプロイする](https://aka.ms/aad-onboard)

