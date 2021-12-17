---
title: Azure Monitor を使用して、データ ファクトリを監視する
description: Azure Data Factory からの情報を使用して診断ログを有効にし、Azure Monitor を使用して Data Factory パイプラインを監視する方法を説明します。
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.custom: contperf-fy22q1
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 91f377e1e092348a9d3a4ea6be3794c6f212d6b8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853949"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Azure Monitor を使用してデータ ファクトリの監視とアラート送信を行う

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

クラウド アプリケーションは複雑であり、動的なパーツが多数あります。 モニターでは、アプリケーションを正常な状態で確実に稼働させ続けるためのデータを提供します。 モニターは、潜在的な問題を回避し、過去の問題をトラブルシューティングするのにも役立ちます。 監視データを使用して、アプリケーションに関する詳細な分析情報を取得できます。 この知識は、アプリケーションのパフォーマンスと保守容易性の向上に役立ちます。 また、手動操作を必要とするアクションの自動化にも役立ちます。

Azure Monitor では、ほとんどの Azure サービスに対して、基礎レベルのインフラストラクチャのメトリックとログを提供します。 Azure 診断ログはリソースによって出力され、そのリソースの操作に関する豊富な頻出データを提供します。 Azure Data Factory (ADF) では、Azure Monitor に診断ログを書き込むことができます。 この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

詳細については、「[Azure Monitor の概要](../azure-monitor/overview.md)」を参照してください。

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Azure Data Factory メトリックとパイプライン実行データの保持

Data Factory では、パイプライン実行データを 45 日間だけ格納します。 データをより長期間保持する場合は、Azure Monitor を使用してください。 Monitor を使用すると、診断ログを複数の異なるターゲットにルーティングして分析できます。

* **[ストレージ アカウント]** : 監査や手動での検査のために、診断ログをストレージ アカウントに保存します。 診断設定を使用して、リテンション期間 (日数) を指定できます。
* **イベント ハブ**: ログを Azure Event Hubs にストリーミングします。 ログは、パートナー サービス/カスタム分析ソリューション (Power BI など) への入力となります。
* **Log Analytics**:ログを Log Analytics で分析します。 Data Factory を Azure Monitor と統合することは、次のシナリオで役立ちます。
  * Data Factory から Monitor に発行された充実したメトリックのセットに対して、複雑なクエリを記述する必要がある場合。 Monitor を使用して、これらのクエリにカスタム アラートを作成できます。
  * データ ファクトリ全体を監視する必要があります。 複数のデータ ファクトリから 1 つの Monitor ワークスペースにデータをルーティングできます。

ログを出力するリソースのサブスクリプションにないストレージ アカウントまたはイベント ハブ名前空間を使用することもできます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な Azure ロールベースのアクセス制御 (Azure RBAC) アクセス権限を持っている必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory メトリックとアラート](monitor-metrics-alerts.md)
- [プログラムでのパイプラインの監視と管理](monitor-programmatically.md)
