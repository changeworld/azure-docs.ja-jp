---
title: Azure Service Fabric - Log Analytics でのパフォーマンスの監視 | Microsoft Docs
description: Azure Service Fabric クラスターのコンテナーとパフォーマンス カウンターを監視するように OMS エージェントを設定する方法を説明します。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: a3ce72e51477c1eda99461b3910bfeeac207be55
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
---
# <a name="performance-monitoring-with-log-analytics"></a>Log Analytics でのパフォーマンスの監視

この記事では、Log Analytics (OMS) エージェントを仮想マシン スケール セットの拡張機能としてクラスターに追加し、既存の Azure Log Analytics ワークスペースに接続する手順について説明します。 これにより、コンテナー、アプリケーション、およびパフォーマンスの監視に関する診断データを収集できます。 これを、仮想マシン スケール セット リソースの拡張機能として追加することで、クラスターをスケーリングしても Azure Resource Manager によってすべてのノードにインストールされるようになります。

> [!NOTE]
> この記事では、Azure Log Analytics ワークスペースが設定済みであることを前提としています。 設定していない場合は、[Azure Log Analytics の設定](service-fabric-diagnostics-oms-setup.md)に関するページに進んでください。

## <a name="add-the-agent-extension-via-azure-cli"></a>Azure CLI を使用して拡張機能を追加する

OMS エージェントをクラスターに追加する最も良い方法は、Azure CLI で利用できる仮想マシン スケール セットの API を使用することです。 Azure CLI をまだ設定していない場合は、Azure Portal に移動して [Cloud Shell](../cloud-shell/overview.md) インスタンスを開くか、[Azure CLI 2.0 をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。

1. Cloud Shell を要求されたら、お使いのリソースと同じサブスクリプションで作業していることを確認します。 確認には `az account show` を使用し、"name" (名前) の値がクラスターのサブスクリプション名と一致することを確認します。

2. Portal で、Log Analytics ワークスペースがあるリソース グループに移動します。 Log Analytics のリソース (リソースの種類は Log Analytics です) をクリックします。 リソースの概要ページが表示されたら、左側のメニューの [設定] セクションの下の **[詳細設定]** をクリックします。

    ![Log Analytics のプロパティ ページ](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Windows クラスターを構築する場合は **[Windows サーバー]** を、Linux クラスターを作成する場合は **[Linux サーバー]** をクリックします。 このページには、`workspace ID` と `workspace key` (ポータルでは、主キーとして表示) が表示されます。 次の手順では、両方が必要です。

4. Cloud Shell で `vmss extension set` API を使用して、クラスターに OMS エージェントをインストールするコマンドを実行します。

    Windows クラスターの場合
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Linux クラスターの場合

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Windows クラスターに OMS エージェントを追加する例を次に示します。

    ![OMS エージェント CLI コマンド](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. 15 分以内にお使いのノードに正常にエージェントが追加されます。 `az vmss extension list` API を使用するとエージェントが追加されたことを確認できます。

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Resource Manager テンプレートを使用してエージェントを追加する

Azure Log Analytics ワークスペースをデプロイし、ご利用の各ノードにエージェントを追加する Resource Manager テンプレートのサンプルは、[Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) または [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) で使用できます。

このテンプレートをダウンロードして、ニーズに最適なクラスターをデプロイするように変更することができます。

## <a name="view-performance-counters-in-the-log-analytics-portal"></a>Log Analytics ポータルでのパフォーマンス カウンターの表示

OMS エージェントを追加したら、Log Analytics ポータルに進み、どのパフォーマンス カウンターを収集するか選択します。 

1. Azure Portal で Service Fabric Analytics ソリューションを作成したリソース グループに移動します。 **[ServiceFabric\<nameOfOMSWorkspace\>]** を選択し、概要ページに移動します。 上部の OMS ポータルに移動するリンクをクリックします。

2. ポータルに移動すると、Service Fabric のものを 1 つを含む、有効なソリューションごとのグラフ形式のタイルが表示されています。 これをクリックし、Service Fabric Analytics ソリューションを継続します。 

3. すると、稼働チャネルと信頼性の高いサービス イベントのグラフがいくつかのタイルで表示されます。 右の歯車のアイコンをクリックして設定ページに移動します。

    ![OMS 設定](media/service-fabric-diagnostics-oms-agent/oms-solutions-settings.png)

4. この設定ページで、[データ] をクリックし、Windows または Linux パフォーマンス カウンターを選択します。 有効にするために選択できる既定の一覧があり、収集の間隔を設定することもできます。 収集する[追加のパフォーマンス カウンター](service-fabric-diagnostics-event-generation-perf.md)を追加することもできます。 正しい形式については、この[記事](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx)を参照してください。

カウンターを構成し、ソリューション ページに戻ると、**[ノード メトリック]** の下のグラフに、すぐにデータが取り込まれ表示されます。 また、Kusto クエリ言語を使用して、イベントをクラスタリングし、ノード、パフォーマンス カウンター名および値をフィルタリングするために、同様にパフォーマンス カウンター データに対してクエリを実行できます。 

![OMS パフォーマンス カウンターのクエリ](media/service-fabric-diagnostics-oms-agent/oms-perf-counter-query.png)

## <a name="next-steps"></a>次の手順

* 関連する[パフォーマンス カウンター](service-fabric-diagnostics-event-generation-perf.md)を収集する。 特定のパフォーマンス カウンターを収集するように OMS エージェントを構成する場合は、「[データ ソースの構成](../log-analytics/log-analytics-data-sources.md#configuring-data-sources)」を確認してください。
* Log Analytics を構成して、検出と診断に役立つ[自動アラート](../log-analytics/log-analytics-alerts.md)を設定する。
* 代わりに、パフォーマンス カウンターの収集は、[Microsoft Azure 診断の拡張機能で行い、Application Insights に送信](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)することができる。