---
title: Azure リソース ログの概要 | Microsoft Docs
description: Azure リソース ログでサポートされているサービスとイベント スキーマについて説明します。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: b953f9b5e5fd8c853746caad3047986786bd1317
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989618"
---
# <a name="azure-resource-logs-overview"></a>Azure リソース ログの概要
Azure リソース ログは、Azure リソースによって出力された [プラットフォーム ログ](platform-logs-overview.md) であり、内部操作を説明するものです。 すべてのリソース ログは共通の上位スキーマを共有し、各サービスで独自のイベントの一意のプロパティを出力する柔軟性を持ちます。

> [!NOTE]
> リソース ログは、以前は診断ログと呼ばれていました。

## <a name="collecting-resource-logs"></a>リソース ログの収集
リソース ログは、サポートされている Azure リソースによって自動的に生成されますが、[診断設定](diagnostic-settings.md)を使用して構成しない限り収集されません。 Azure リソースごとに診断設定を作成し、ログを次の送信先に転送します。

| Destination | シナリオ |
|:---|:---|:---|
| [Log Analytics ワークスペース](resource-logs-collect-workspace.md) | 他の監視データと共にログを分析し、ログ クエリやログ アラートなどの Azure Monitor 機能を活用します。 |
| [Azure Storage](resource-logs-collect-storage.md) | 監査やバックアップのためにログをアーカイブします。 |
| [イベント ハブ](resource-logs-stream-event-hubs.md) | サード パーティのログ システムやテレメトリ システムにログをストリーミングします。  |

## <a name="compute-resources"></a>コンピューティング リソース
リソース ログは、Azure コンピューティング リソースのゲスト OS レベルのログとは異なります。 コンピューティング リソースには、イベント ログ、syslog、パフォーマンス カウンターなどのデータを含むログおよびメトリックをゲスト OS から収集するエージェントが必要です。 [診断の拡張機能](agents-overview.md#azure-diagnostic-extension)を使用して Azure 仮想マシンおよび [Log Analytics エージェント](agents-overview.md#log-analytics-agent)からログ データをルーティングし、Azure、他のクラウド、およびオンプレミスの仮想マシンから Log Analytics ワークスペースにログとメトリックを収集します。 詳細については、「[Azure Monitor で使用する監視データのソース](data-sources.md)」を参照してください。

## <a name="resource-logs-schema"></a>リソース ログのスキーマ
リソース ログのスキーマとカテゴリの詳細については、[リソース ログのスキーマ](diagnostic-logs-schema.md)に関する記事を参照してください。 

## <a name="next-steps"></a>次の手順

* Azure の監視に使用できる[その他の Azure プラットフォーム ログについて確認します](platform-logs-overview.md)。
