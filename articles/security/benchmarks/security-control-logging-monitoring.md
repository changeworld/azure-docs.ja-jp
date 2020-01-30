---
title: Azure セキュリティ コントロール - ログ記録と監視
description: セキュリティ コントロールでのログ記録と監視
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545502"
---
# <a name="security-control-logging-and-monitoring"></a>セキュリティ コントロール: ログ記録と監視

セキュリティのログ記録と監視は、Azure サービスに対する監査ログの有効化、取得、および保存に関連するアクティビティに焦点を当てています。

## <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft は Azure リソースのためにタイム ソースを管理していますが、コンピューティング リソースのために時刻同期設定を管理するオプションが用意されています。

Azure コンピューティング リソースの時刻同期を構成する方法:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.2 | 6.5、6.6 | Customer |

Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。 Azure Sentinel をオンボードする方法:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure Monitor でプラットフォーム ログとメトリックを収集する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure Monitor で Azure 仮想マシンの内部ホスト ログを収集する方法:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Azure Monitor とサードパーティの SIEM 統合の使用を開始する方法:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.3 | 6.2、6.3 | Customer |

監査ログ、セキュリティ ログ、および診断ログにアクセスするため、Azure リソースに対する診断設定を有効にします。 自動的に使用できるアクティビティ ログには、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素が含まれています。

Azure Monitor でプラットフォーム ログとメトリックを収集する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure でのログ記録とログのさまざまな種類について:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.4 | 6.2、6.3 | Customer |

コンピューティング リソースが Microsoft に所有されている場合、その監視は Microsoft が担当します。 コンピューティング リソースがお客様の組織に所有されている場合、その監視はお客様が担当します。 OS は Azure Security Center を使用して監視できます。 Security Center によってオペレーティング システムから収集されるデータには、OS の種類とバージョン、OS ログ (Windows イベント ログ)、実行中のプロセス、マシン名、IP アドレス、ログイン ユーザーなどがあります。 Log Analytics エージェントは、クラッシュ ダンプ ファイルも収集します。

Azure Monitor で Azure 仮想マシンの内部ホスト ログを収集する方法:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Azure Security Center のデータ収集について:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.5 | 6.4 | Customer |

Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

Log Analytics ワークスペースのログ保持パラメーターを設定する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.6 | 6.7 | Customer |

異常な動作がないかログの分析と監視を行って、定期的に結果を確認します。 Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

Azure Sentinel をオンボードする方法:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Log Analytics ワークスペースについて:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor でカスタム クエリを実行する方法:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.7 | 6.8 | Customer |

Log Analytics ワークスペースと共に Azure Security Center を使用し、セキュリティ ログやイベントで検出される異常なアクティビティに対する監視とアラートの送信を行います。

または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。

Azure Sentinel をオンボードする方法:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure Security Center でアラートを管理する方法:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Log Analytics のログ データに関するアラートを送信する方法:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.8 | 8.6 | Customer |

Azure Virtual Machines および Cloud Services についてのマルウェア対策イベント収集を有効にします。

Virtual Machines に対して Microsoft Antimalware を構成する方法:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Cloud Services に対して Microsoft Antimalware を構成する方法:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Microsoft Antimalware について:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.9 | 8.7 | Customer |

DNS ログ記録用のサード パーティ ソリューションを実装します。

## <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 2.1 | 8.8 | Customer |

ノードごとに手動でコンソールのログ記録と PowerShell トランスクリプションを構成します。

## <a name="next-steps"></a>次のステップ

次のセキュリティ コントロールを参照してください。[ID およびアクセス制御](security-control-identity-access-control.md)
