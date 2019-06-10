---
title: Azure 診断ログの概要
description: Azure Monitor の Azure 診断ログの概要と、診断ログを使用して Azure リソース内で発生するイベントを把握する方法について説明します。
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244874"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Azure 診断ログの概要

**診断ログ**からは、Azure リソースの操作で頻繁に見られるデータが豊富に提供されます。 Azure Monitor には、次の 2 種類の診断ログが用意されています。

* **テナント ログ**: Azure Active Directory ログなど、Azure サブスクリプションの外部に存在するテナント レベルのサービスのログです。
* **リソースログ**: ネットワーク セキュリティ グループやストレージ アカウントなど、Azure サブスクリプション内でリソースをデプロイする Azure サービスのログです。

    ![リソース診断ログとその他の種類のログ](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

これらのログの内容は、Azure サービスとリソースの種類によって異なります。 たとえば、ネットワーク セキュリティ グループのルール カウンターとキー コンテナーの監査は、2 種類の診断ログです。

これらのログは、[アクティビティ ログ](activity-logs-overview.md)とは異なります。 アクティビティ ログを使用すると、サブスクリプション内で Resource Manager を使用して実行された操作 (仮想マシンの作成やロジック アプリの削除など) を分析できます。 アクティビティ ログはサブスクリプションレベルのログです。 リソースレベルの診断ログを使用すると、そのリソース内で実行された操作 (Key Vault からのシークレットの取得など) を分析できます。

これらのログは、ゲスト OS レベルの診断ログとも異なります。 ゲスト OS 診断ログは、仮想マシンの内部で実行されているエージェントや、他のサポートされるリソースの種類によって収集されるログです。 リソースレベルの診断ログの場合、エージェントは不要です。Azure プラットフォーム自体からリソース固有のデータをキャプチャします。一方、ゲスト OS レベルの診断ログの場合、仮想マシン上で実行されているオペレーティング システムとアプリケーションからデータをキャプチャします。

ここで説明するサービスは、診断ログでサポートされていない場合があります。 [この記事には、診断ログ をサポートするサービスの一覧が記載されたセクションがあります](./../../azure-monitor/platform/diagnostic-logs-schema.md)。

## <a name="what-you-can-do-with-diagnostic-logs"></a>診断ログで実行できること
診断ログでは次のことを実行できます。

![診断ログの論理的配置](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* 監査や手動での検査に使用するために診断ログを[**ストレージ アカウント**](../../azure-monitor/platform/archive-diagnostic-logs.md)に保存する。 **リソース診断設定**を使用して、リテンション期間 (日数) を指定できます。
* サード パーティのサービスや Power BI などのカスタム分析ソリューションで取り込むために、[**Event Hubs** にストリーム配信する](diagnostic-logs-stream-event-hubs.md)。
* これを [Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md) で分析する。ここでは、データが Azure Monitor に即座に書き込まれ、データを最初にストレージに出力する必要はありません。  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

ログを出力するサブスクリプションとは別のサブスクリプションで、ストレージ アカウントまたは Event Hubs 名前空間を使用できます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な RBAC アクセスを持っている必要があります。

> [!NOTE]
>  現在、セキュリティで保護された仮想ネットワークの背後にあるストレージ アカウントにネットワーク フロー ログをアーカイブすることはできません。

## <a name="diagnostic-settings"></a>診断設定

リソース診断ログは、リソース診断設定を使用して構成します。 テナント診断ログは、テナント診断設定を使用して構成します。 サービス コントロールの**診断設定**:

* 診断ログおよびメトリックの送信先 (ストレージ アカウント、Event Hubs、Azure Monitor)。
* 送信されるログ カテゴリ、メトリック データが送信されるかどうか。
* ログの各カテゴリをストレージ アカウントに保持する期間。
    - リテンション期間が 0 日の場合、ログは永続的に保持されます。 または、1 日から 365 日の間の任意の日数を値として指定できます。
    - リテンション ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合 (たとえば、Event Hubs または Log Analytics オプションだけが選択されている場合)、リテンション ポリシーは無効になります。
    - 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが 1 日の場合、その日が始まった時点で、一昨日のログは削除されます。 削除プロセスは午前 0 時 (UTC) に開始されますが、ストレージ アカウントからのログの削除には最大で 24 時間かかる可能性があるので注意してください。

これらの設定は、ポータルの診断設定から、Azure PowerShell および CLI のコマンドで、または [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) を使用して構成できます。

> [!NOTE]
> 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。
>
> *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>診断ログでサポートされているサービス、カテゴリ、スキーマ

サポートされているサービスと、それらのサービスで使用されるログ カテゴリおよびスキーマの完全な一覧については、[この記事](../../azure-monitor/platform/diagnostic-logs-schema.md)をご覧ください。

## <a name="next-steps"></a>次の手順

* [リソース診断ログを **Event Hubs** にストリーミングする](diagnostic-logs-stream-event-hubs.md)
* [Azure Monitor REST API を使用してリソース診断設定を変更する](https://docs.microsoft.com/rest/api/monitor/)
* [Azure Monitor を使用した、Azure ストレージからのログの分析](collect-azure-metrics-logs.md)
