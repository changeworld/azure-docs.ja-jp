---
title: Azure Security のデータを SIEM にエクスポートする - パイプラインの構成 (プレビュー) | Microsoft Docs
description: この記事では、Azure Security Center のログを SIEM に取得する手順について説明します
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: barclayn
ms.openlocfilehash: aede60a729fe9c0594ded485e189c0b467e34271
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298235"
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Azure Security のデータを SIEM にエクスポートする - パイプラインの構成 (プレビュー)

このドキュメントでは、Azure Security Center のセキュリティ データを SIEM にエクスポートする手順について詳しく説明します。

Azure Security Center によって生成されて処理されたイベントは、Azure Monitor で利用可能なログの種類の 1 つである Azure [アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)に発行されます。 Azure Monitor では、任意の監視データを SIEM ツールにルーティングするための統合パイプラインが提供されています。 これは Event Hub にデータをストリーミングすることによって行われ、そこからはパートナー ツールに取得できます。

このパイプでは、Azure 環境から監視データにアクセスするために [Azure Monitoring の 1 つのパイプライン](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)が使われます。 これにより、データを使うように SIEM と監視ツールを簡単に設定できます。

以降のセクションでは、イベント ハブにストリーミングされるようにデータを構成する方法について説明します。 以下の手順では、Azure Security Center が Azure サブスクリプションで既に構成されているものとします。

概要

![概要](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>SIEM に公開される Azure セキュリティ データ

このプレビュー バージョンでは、[セキュリティの警告](../security-center/security-center-managing-and-responding-alerts.md)を公開します。 今後のリリースでは、セキュリティに関する推奨事項でデータ セットを増やします。

## <a name="how-to-setup-the-pipeline"></a>パイプラインのセットアップ方法 

### <a name="create-an-event-hub"></a>Event Hub を作成する 

始める前に、[Event Hubs 名前空間を作成する](../event-hubs/event-hubs-create.md)必要があります。 この名前空間と Event Hub が、すべての監視データの送信先です。

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Event Hubs への Azure アクティビティ ログのストリーミング

「[アクティビティ ログの Event Hubs へのストリーム](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)」をご覧ください

### <a name="install-a-partner-siem-connector"></a>パートナー SIEM コネクタをインストールする 

Azure Monitor で監視データを Event Hub にルーティングすると、パートナー SIEM や監視ツールに簡単に統合することができます。

一サポートされる SIEM の一覧は、[こちらのリンク](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)をご覧ください

## <a name="example-for-querying-data"></a>データのクエリの例 

警告データの取得に使うことができる Splunk クエリを次に示します。

| **クエリの説明**                                | **クエリ**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| すべての警告                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| 名前を指定した操作の数の集計             | index=main sourcetype="amal:security" \| table operationName \| stats count by operationName                                |
| 警告情報の取得: 日時、名前、状態、ID、サブスクリプション | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>次の手順

- [サポート対象の SIEM](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [アクティビティ ログの Event Hubs へのストリーム](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [セキュリティの警告](../security-center/security-center-managing-and-responding-alerts.md)