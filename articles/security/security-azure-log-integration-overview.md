---
title: "Azure のリソースから SIEM システムへのログの統合 | Microsoft Docs"
description: "Azure ログ統合と、その主な機能およびしくみについて紹介します。"
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
ms.openlocfilehash: e427e2f7dafb6db9bc5c15d841fbbf83a02fc0e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Microsoft Azure ログ統合の概要

Azure ログ統合を使用すると、[Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) へのコネクタがまだ SIEM ベンダーから利用可能でない場合に、Azure リソースの未加工のログをオンプレミスのセキュリティ情報イベント管理 (SIEM) システムと統合できます。

Azure ログの統合に優先される方法は、SIEM ベンダーの Azure Monitor コネクタを使用し、こちらの[手順](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)に従うことです。 ただし、SIEM ベンダーが Azure Monitor にコネクタを提供していない場合は、このようなコネクタが使用可能になるまで、Azure Log Integration を一時的なソリューションとして使用できます (SIEM が Azure Log Integration でサポートされている場合)。

>[!IMPORTANT]
>主な関心が仮想マシン ログの収集である場合、ほとんどの SIEM ベンダーがソリューションにこれを含めています。 SIEM ベンダーのコネクタの使用が常に推奨される代替手段です。

Azure ログ統合では、Windows イベント ビューアー ログの Windows イベントと、Azure リソースの [Azure アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)、[Azure Security Center アラート](../security-center/security-center-intro.md)、および [Azure 診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)を収集します。 この統合は、SIEM ソリューションですべての資産についてのオンプレミスまたはクラウドの統合ダッシュボードを提供する助けとなります。これによりセキュリティ イベントの集計、関連付け、分析を実行し、アラートを生成できます。

>[!NOTE]
現時点でサポートされているクラウドは、Azure 商用クラウドおよび Azure Government クラウドのみです。 その他のクラウドはサポートされていません。

![Azure ログ統合][1]

## <a name="what-logs-can-i-integrate"></a>統合できるログ

Azure サービスでは、すべてのサービスの広範なログ記録を作成します。 これらのログは、次の 3 種類のログを表しています。

* **コントロール/管理ログ**は、[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の CREATE、UPDATE、および DELETE 操作の可視性を提供します。 Azure アクティビティ ログは、このタイプのログです。
* **データ プレーン ログ**は、Azure リソース使用の一環として発生するイベントの可視性を提供します。 このタイプのログの例は、Windows 仮想マシンにおける Windows イベント ビューアーの**システム**、**セキュリティ**、および**アプリケーション**の各チャンネルです。 もう 1 つの例は、Azure Monitor によって構成される診断ログです。
* **処理済みイベント**は、ユーザーに代わって処理された分析済みのイベントとアラートを提供します。 このタイプのイベントの例は、Azure Security Center アラートです。Azure Security Center は、ユーザーのサブスクリプションを処理および分析して、ユーザーの現在のセキュリティ体制に関するアラートを提供します。

Azure ログ統合は、ArcSight、QRadar、および Splunk をサポートしています。 いずれの場合でも、ご利用の SIEM ベンダーに問い合わせてネイティブ コネクタがあるかどうかを評価してください。 ネイティブ コネクタを使用できる場合、Azure Log Integration を使用する必要はありません。

他のオプションがない場合は、Azure Log Integration を検討できます。 次の表に、推奨事項を示します。

|**SIEM** | **ログ インテグレーターを既に使用しているお客様** | **SIEM の統合オプションを調査しているお客様**|
|---------|--------------------------|-------------------------------------------|
|**SPLUNK** | [Splunk 向けの Azure Monitor アドオン](https://splunkbase.splunk.com/app/3534/)への移行を開始します | [SPLUNK コネクタ](https://splunkbase.splunk.com/app/3534/)を使用します |
|**IBM QRADAR** | http://aka.ms/azmoneventhub の最後に記載されている QRadar コネクタに移行するか、その使用を開始します | http://aka.ms/azmoneventhub の最後に記載されている QRadar コネクタを使用します  |
|**ARCSIGHT** | コネクタが使用可能になるまでログ インテグレーターを使い続け、その後コネクタ ベースのソリューションに移行します。  | 別の方法として、Azure Log Analytics を検討してください。 コネクタが利用可能になったときに移行プロセスを実行する意思がない場合は、Azure Log Integration へのオンボーディングを行わないください。 |

>[!NOTE]
>Azure ログの統合は無料のソリューションですが、ログ ファイル情報の保存によって発生する Azure Storage のコストがあります。

サポートが必要な場合は[サポート要求](../azure-supportability/how-to-create-azure-support-request.md)をオープンできます。 これを行うには、サポートを依頼しようとしているサービスとして **[ログ統合]** を選択します。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Azure ログ統合について紹介しました。 Azure ログ統合と、サポートされているログのタイプの詳細については、次をご覧ください。

* [Azure ログ統合の使用](security-azure-log-integration-get-started.md) – このチュートリアルでは、Azure ログ統合のインストール、Azure WAD ストレージのログ、Azure アクティビティ ログ、Azure Security Center の警告、および Azure Active Directory の監査ログの統合について説明します。
* [Azure ログ統合のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md) – この FAQ は、Azure ログ統合について寄せられる質問とその回答です。
* [外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
