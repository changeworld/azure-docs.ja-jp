---
title: "Azure Security Center でのパートナー統合 | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center とパートナーの統合によって Azure リソースの全体的なセキュリティに強化する方法について説明します。"
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
ms.date: 06/23/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: 85460ea37d251b53074de9d832610d253700f2c1
ms.contentlocale: ja-jp
ms.lasthandoff: 06/24/2017


---
# Azure Security Center でのパートナー統合
<a id="partner-integration-in-azure-security-center" class="xliff"></a>
このドキュメントでは、Azure Security Center とパートナーの統合により、パートナーの証明と課金に Azure Marketplace を活用しながら、全体的なセキュリティを強化して Azure での統合されたエクスペリエンスを実現する方法について説明します。

>[!NOTE] 
>2017 年 6 月上旬以降、Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行います。 詳細については、「[Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)」を参照してください。 この記事の情報は、Microsoft Monitoring Agent に移行した後の Security Center の機能を示しています。
>

## Security Center からパートナーのソリューションをデプロイする理由
<a id="why-deploy-partners-solutions-from-security-center" class="xliff"></a>

Security Center でパートナー統合を利用する主な理由は、次の 4 つです。

- **デプロイの容易さ**: Security Center の推奨事項に従うと、パートナー ソリューションのデプロイがさらに簡単になります。 デプロイ プロセスは、既定の構成とネットワーク トポロジを使用して完全に自動化できます。または、ユーザーは半自動のオプションを選択して、構成の柔軟性とカスタマイズ性を高めることができます。
- **統合された検出機能**: パートナー ソリューションのセキュリティ イベントは、Security Center の警告とインシデントの一環として自動的に収集、集計、表示されます。 これらのイベントは、他のソースからの検出とも組み合わされ、高度な脅威検出機能を提供します。
- **統合された正常性の監視と管理**: 統合された正常性イベントでは、ユーザーはすべてのパートナー ソリューションを一覧で監視できます。 基本的な管理は、パートナー ソリューションを使用した高度な構成への簡単なアクセスと共に使用できます。
- **SIEM へのエクスポート**: ユーザーは、Microsoft Azure ログ統合 (プレビュー) を使用して、Security Center とパートナーのすべての警告をオンプレミスの SIEM システムに CEF 形式でエクスポートできるようになりました。


## Security Center と統合されているパートナー
<a id="what-partners-are-integrated-with-security-center" class="xliff"></a>
Security Center は現在、次のソリューションと統合されています。

- エンドポイント保護 ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)、Symantec、[Microsoft Antimalware for Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Web アプリケーション ファイアウォール ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets)、[App Gateway WAF](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- 次世代ファイアウォール ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2)、[Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- 脆弱性評価 ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Security Center では、これらの既存のカテゴリに含まれるパートナーの数を徐々に拡大し、新しいカテゴリを追加する予定です。 

## パートナー ソリューションのデプロイ方法
<a id="how-to-deploy-a-partner-solution" class="xliff"></a>

Azure 環境の構成と定義されたセキュリティ ポリシーに応じて、Security Center がパートナー ソリューションのデプロイを推奨する場合があります。 推奨する際は、パートナー ソリューションの選択とインストールの手順が案内されます。 このときの全体的なデプロイ操作は、ソリューションとパートナーの種類によって異なります。 詳細については、次のリンクを参照してください。

- [Web アプリケーション ファイアウォールの追加](security-center-add-web-application-firewall.md)
- [次世代ファイアウォールの追加](security-center-add-next-generation-firewall.md)
- [Endpoint Protection をインストールします](security-center-install-endpoint-protection.md)
- [脆弱性評価がインストールされていません](security-center-vulnerability-assessment-recommendations.md)

## パートナー ソリューションの管理方法
<a id="how-to-manage-partner-solutions" class="xliff"></a>

パートナー ソリューションのデプロイが完了したら、Security Center のメイン ダッシュボードにあるパートナー ソリューションのタイルから、ソリューションの正常性に関する情報を確認したり、基本的な管理タスクを実行したりできます。 Security Center でのパートナー ソリューションの管理の詳細については、「[Monitoring partner solutions with Azure Security Center (Azure Security Center を使用したパートナー ソリューションの監視)](security-center-partner-solutions.md)」を参照してください。

![パートナー統合](./media/security-center-partner-integration/security-center-partner-integration-fig1-1-newUI.png)

> [!NOTE]
> Symantec Endpoint Protection のサポートは検出に制限されており、正常性アラートは利用できません。
>

## 関連項目
<a id="see-also" class="xliff"></a>
このドキュメントでは、Azure Security Center でのパートナー ソリューションの統合方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
* [Azure セキュリティ センターでのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)
* [Azure Security Center の種類別のセキュリティの警告](security-center-alerts-type.md)
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

