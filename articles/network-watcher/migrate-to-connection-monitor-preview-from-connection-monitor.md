---
title: 接続モニターから接続モニター (プレビュー) に移行する
titleSuffix: Azure Network Watcher
description: 接続モニターから接続モニター (プレビュー) に移行する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: 05b42024529b8d9de3590488ecafbdf83283e007
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441818"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>接続モニターから接続モニター (プレビュー) に移行する

数回のクリックで、既存の接続モニターを改善された新しい接続モニター (プレビュー) にダウンタイムなしで移行できます。 利点の詳細については、「[接続モニター (プレビュー)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)」を参照してください。

## <a name="key-points-to-note"></a>注意する点

この移行は、次の結果を生み出すために役立ちます。

* エージェントとファイアウォールの設定は現状のままです。 変更の必要はありません。 
* 既存の接続モニターは、接続モニター (プレビュー) -> テストグループ -> テスト形式にマップされます。 **[編集]** を選択することで、新しい接続モニターのプロパティを表示して変更したり、テンプレートをダウンロードして接続モニターの変更を行い、それを Azure Resource Manager 経由で送信したりできます。 
* Network Watcher 拡張機能を備えた Azure 仮想マシンでは、ワークスペースとメトリックの両方にデータが送信します。 接続モニターでは、古いメトリック(ProbesFailedPercent と AverageRoundtripMs) の代わりに、新しいメトリック (ChecksFailedPercent (プレビュー) と RoundTripTimeMs (プレビュー)) 経由でデータを使用できるようになります。 
* データの監視:
   * **アラート**:新しいメトリックに自動的に移行されます。
   * **ダッシュボードと統合**:メトリック セットを手動で編集する必要があります。 
    
## <a name="prerequisites"></a>前提条件

カスタム ワークスペースを使用している場合は、サブスクリプションと Log Analytics ワークスペースのリージョンで Network Watcher が有効になっていることを確認してください。 

## <a name="migrate-the-connection-monitors"></a>接続モニターを移行する

1. 古い接続モニターを新しいものに移行するには、 **[接続モニター]** を選択し、 **[接続モニターの移行]** を選択します。

    ![接続モニターを接続モニター (プレビュー) に移行する例を示すスクリーンショット。](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. 移行するサブスクリプションと接続モニターを選択し、 **[Migrate selected]\(選択したものを移行\)** を選択します。 

数回のクリックで、既存の接続モニターが接続モニター (プレビュー) に移行されます。 

これで、接続モニター (プレビュー) のプロパティのカスタマイズ、既定のワークスペースの変更、テンプレートのダウンロード、および移行の状態を確認できます。 

移行が開始されると、次の変更が行われます。 
* Azure Resource Manager のリソースで新しい接続モニターに変更されます。
    * 接続モニターの名前、リージョン、およびサブスクリプションは未変更のままです。 リソース ID は影響を受けません。
    * 接続モニターをカスタマイズしていない限り、既定の Log Analytics ワークスペースは、サブスクリプションと接続モニターのリージョンに作成されます。 このワークスペースは、監視データが格納される場所です。 テスト結果データも、メトリックに格納されます。
    * 各テストは、 *defaultTestGroup* というテスト グループに移行されます。
    * 移行元と移行先のエンドポイントが作成され、新しいテスト グループで使用されます。 既定の名前は *defaultSourceEndpoint* と *defaultDestinationEndpoint* です。
    * 宛先ポートとプローブ間隔は、*defaultTestConfiguration* というテスト構成に移動されます。 ポートの値に基づいて、プロトコルが設定されます。 成功のしきい値などのオプションのプロパティは、空白のままです。
* メトリック アラートは、接続モニター (プレビュー) メトリック アラートに移行されます。 メトリックは異なっているため、変更されます。 詳細については、「[接続モニター (プレビュー) によるネットワーク接続の監視](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor)」を参照してください。
* 移行された接続モニターは、古い接続モニター ソリューションとしては表示されなくなります。 それらは、接続モニター (プレビュー) でのみ使用できるようになります。
* Power BI や Grafana のダッシュボードなどの外部統合やセキュリティ情報イベント管理 (SIEM) システムとの統合は、手動で移行する必要があります。 これは、ユーザーがセットアップを移行するために実行する必要がある唯一の手順です。

## <a name="next-steps"></a>次の手順

接続モニター (プレビュー) の詳細については、以下を参照してください。
* [Network Performance Monitor から接続モニター (プレビュー) に移行する](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* [Azure portal を使用して接続モニター (プレビュー) を作成する](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
