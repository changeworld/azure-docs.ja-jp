---
title: Azure リソースのログを SIEM システムと統合する | Microsoft Docs
description: Azure Log Integration の主な機能としくみについて説明します。
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 6d91692a64a4d3def80990a439fe0a0898bf2f09
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-log-integration"></a>Azure Log Integration の概要

Azure Log Integration を使用して、Azure リソースからの未加工のログをオンプレミスのセキュリティ情報イベント管理 (SIEM) システムに統合できます。 Azure Log Integration は、SIEM ベンダーから [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) へのコネクタが利用できない場合のみ使用してください。

Azure のログを統合するために推奨される方法は、SIEM ベンダーの Azure Monitor コネクタを使用することです。 このコネクタを使用するには、[データ イベント ハブに対する監視のストリーミング](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)に関する記事の指示に従ってください。 

ただし、SIEM ベンダーが Azure Monitor へのコネクタを提供していない場合は、このようなコネクタが利用可能になるまで、Azure Log Integration を一時的なソリューションとして使用できます。 Azure Log Integration は、Azure Log Integration が SIEM がサポートしている場合にのみ使用できるオプションです。

> [!IMPORTANT]
> 主な関心が仮想マシンのログの収集である場合、ほとんどの SIEM ベンダーのソリューションにこのオプションが含まれています。 SIEM ベンダーのコネクタを使用することが常に推奨されます。

Azure Log Integration は、Windows イベント ビューアー ログの Windows イベントと、Azure リソースの [Azure アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)、[Azure Security Center のアラート](../security-center/security-center-intro.md)、および [Azure 診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)を収集します。 Integration によって、オンプレミスとクラウドのすべての資産に対する一元化されたダッシュボードを SIEM ソリューションに用意できます。 ダッシュボードを使用して、セキュリティ イベントに対するアラートの受信、集計、関連付け、および分析を実行できます。

> [!NOTE]
> 現時点では、Azure Log Integration は、Azure 商用クラウドと Azure Government クラウドのみをサポートしています。 その他のクラウドはサポートされていません。

![Azure Log Integration のプロセス図][1]

## <a name="what-logs-can-i-integrate"></a>統合できるログ

Azure では、各 Azure サービスの広範なログ記録が作成されます。 ログには、次の 3 つのログの種類があります。

* **コントロール/管理ログ**: [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の CREATE、UPDATE、および DELETE 操作を可視化します。 Azure アクティビティ ログは、この種類のログの例です。
* **データ関連のログ**: Azure リソースの使用時に発生するイベントを可視化します。 このタイプのログの例は、Windows 仮想マシンにおける Windows イベント ビューアーの**システム**、**セキュリティ**、および**アプリケーション**の各チャンネルです。 別の例は、Azure Monitor を使用して構成する Azure 診断のログです。
* **処理済みイベント**: ユーザーに代わって処理された分析済みのイベントとアラートを示します。 この種類のイベントの例は、Azure Security Center のアラートです。 Azure Security Center は、サブスクリプションを処理して分析し、現在のセキュリティの状態に関連するアラートを生成します。

Azure Log Integration は、ArcSight、QRadar、および Splunk をサポートしています。 ご利用の SIEM ベンダーに問い合わせて、ベンダーにネイティブ コネクタがあるかどうかを確認してください。 ネイティブ コネクタを利用できる場合は、Azure Log Integration を使用しないでください。

他に利用できるオプションがない場合は、Azure Log Integration の使用を検討します。 次の表に、推奨事項を示します。

|SIEM | Azure ログ インテグレーターを既に使用しているお客様 | SIEM の統合オプションを調査しているお客様|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | [Splunk 向けの Azure Monitor アドオン](https://splunkbase.splunk.com/app/3534/)への移行を開始します。 | [Splunk コネクタ](https://splunkbase.splunk.com/app/3534/)を使用します。 |
|**QRadar** | 「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)」の最後のセクションに記載されている QRadar コネクタに移行するか、使用を開始します。 | 「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)」の最後のセクションに記載されている QRadar コネクタを使用します。 |
|**ArcSight** | コネクタが利用可能になるまで、引き続き Azure ログ インテグレータを使用します。 その後、コネクタ ベースのソリューションに移行します。  | 別の方法として、Azure Log Analytics の使用を検討してください。 コネクタが利用可能になったときに移行プロセスを実行する意思がない場合は、Azure Log Integration を使用しないでください。 |

> [!NOTE]
> Azure Log Integration は無料のソリューションですが、ログ ファイル情報の保存に関連する Azure ストレージのコストが発生します。

サポートが必要な場合は[サポート リクエスト](../azure-supportability/how-to-create-azure-support-request.md)を作成できます。 サービスとして、**[Log Integration]** を選択します。

## <a name="next-steps"></a>次の手順

この記事では、Azure Log Integration の概要を説明しました。 Azure Log Integration の詳細と、サポートされているログの種類については、次の記事をご覧ください。

* [Azure Log Integration の使用を開始する](security-azure-log-integration-get-started.md)。 このチュートリアルでは、Azure Log Integration のインストールについて説明します。 Windows Azure 診断 (WAD) ストレージ のログ、Azure アクティビティ ログ、Azure Security Center のアラート、および Azure Active Directory の監査ログを統合する方法についても説明します。
* [Azure Log Integration のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md)。 この FAQ は、Azure Log Integration について寄せられる質問とその回答です。
* [外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)方法を確認します。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
