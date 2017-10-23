---
title: "Azure Security Center でのパートナーとソリューションの統合 | Microsoft Docs"
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
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: a6998c997840f1a9f349b85a4274908b611cd315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Azure Security Center でのパートナーとソリューションの統合

この記事では、Azure Security Center とパートナーの統合によって全体的なセキュリティを強化する方法について説明します。 Security Center では、Azure の統合されたエクスペリエンスを実現し、パートナーの認定と課金に Azure Marketplace を活用します。

## <a name="deploy-partner-solutions-from-security-center"></a>Security Center からパートナー ソリューションをデプロイする

Security Center でパートナー統合を使用する主な理由は 4 つあります。

- **デプロイの容易さ**。 Security Center の推奨事項に従うと、パートナー ソリューションのデプロイはさらに簡単になります。 デプロイ プロセスは、既定の設定とネットワーク トポロジを使用して完全に自動化できます。 また、ユーザーは、柔軟性の向上やさらなるカスタマイズのために、半自動のオプションを選択することもできます。
- **統合された検出機能**。 パートナー ソリューションのセキュリティ イベントは、Security Center の警告とインシデントの一環として自動的に収集、集計、表示されます。 また、これらのイベントは、他のソースからの検出とも組み合わされ、高度な脅威検出機能を提供します。
- **統合された正常性の監視と管理**。 ユーザーは、統合された正常性イベントを使用して、すべてのパートナー ソリューションをひとめで監視できます。 基本的な管理は、パートナー ソリューションを使用して高度な設定に簡単にアクセスできれば、使用できます。
- **SIEM へのエクスポート**。 ユーザーは、Azure ログ統合 (プレビュー) を使用して、Security Center とパートナーの警告すべてをオンプレミスのセキュリティ情報およびイベント管理 (SIEM) システムに共通イベント形式 (CEF) でエクスポートできます。


## <a name="partners-that-integrate-with-security-center"></a>Security Center と統合されるパートナー

現在、Azure Marketplace と Security Center で利用できる、パートナーのソリューションのネイティブな統合は、次のとおりです。

- **Endpoint Protection**。 [Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)、Symantec、[Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](https://docs.microsoft.com/azure/security/azure-security-antimalware)。
- **Web アプリケーション ファイアウォール**。 [Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets)、および [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)。 
- **次世代ファイアウォール**。 [Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2)、[Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)。 
- **脆弱性評価**。 [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/)。 

Security Center では、上記のカテゴリに含まれるパートナーの数を徐々に増やし、新しいカテゴリを追加する予定です。 

## <a name="deploy-a-partner-solution"></a>パートナー ソリューションのデプロイ

ご利用の Azure 環境の設定と定義したセキュリティ ポリシーに応じて、Security Center はパートナー ソリューションのデプロイを推奨する場合があります。 Security Center の推奨事項により、パートナー ソリューションの選択とインストールの手順が案内されます。 全体的なデプロイ操作は、使用するソリューションとパートナーの種類によって異なる場合があります。 詳細については、次の記事を参照してください。

- [[Endpoint Protection のインストール]](security-center-install-endpoint-protection.md)
- [Web アプリケーション ファイアウォールの追加](security-center-add-web-application-firewall.md)
- [次世代ファイアウォールの追加](security-center-add-next-generation-firewall.md)
- [脆弱性評価がインストールされていません](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>パートナー ソリューションの管理

デプロイ後、ソリューションの正常性に関する情報を表示したり、基本的な管理タスクを実行したりするには、**[Security Center]** ダッシュボードで **[パートナー ソリューション]** を選択します。

![パートナー ソリューションの統合](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

[セキュリティ ソリューション] を開いたときに表示されるコンテンツは、インフラストラクチャによって異なる場合があります。 前のイメージを例とした場合、このページには 3 つのセクションがあります。

- **[接続済みのソリューション]**。 Security Center に接続されているソリューションが表示されます。
- **[検出されたソリューション]**。 Security Center に接続されていないソリューションが表示されます。 これらのソリューションを接続すると、**[接続済みのソリューション]** に表示されます。 接続されていないソリューションが検出されない場合、このセクションは表示されません。
- **[Add data sources]\(データ ソースの追加\)**。 Security Center に追加できる Azure のデータ ソースと Azure 以外のデータ ソースが表示されます。

### <a name="connected-solutions"></a>接続済みソリューション

**[接続済みソリューション]** セクションには、Security Center に現在接続されているすべてのセキュリティ ソリューションが表示されます。 

![接続済みソリューション](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

表示される情報は、ソリューションによって異なる場合があります。 各タイルには、以下のような情報が表示されます。

- **パートナーの会社のアイコン**。 Security Center に会社のアイコンがない場合は、パートナー名の最初の文字が表示されます。
- **ソリューションの種類**。 ソリューションの種類が表示されます。
- **コンピューター名**。 コンピューター名が表示されます。
- **正常性状態**。 正常性インジケーターが送信されない場合、Security Center では、アプライアンスによって報告が行われているかどうかを示すために、最後に受信したイベントの日時が表示されます。 Security Center が特定のソリューションから正常性インジケーターを受信しない場合、そのソリューションのタイルはこのセクションに表示されません。

> [!NOTE]
> Security Center では、アプライアンスによって報告が行われているかどうかを示すために、最後に受信したイベントの日時が表示されます。 正常性インジケーターを送信していないソリューションが接続済みとして表示されるのは、それらがアラートの場合とイベントが過去 14 日間に送信された場合です。
>  

これらのソリューションの中には、Azure に完全に統合されているものもあれば、オンプレミスにあるものもあります。 Security Center は [CEF](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef) をサポートしているため、CEF をサポートするファイアウォールなど、CEF を使用するソリューションに接続できます。 このソリューションが Security Center に追加されると、ファイアウォールから Security Center に CEF 形式のログが送信されます。このログは、[Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) に渡されます。 ファイアウォールは Azure 以外のリソースであり、イベントを送信しますが、正常性インジケーターは送信しません。 Security Center にある正常性に関する唯一の情報は、このアプライアンスがイベントを最後に送信した時刻です。 Azure 以外のすべてのリソースの場合、Security Center では、タイルの正常性領域に、最後のイベントが受信された日時が表示されます。 この情報は、Azure 以外のリソースがまだ報告を行っていることを示します。

### <a name="discovered-solutions"></a>検出されたソリューション

**[検出されたソリューション]** セクションには、Azure 経由で追加されたすべてのソリューションが表示されます。 また、Security Center が提案する、それに接続する必要があるソリューションがすべて表示されます。

![検出されたソリューション](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center は、組み込みの Azure ソリューション ([Azure Active Directory (Azure AD) Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) など) と統合できます。 Azure AD Identity Protection のライセンスを持っているものの、Security Center に接続していない場合、Azure AD Identity Protection は **[検出されたソリューション]** の一覧に表示されます。 このソリューションを Security Center と統合するには、**[Azure AD Identity Protection]** タイルで **[接続]** を選択します。そうすると、次のページが表示されます。

![Azure AD Identity Protection](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

Azure AD Identity Protection の接続を終了するには、データが保存されているワークスペースを選択します。 Azure AD Identity Protection のすべてのデータは、この手順で選択したワークスペース領域から送信されます。 ワークスペース セレクターを使用してワークスペースを選択すると、そこでデータの送信が開始されます。

Security Center に接続するには、全体管理者またはセキュリティ管理者である必要があります。アクセス許可がない場合は、**[接続]** ボタンが無効になっています。 ボタンが無効になっている理由を説明するメッセージが表示されます。

Azure AD Identity Protection のアラートは、Security Center の検出パイプを通過します。 このため、Security Center と Azure AD Identity Protection の両方からアラートを受け取ることになります。 Security Center は、[セキュリティ インシデント](https://docs.microsoft.com/azure/security-center/security-center-incident)を作成するために、関連がありそうなすべてのアラートをマージします。 セキュリティ インシデントの説明からは、疑わしいアクティビティに関するより詳細な情報を得られます。

### <a name="add-data-sources"></a>データ ソースの追加

Azure コンピューターと非 Azure コンピューターを追加して、Security Center と統合することができます。 Azure 以外のコンピューターの追加とは、オンプレミスのコンピューター、または CEF をサポートするアプライアンスの追加を意味します。 

![データ ソース](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="see-also"></a>関連項目

この記事では、Security Center でパートナー ソリューションを統合する方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
* [Security Center でセキュリティのアラートの管理と対応を行う](security-center-managing-and-responding-alerts.md)
* [Security Center における各種セキュリティ アラート](security-center-alerts-type.md)
* [Security Center でのセキュリティ正常性の監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)。 パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center の FAQ](security-center-faq.md)。 Security Center の使用に関してよく寄せられる質問とその回答を紹介しています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
