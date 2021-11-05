---
title: アラートを Microsoft Defender for Cloud からセキュリティ情報イベント管理 (SIEM) システムとその他の監視ソリューションにストリーミングする
description: セキュリティ アラートを Microsoft Sentinel、サードパーティ製の SIEM、SOAR、または ITSM ソリューションにストリーミングする方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 34fcb3b4a8771f9c2b635a0f8e2c5a43ddf896a3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428617"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud では、セキュリティ アラートを最も一般的なセキュリティ情報イベント管理 (SIEM)、セキュリティ オーケストレーション自動応答 (SOAR)、および IT サービス管理 (ITSM) ソリューションにストリーミングできます。

現在使用されている最も一般的なソリューション (以下が含まれます) のすべてでアラート データを表示できるようにする Azure ネイティブ ツールがあります。

- **Microsoft Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **IBM の QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-microsoft-sentinel"></a>Microsoft Sentinel にアラートをストリーミングする 

Defender for Cloud は、Azure のクラウドネイティブ SIEM および SOAR ソリューションである Microsoft Sentinel とネイティブに統合されます。 

[Microsoft Sentinel の詳細情報](../sentinel/overview.md)。

### <a name="microsoft-sentinels-connectors-for-defender-for-cloud"></a>Microsoft Sentinel の Defender for Cloud 用コネクタ

Microsoft Sentinel には、サブスクリプションレベルとテナント レベルで Microsoft Defender for Cloud 用の組み込みコネクタが含まれています。

- [サブスクリプション レベルで Azure Sentinel にアラートをストリーミングする](../sentinel/connect-azure-security-center.md)
- [テナント内のすべてのサブスクリプションを Azure Sentinel に接続する](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

Defender for Cloud を Microsoft Sentinel に接続すると、Microsoft Sentinel に取り込まれた Defender for Cloud アラートの状態が 2 つのサービス間で同期されます。 たとえば、 Defender for Cloud でアラートが閉じられた場合、そのアラートは Microsoft Sentinel でも閉じられたものとして表示されます。 Defender for Cloud でアラートの状態を変更しても、同期された  Microsoft Sentinel アラートを含む  Microsoft Sentinel **インシデント** の状態には影響せず、同期されたアラートそのものの状態のみに影響します。

プレビュー機能である **双方向アラート同期** を有効にすると、元の  Defender for Cloud アラートの状態が、それらの Defender for Cloud アラートのコピーを含む Microsoft Sentinel インシデントと自動的に同期されます。 そのため、たとえば、Azure Defender アラートを含む Azure Sentinel インシデントが閉じられると、対応する元のアラートが Azure Defender によって自動的に閉じられます。

詳細については[「Microsoft Defender for Cloud から Defender for Cloud アラートに接続する」を参照してください](../sentinel/connect-azure-security-center.md)。

> [!NOTE]
> 双方向アラート同期機能は Azure Government クラウドでは利用できません。 

### <a name="configure-ingestion-of-all-audit-logs-into-microsoft-sentinel"></a>すべての監査ログの Microsoft Sentinel への取り込みを構成する 

Microsoft Sentinel で Defender for Cloud アラートを調査するもう 1 つの方法は、監査ログを Microsoft Sentinel にストリーム配信する方法です。
    - [Windows セキュリティ イベントの接続](../sentinel/connect-windows-security-events.md)
    - [Syslog を使用して Linux ベースのソースからデータを収集する](../sentinel/connect-syslog.md)
    - [Azure アクティビティ ログからデータを接続する](../sentinel/data-connectors-reference.md#azure-activity)

> [!TIP]
> Microsoft Sentinel の課金は、分析のために Microsoft Sentinel に取り込まれ、Azure Monitor Log Analytics ワークスペースに格納されたデータの量に基づいて行われます。 Microsoft Sentinel には、柔軟で予測可能な価格モデルが用意されています。 [Microsoft Sentinel の価格ページを確認してください](https://azure.microsoft.com/pricing/details/azure-sentinel/)。


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Microsoft Graph Security API を使用してアラートをストリーミングする

Defender for Cloud は、Microsoft Graph Security API とすぐに統合できます。 構成は不要であり、追加のコストは発生しません。 

この API を使用して、**テナント全体** (およびその他の多くの Microsoft セキュリティ製品のデータ) から、サードパーティ製の SIEM と その他の一般的なプラットフォームにアラートをストリーミングできます。

- **Splunk Enterprise と Splunk Cloud** - [Splunk 用の Microsoft Graph Security API アドオンを使用します](https://splunkbase.splunk.com/app/4564/) 
- **Power BI** - [Power BI Desktop で Microsoft Graph Security API に接続します](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow** - [ServiceNow Store から Microsoft Graph Security API アプリケーションをインストールして構成する手順に従います](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar** - [Microsoft Graph API 経由の Microsoft Defender 用の IBM のデバイス サポート モジュール](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**、**Anomali**、**Lookout**、**InSpark** など - [Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Microsoft Graph Security API の詳細](https://www.microsoft.com/security/business/graph-security-api)。


## <a name="stream-alerts-with-azure-monitor"></a>Azure Monitor を使用してアラートをストリーミングする 

**ArcSight**、**Splunk**、**SumoLogic**、Syslog servers、**LogRhythm**、**Logz.io Cloud Observability Platform**、およびその他の監視ソリューションにアラートをストリーミングするには、 Azure Event Hubs 経由で SDefender for Cloud を Azure Monitor に接続します。

1. サブスクリプション レベルでDefender for Cloud アラートを専用の Azure イベント ハブにストリーミングするために、[連続エクスポート](continuous-export.md)を有効にします。 
    > [!TIP]
    > Azure Policy を使用して管理グループ レベルでこれを行うには、「[連続エクスポートの自動化の構成を大規模に作成する](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)」を参照してください。

1. [Azure Monitor の組み込みコネクタを使用して、Azure イベント ハブを任意のソリューションに接続します](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)。

1. 必要に応じて、未加工のログを Azure イベント ハブにストリーミングし、任意のソリューションに接続します。 「[使用可能なデータの監視](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available)」で詳細を確認してください。

> [!TIP]
> エクスポートされたデータ型のイベント スキーマを表示するには、[イベント ハブのイベント スキーマ](https://aka.ms/ASCAutomationSchemas)にアクセスします。


## <a name="next-steps"></a>次のステップ

このページでは、選択した SIEM、SOAR、または ITSM ツールで Microsoft Defender for Cloud アラート データを使用できるようにする方法について説明しました。 関連資料については、以下を参照してください。

- [Microsoft Sentinel とは](../sentinel/overview.md)
- [Microsoft Defender for Cloud でのアラートの検証](alert-validation.md) - アラートが正しく構成されていることを確認します
- [クラウドデータに対して Defender を継続的にエクスポートする](continuous-export.md)