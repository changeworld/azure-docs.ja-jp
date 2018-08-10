---
title: Azure Security Center でのセキュリティ ソリューションの統合 | Microsoft Docs
description: Azure Security Center とパートナーの統合によって Azure リソースの全体的なセキュリティを強化する方法について説明します。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/07/2018
ms.author: terrylan
ms.openlocfilehash: b0e674eb161af41a848f0456a033d615293a9947
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622791"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center でのセキュリティ ソリューションの統合
このドキュメントは、既に Azure Security Center に接続されているセキュリティ ソリューションを管理したり、新しいセキュリティ ソリューションを追加したりする際に役立ちます。

## <a name="integrated-azure-security-solutions"></a>統合された Azure セキュリティ ソリューション
Security Center を使用すると、Azure で統合されたセキュリティ ソリューションを簡単に有効にすることができます。 利点は次のとおりです。

- 
  **簡略化されたデプロイ**: Security Center により、統合されたパートナー ソリューションのプロビジョニングが簡略化されます。 マルウェア対策や脆弱性評価のようなソリューションでは、Security Center は、仮想マシンで必要なエージェントをプロビジョニングできます。また、ファイアウォール アプライアンスでは、必要とされるネットワーク構成の多くに対処できます。
- **統合された検出機能**: パートナー ソリューションのセキュリティ イベントは、Security Center の警告とインシデントの一環として自動的に収集、集計、表示されます。 また、これらのイベントは、他のソースからの検出とも組み合わされ、高度な脅威検出機能を提供します。
- **統合された正常性の監視と管理**: ユーザーは、統合された正常性イベントを使用して、すべてのパートナー ソリューションをひとめで監視できます。 基本的な管理は、パートナー ソリューションを使用して高度な設定に簡単にアクセスできれば、使用できます。

現時点では、統合されたセキュリティ ソリューションには以下が含まれます。

- エンドポイント保護 ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)、[Symantec](https://www.symantec.com/products)、[McAfee](https://www.mcafee.com/us/products.aspx)、[Windows Defender](https://www.microsoft.com/windows/comprehensive-security)、および [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection))
- Web アプリケーション ファイアウォール ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/products.html)、[Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- 次世代ファイアウォール ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2)、[Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)、および [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- 脆弱性評価 ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) および [Rapid7](https://www.rapid7.com/products/insightvm/))

エンドポイント保護の統合エクスペリエンスはソリューションによって異なります。 次の表に、各ソリューションのエクスペリエンスの詳細を示します。

| エンドポイント保護               | プラットフォーム                             | Security Center のインストール | Security Center の検出 |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (Microsoft Antimalware)                  | Windows Server 2016                   | いいえ、OS に組み込まれている           | [はい]                       |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2 | 拡張機能を介して                | [はい]                       |
| Trend Micro – すべてのバージョン         | Windows Server ファミリ                 | いいえ                            | [はい]                       |
| Symantec v12.1.1100 以降              | Windows Server ファミリ                 | いいえ                            | [はい]                       |
| McAfee v10 以降                       | Windows Server ファミリ                 | いいえ                            | [はい]                       |
| Kaspersky                         | Windows Server ファミリ                 | いいえ                            | いいえ                         |
| Sophos                            | Windows Server ファミリ                 | いいえ                            | いいえ                         |



## <a name="how-security-solutions-are-integrated"></a>セキュリティ ソリューションを統合するしくみ
Security Center からデプロイされている Azure セキュリティ ソリューションは自動的に接続されます。 また、次のようなその他のセキュリティ データ ソースも接続できます。

- Azure AD Identity Protection
- オンプレミスまたは他のクラウドで実行されているコンピューター
- 共通イベント形式 (CEF) をサポートするセキュリティ ソリューション
- Microsoft Advanced Threat Analytics

![パートナー ソリューションの統合](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>統合された Azure セキュリティ ソリューションとその他のデータ ソースの管理

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。

2. **[Microsoft Azure] メニュー**の **[セキュリティ センター]** を選択します。 **[セキュリティ センター - 概要]** が開きます。

  ![Security Center の概要](./media/security-center-partner-integration/overview.png)

3. **[概要]** で、**[セキュリティ ソリューション]** を選択します。

**[セキュリティ ソリューション]** では、統合された Azure セキュリティ ソリューションの正常性に関する情報を表示し、基本的な管理タスクを実行することができます。 また、共通イベント形式 (CEF) の Azure Active Directory Identity Protection のアラートやファイアウォール ログなど、他の種類のセキュリティ データ ソースを接続することもできます。

### <a name="connected-solutions"></a>接続済みソリューション

**[接続済みのソリューション]** セクションには、現在 Security Center に接続されているセキュリティ ソリューションと、各ソリューションの正常性状態に関する情報が表示されます。  

![接続済みソリューション](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

詳細については、[接続済みのパートナー ソリューションの管理](security-center-partner-solutions.md)に関するページを参照してください。

### <a name="discovered-solutions"></a>検出されたソリューション

Security Center は、Azure で実行されていても Security Center に接続されていないセキュリティ ソリューションを自動的に検出し、**[検出されたソリューション]** セクションにソリューションを表示します。 これには、[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) などの Azure ソリューションだけでなく、パートナー ソリューションも含まれます。

> [!NOTE]
> 検出されたソリューション機能のサブスクリプション レベルで、Security Center の Standard レベルが必要です。 セキュリティの価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
>
>

ソリューションの下の **[接続]** を選択して、Security Center と統合し、セキュリティのアラートが通知されるようにします。

![検出されたソリューション](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center は、共通イベント形式 (CEF) のログを転送することができる、サブスクリプションにデプロイされたソリューションも検出します。 Security Center に、CEF ログを使用する[セキュリティ ソリューションを接続](quick-security-solutions.md)する方法を参照してください。

### <a name="add-data-sources"></a>データ ソースの追加

**[データ ソースの追加]** セクションには、接続できるその他の使用可能なデータ ソースが表示されます。 このようなソースのいずれかからデータを追加する手順については、**[追加]** をクリックしてください。

![データ ソース](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>次の手順

この記事では、Security Center でパートナー ソリューションを統合する方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Microsoft Advanced Threat Analytics を Azure Security Center に接続する](security-center-ata-integration.md)
* [Azure Active Directory Identity Protection を Azure Security Center に接続する](security-center-aadip-integration.md)
* [Security Center でのセキュリティ正常性の監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)。 パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center の FAQ](security-center-faq.md)。 Security Center の使用に関してよく寄せられる質問とその回答を紹介しています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
