---
title: "Azure Security Center でのセキュリティ ソリューションの統合 | Microsoft Docs"
description: "Azure Security Center とパートナーの統合によって Azure リソースの全体的なセキュリティを強化する方法について説明します。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2017
ms.author: yurid
ms.openlocfilehash: 847a872661bea31b774814188c7707260a16e620
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2017
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center でのセキュリティ ソリューションの統合
このドキュメントは、既に Azure Security Center に接続されているセキュリティ ソリューションを管理したり、新しいセキュリティ ソリューションを追加したりする際に役立ちます。

## <a name="integrated-azure-security-solutions"></a>統合された Azure セキュリティ ソリューション
Security Center を使用すると、Azure で統合されたセキュリティ ソリューションを簡単に有効にすることができます。 利点は次のとおりです。

- **簡略化されたデプロイ**: Security Center により、統合されたパートナー ソリューションのプロビジョニングが簡略化されます。 マルウェア対策や脆弱性評価のようなソリューションでは、Security Center は、仮想マシンで必要なエージェントをプロビジョニングできます。また、ファイアウォール アプライアンスでは、必要とされるネットワーク構成の多くに対処できます。
- **統合された検出機能**: パートナー ソリューションのセキュリティ イベントは、Security Center の警告とインシデントの一環として自動的に収集、集計、表示されます。 また、これらのイベントは、他のソースからの検出とも組み合わされ、高度な脅威検出機能を提供します。
- **統合された正常性の監視と管理**: ユーザーは、統合された正常性イベントを使用して、すべてのパートナー ソリューションをひとめで監視できます。 基本的な管理は、パートナー ソリューションを使用して高度な設定に簡単にアクセスできれば、使用できます。

現時点では、統合されたセキュリティ ソリューションには以下が含まれます。

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)、Symantec、[Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](https://docs.microsoft.com/azure/security/azure-security-antimalware)、Windows Defender、System Center Endpoint Protection (SCEP))
- Web アプリケーション ファイアウォール ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets)、[Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- 次世代ファイアウォール ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2)、[Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html))
- 脆弱性評価 ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

## <a name="how-security-solutions-are-integrated"></a>セキュリティ ソリューションを統合するしくみ
Security Center からデプロイされている Azure セキュリティ ソリューションは自動的に接続されます。 また、次のようなその他のセキュリティ データ ソースも接続できます。

- Azure AD Identity Protection
- オンプレミスまたは他のクラウドで実行されているコンピューター
- 共通イベント形式 (CEF) をサポートするセキュリティ ソリューション
- Microsoft Advanced Threat Analytics

![パートナー ソリューションの統合](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>統合された Azure セキュリティ ソリューションとその他のデータ ソースの管理

デプロイ後に、統合された Azure セキュリティ ソリューションの正常性に関する情報を表示し、基本的な管理タスクを実行することができます。 また、共通イベント形式 (CEF) の Azure Active Directory Identity Protection のアラートやファイアウォール ログなど、他の種類のセキュリティ データ ソースを接続することもできます。 Security Center ダッシュボードで [セキュリティ ソリューション] を選択します。

### <a name="connected-solutions"></a>接続済みソリューション

**[接続済みのソリューション]** セクションには、現在 Security Center に接続されているセキュリティ ソリューションと、各ソリューションの正常性状態に関する情報が表示されます。  

![接続済みソリューション](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

### <a name="discovered-solutions"></a>検出されたソリューション

**[検出されたソリューション]** セクションには、Azure 経由で追加されたすべてのソリューションが表示されます。 また、Security Center が提案する、それに接続する必要があるソリューションがすべて表示されます。

![検出されたソリューション](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center は、Azure で実行されている他のセキュリティ ソリューションを自動的に検出します。 これには、[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) などの Azure ソリューションに加え、Azure で実行されているパートナー ソリューションも含まれます。 これらのソリューションを Security Center と統合するには、**[接続]** を選択します。

### <a name="add-data-sources"></a>データ ソースの追加

**[データ ソースの追加]** セクションには、接続できるその他の使用可能なデータ ソースが表示されます。 このようなソースのいずれかからデータを追加する手順については、**[追加]** をクリックしてください。

![データ ソース](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>次のステップ

この記事では、Security Center でパートナー ソリューションを統合する方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
* [Microsoft Advanced Threat Analytics を Azure Security Center に接続する](security-center-ata-integration.md)
* [Azure Active Directory Identity Protection を Azure Security Center に接続する](security-center-aadip-integration.md)
* [Security Center でのセキュリティ正常性の監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)。 パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center の FAQ](security-center-faq.md)。 Security Center の使用に関してよく寄せられる質問とその回答を紹介しています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
