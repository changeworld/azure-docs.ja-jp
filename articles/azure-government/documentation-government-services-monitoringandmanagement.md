---
title: "Azure Government の監視と管理 | Microsoft Docs"
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/31/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 722c53f819126791073575da04eded2ec5465764
ms.openlocfilehash: 9d4ba4eff1c9768a11c18cbf531b252d767bc9f2


---
# <a name="azure-government-monitoring-management"></a>Azure Government の監視と管理
この記事では、Azure Government 環境での監視と管理に関するサービスのバリエーションと、考慮事項の概要を紹介します。

## <a name="automation"></a>Automation
Automation は、Azure Government で一般提供されています。

### <a name="variations"></a>バリエーション
Automation の機能のうち以下のものは、Azure Government では現在使用できません。

* 認証のためのサービス プリンシパル資格情報の作成

詳細については、[Automation のパブリック ドキュメント](../automation/automation-intro.md)をご覧ください。

## <a name="backup"></a>バックアップ
バックアップは、Azure Government で一般提供されています。

詳細については、「[Azure Government Backup (Azure Government のバックアップ)](documentation-government-services-backup.md)」を参照してください。

### <a name="variations"></a>バリエーション
バックアップ機能のうち以下のものは、Azure Government では現在使用できません。

* Azure Resource Manager のコンテナー
* Azure Portal を使用した管理 (Azure クラシック ポータルはサポートされています)

## <a name="log-analytics"></a>Log Analytics
Log Analytics は、Azure Government で一般提供されています。

### <a name="variations"></a>バリエーション
次の Log Analytics の機能とソリューションは、Azure Government では現在使用できません。

* Microsoft Azure のプレビュー段階のソリューション。これには以下が含まれます。
  * ネットワーク監視ソリューション
  * アプリケーション間の依存関係の監視ソリューション
  * Office 365 ソリューション
  * Windows 10 アップグレード分析ソリューション
  * Application Insights ソリューション
  * Azure Networking Analytics ソリューション
  * Azure Automation Analytics ソリューション
  * Key Vault Analytics ソリューション
* オンプレミスのソフトウェアを更新する必要のあるソリューションと機能。これには以下が含まれます。
  * System Center Operations Manager 2016 との統合 (以前のバージョンの Operations Manager もサポートされます)
  * System Center Configuration Manager のコンピューター グループ
  * Surface Hub ソリューション
* パブリック Azure のプレビュー段階の機能。これには以下が含まれます。
  * Power BI へのデータのエクスポート
* Azure メトリックと Azure 診断
* Operations Management Suite のモバイル アプリケーション

Azure Government では、Log Analytics の URL が異なります。

| Azure Public | Azure Government | メモ |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Log Analytics ポータル |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[データ コレクター API](../log-analytics/log-analytics-data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |エージェント通信 - [ファイアウォール設定の構成](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |エージェント通信 - [ファイアウォール設定の構成](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |エージェント通信 - [ファイアウォール設定の構成](../log-analytics/log-analytics-proxy-firewall.md) |

次の Log Analytics 機能は、Azure Government では動作が異なります。

* Windows エージェントは、Azure Government の [Log Analytics ポータル](https://oms.microsoft.us)からダウンロードする必要があります。
* System Center Operations Manager 管理サーバーを Log Analytics に接続するには、更新された管理パックをダウンロードしてインポートする必要があります。
  1. [更新された管理パック](http://go.microsoft.com/fwlink/?LinkId=828749)をダウンロードして保存します。
  2. ダウンロードしたファイルを解凍します。
  3. 管理パックを Operations Manager にインポートします。 ディスクから管理パックをインポートする方法については、Microsoft TechNet Web サイトの「[Operations Manager 管理パックのインポート方法](http://technet.microsoft.com/library/hh212691.aspx)」をご覧ください。
  4. Operations Manager を Log Analytics に接続するには、「[Operations Manager を Log Analytics に接続する](../log-analytics/log-analytics-om-agents.md)」の手順に従ってください。

### <a name="frequently-asked-questions"></a>よく寄せられる質問
* Microsoft Azure の Log Analytics のデータを Azure Government に移行できますか。
  * いいえ。 Microsoft Azure のデータやワークスペースを Azure Government に移動することはできません。
* Operations Management Suite Log Analytics ポータルから、Microsoft Azure と Azure Government のワークスペースを切り替えることはできますか。
  * いいえ。 Microsoft Azure と Azure Government のポータルは別個であり、情報を共有することはできません。

詳細については、 [Log Analytics のパブリック ドキュメント](../log-analytics/log-analytics-overview.md)をご覧ください。

## <a name="site-recovery"></a>Site Recovery
Site Recovery は、Azure Government で一般提供されています。

詳細については、[Site Recovery のパブリック ドキュメント](../site-recovery/site-recovery-overview.md)をご覧ください。

### <a name="variations"></a>バリエーション
Site Recovery の機能のうち以下のものは、Azure Government では現在使用できません。

* Azure Resource Manager の Site Recovery コンテナー

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を講読してください。



<!--HONumber=Nov16_HO3-->


