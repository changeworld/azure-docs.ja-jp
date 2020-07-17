---
title: Azure のセキュリティのログと監査 | Microsoft Docs
description: Azure で利用できるログと、得られるセキュリティ分析情報について説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: bd0f42507e22559690e2682a391c53b9c090aa6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750793"
---
# <a name="azure-security-logging-and-auditing"></a>Azure セキュリティのログと監査

Azure にはさまざまな構成可能なセキュリティ監査およびログのオプションが用意されており、セキュリティ ポリシーとメカニズムのギャップを特定するのに役立ちます。 この記事では、Azure でホストされているサービスからのセキュリティ ログの生成、収集、分析について説明します。

> [!Note]
> この記事で紹介しているいくつかの推奨事項に従った結果、データ、ネットワーク、またはコンピューティング リソースの使用量が増加したり、ライセンスまたはサブスクリプションのコストが増加したりする場合があります。

## <a name="types-of-logs-in-azure"></a>Azure のログのタイプ

クラウド アプリケーションは、動的なパーツを多数使った複雑な構成になっています。 ログ データは、アプリケーションに関する分析情報を提供し、次のことを支援します。

- これまでに発生した問題のトラブルシューティングや潜在的な問題の防止
- アプリケーションのパフォーマンスや保守容易性の向上
- 手動操作を必要とするアクションの自動化

Azure のログは、次の種類に分類されます。
* **コントロール/管理ログ**: Azure Resource Manager の CREATE、UPDATE、DELETE 操作に関する情報を提供します。 詳細については、[Azure アクティビティ ログ](../../azure-monitor/platform/platform-logs-overview.md)に関するページを参照してください。

* **データ プレーン ログ**は、Azure のリソース使用の一環として発生したイベントに関する情報を提供します。 この種類のログの例として、仮想マシンの Windows イベント システム ログ、セキュリティ ログ、アプリケーション ログや、Azure Monitor で構成される[診断ログ](../../azure-monitor/platform/platform-logs-overview.md)があります。

* **処理済みイベント**: ユーザーに代わって処理された分析済みのイベント/アラートに関する情報を提供します。 この種類の例として、[Azure Security Center](../../security-center/security-center-intro.md) がサブスクリプションを処理して分析し、簡潔なセキュリティ アラートを提供する [Azure Security Center のアラート](../../security-center/security-center-managing-and-responding-alerts.md)があります。

次の表には、Azure で使用できる最も重要な種類のログを示します。

| ログのカテゴリ | ログのタイプ | 使用法 | 統合 |
| ------------ | -------- | ------ | ----------- |
|[アクティビティ ログ](../../azure-monitor/platform/platform-logs-overview.md)|Azure Resource Manager リソースのコントロールプレーン イベント|  サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。|    Rest API、[Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md)|
|[Azure リソース ログ](../../azure-monitor/platform/platform-logs-overview.md)|サブスクリプションの Azure Resource Manager リソースの操作に関してよく使用されるデータ|   リソース自体が実行した操作に関する分析情報を提供します。| Azure Monitor|
|[Azure Active Directory レポート](../../active-directory/reports-monitoring/overview-reports.md)|ログとレポート | ユーザーのサインイン アクティビティと、ユーザーおよびグループの管理に関するシステム アクティビティの情報を報告します。|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[仮想マシンとクラウド サービス](../../azure-monitor/learn/quick-collect-azurevm.md)|Windows イベント ログ サービスと Linux Syslog|  仮想マシンのシステム データとログ データを取り込み、そのデータを任意のストレージ アカウントに転送します。|   Azure Monitor の Windows (Microsoft Azure Diagnostics ([WAD](../../monitoring-and-diagnostics/azure-diagnostics.md)) ストレージを使用) と Linux|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|ストレージ ログ (ストレージ アカウントのメトリック データの提供)|トレース要求に関する分析情報を提供し、使用傾向を分析して、ストレージ アカウントの問題を診断します。|   REST API または[クライアント ライブラリ](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[ネットワーク セキュリティ グループ (NSG) フロー ログ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON 形式 (送信および受信のフローをルールごとに表示)|ネットワーク セキュリティ グループを介したイングレスおよびエグレス IP トラフィックに関する情報を表示します。|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Application Insight](../../azure-monitor/app/app-insights-overview.md)|ログ、例外、カスタム診断|  複数のプラットフォームの Web 開発者向けにアプリケーション パフォーマンス管理 (APM) サービスを提供します。| REST API、[Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[データの処理/セキュリティ アラート](../../security-center/security-center-intro.md)|  Azure Security Center のアラート、Azure Monitor のログ アラート|    セキュリティに関する情報と警告を提供します。|  REST API、JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>オンプレミスの SIEM システムとのログ統合
[Security Center アラートの統合](../../security-center/security-center-export-data-to-siem.md)では、Security Center アラート、Azure Diagnostics ログで収集された仮想マシンのセキュリティ イベント、Azure 監査ログを Azure Monitor ログまたは SIEM ソリューションと同期させる方法について説明します。

## <a name="next-steps"></a>次のステップ

- [監査とログ](management-monitoring-overview.md): 可視性の維持と、タイムリーなセキュリティ アラートへの迅速な対応により、データを保護します。

- [Azure 内でのセキュリティ ログと監査ログの収集](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Azure インスタンスで適切なセキュリティ ログと監査ログが収集されるようにこれらの設定を適用します。

- [サイト コレクションの監査設定を構成する](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): サイト コレクションの管理者の場合、個々のユーザーのアクションの履歴と特定の期間内に行われたアクションの履歴を取得します。

- [Office 365 セキュリティ/コンプライアンス センターで監査ログを検索する](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Office 365 セキュリティ/コンプライアンス センターを使用して統合監査ログを検索し、Office 365 の組織のユーザーと管理者のアクティビティを表示できます。
