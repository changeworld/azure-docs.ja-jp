---
title: Microsoft 365 Defender 統合によって Azure Sentinel にインポートされない Microsoft Cloud App Security のアラート | Microsoft Docs
description: この記事では、Microsoft 365 Defender によって収集されていないため、Microsoft Cloud App Security から Azure Sentinel に直接取り込む必要があるアラートを示します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 0232cbde5aaddad268aa0e4725ee3587a2461800
ms.sourcegitcommit: 18cd3c1c8cc47258c6a1a04e0e03d6248c52ef24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107992753"
---
# <a name="microsoft-cloud-app-security-alerts-not-imported-into-azure-sentinel-through-microsoft-365-defender-integration"></a>Microsoft 365 Defender 統合によって Azure Sentinel にインポートされない Microsoft Cloud App Security のアラート

他の Microsoft Defender コンポーネント (Defender for Endpoint、Defender for Identity、Defender for Office 365) と同様に、Microsoft 365 Defender によって収集されたアラートが Microsoft Cloud App Security によって生成されます。 次に、Microsoft 365 Defender によって、Microsoft 365 Defender コネクタが有効になっている場合に、[Azure Sentinel によって取り込まれ、同期されている](microsoft-365-defender-sentinel-integration.md#microsoft-365-defender-incidents-and-microsoft-incident-creation-rules)インシデントが生成されます。

他の 3 つのコンポーネントとは異なり、**すべての種類の** Cloud App Security アラートが Microsoft 365 Defender にオンボードされるわけではありません。そのため、Azure Sentinel でのすべての Cloud App Security アラートのインシデントが必要な場合は、Sentinel に直接取り込まれたアラートからインシデントが引き続き生成され、さらに、Microsoft 365 Defender にオンボードされるアラートからは生成されないように (そのため、重複することはありません)、Microsoft インシデントの作成分析ルールを適切に調整する必要があります。

## <a name="cloud-app-security-alerts-not-onboarded-to-microsoft-365-defender"></a>Microsoft 365 Defender にオンボードされない Cloud App Security アラート

次のアラートは Microsoft 365 Defender にはオンボードされません。また、これらのアラートを生成する Microsoft インシデント作成ルールは、引き続きインシデントを生成するように構成する必要があります。

| Cloud App Security アラートの表示名 | Cloud App Security アラート名 |
|-|-|
| **アクセス ポリシー アラート** | `ALERT_CABINET_INLINE_EVENT_MATCH` |
| **検出済みトラフィック ログからのアクティビティ作成が、1 日の制限を超えました** | `ALERT_DISCOVERY_TRAFFIC_LOG_EXCEEDED_LIMIT` |
| **アクティビティ ポリシー アラート** | `ALERT_CABINET_EVENT_MATCH_AUDIT` |
| **異常な抜き取りアラート** | `ALERT_EXFILTRATION_DISCOVERY_ANOMALY_DETECTION` |
| **危険な状態のアカウント** | `ALERT_COMPROMISED_ACCOUNT` |
| **アプリのセキュリティ侵害アラートが検出されました** | `ALERT_MANAGEMENT_DISCOVERY_BREACHED_APP` |
| **非アクティブなアカウント** | `ALERT_ZOMBIE_USER` |
| **調査の優先順位スコアの上昇** | `ALERT_UEBA_INVESTIGATION_PRIORITY_INCREASE` |
| **悪意のある OAuth アプリの同意** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MALICIOUS_OAUTH_APP_CONSENT` |
| **まぎらわしい OAuth アプリ名** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MISLEADING_APP_NAME` |
| **OAuth アプリのまぎらわしい発行元名** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MISLEADING_PUBLISHER_NAME` |
| **新しいアプリが検出されました** | `ALERT_CABINET_DISCOVERY_NEW_SERVICE` |
| **OAuth アプリで、セキュリティで保護されていないリダイレクト URL が使用されています** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_NON_SECURE_REDIRECT_URL` |
| **OAuth アプリ ポリシー アラート** | `ALERT_CABINET_APP_PERMISSION` |
| **不審なアクティビティのアラート** | `ALERT_SUSPICIOUS_ACTIVITY` |
| **不審なクラウド使用のアラート** | `ALERT_DISCOVERY_ANOMALY_DETECTION` |
| **不審な OAuth アプリ名** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_SUSPICIOUS_APP_NAME` |
| **システム警告アプリ コネクタ エラー** | `ALERT_MANAGEMENT_DISCONNECTED_API` |
| **Cloud Discovery 自動ログ アップロード エラーに関するシステム警告** | `ALERT_MANAGEMENT_LOG_COLLECTOR_LOW_RATE` |
| **Cloud Discovery ログ処理エラーに関するシステム警告** | `ALERT_MANAGEMENT_LOG_COLLECTOR_CONSTANTLY_FAILED_PARSING` |
| **システム警告 ICAP コネクタ エラー** | `ALERT_MANAGEMENT_DLP_CONNECTOR_ERROR` |
| **システム警告 SIEM エージェント エラー** | `ALERT_MANAGEMENT_DISCONNECTED_SIEM` |
| **システム警告 SIEM エージェント通知** | `ALERT_MANAGEMENT_NOTIFICATIONS_SIEM` |
| **クラウド リソースのリージョンが通常とは異なる** | `MCAS_ALERT_ANUBIS_DETECTION_UNCOMMON_CLOUD_REGION` |
|

## <a name="next-steps"></a>次の手順

- [Microsoft 365 Defender を Azure Sentinel に接続](connect-microsoft-365-defender.md)します。
- [Azure Sentinel](overview.md)、[Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender)、[Cloud App Security](/cloud-app-security/what-is-cloud-app-security) について詳しく確認します。
