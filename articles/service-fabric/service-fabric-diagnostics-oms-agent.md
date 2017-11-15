---
title: "OMS エージェントによる監視の設定 - Azure Service Fabric | Microsoft Docs"
description: "Azure Service Fabric クラスターのコンテナーとパフォーマンス カウンターを監視するように OMS エージェントを設定する方法を説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: e1a45f9924291382bb1bbdc969e97ee54a7b6132
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2017
---
# <a name="add-the-oms-agent-to-a-cluster"></a>OMS エージェントをクラスターに追加する

この記事では、OMS エージェントを仮想マシン スケール セットの拡張機能としてクラスターに追加し、既存の OMS Log Analytics ワークスペースに接続する手順について説明します。 これにより、コンテナー、アプリケーション、およびパフォーマンスの監視に関する診断データを収集できます。 拡張機能として追加することで、クラスターをスケーリングしても Azure Resource Manager によってすべてのノードにインストールされるようになります。

> [!NOTE]
> この記事では、OMS Log Analytics ワークスペースが設定済みであることを前提としています。 設定していない場合は、[OMS Log Analytics の設定](service-fabric-diagnostics-oms-setup.md)に関するページに進んでください。

## <a name="add-the-agent-extension-via-azure-cli"></a>Azure CLI を使用して拡張機能を追加する

OMS エージェントをクラスターに追加する最も良い方法は、Azure CLI で利用できる仮想マシン スケール セットの API を使用することです。 Azure CLI をまだ設定していない場合は、Azure Portal に移動して [Cloud Shell](../cloud-shell/overview.md) インスタンスを開くか、[Azure CLI 2.0 をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。

1. Cloud Shell を要求されたら、お使いのリソースと同じサブスクリプションで作業していることを確認します。 確認には `az account show` を使用し、"name" (名前) の値がクラスターのサブスクリプション名と一致することを確認します。

2. ポータルで、OMS ワークスペースがあるリソース グループに移動します。 Log Analytics リソース (リソースの種類は Log Analytics になります) をクリックし、右側のナビゲーションで下にスクロールして **[プロパティ]** をクリックします。

    ![OMS プロパティ ページ](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    `workspaceId` を書き留めておきます。 

3. エージェントのデプロイにはお使いの `workspaceKey` も必要になります。 お使いのキーを取得するには、左側のナビゲーションの *[設定]* セクションで **[詳細設定]** をクリックします。 Windows クラスターを構築する場合は **[Windows サーバー]** を、Linux クラスターを作成する場合は **[Linux サーバー]** をクリックします。 表示される *[主キー]* が、エージェントのデプロイに使用する `workspaceKey` として必要になります。

4. Cloud Shell で `vmss extension set` API を使用して、クラスターに OMS エージェントをインストールするコマンドを実行します。

    Windows クラスターの場合
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Linux クラスターの場合

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId'}":'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Windows クラスターに OMS エージェントを追加する例を次に示します。

    ![OMS エージェント CLI コマンド](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
    15 分以内にお使いのノードに正常にエージェントが追加されます。 `az vmss extension list` API を使用するとエージェントが追加されたことを確認できます。

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Resource Manager テンプレートを使用してエージェントを追加する

OMS Log Analytics ワークスペースをデプロイし、お使いの各ノードにエージェントを追加する Resource Manager テンプレートのサンプルは、[Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) または [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) で使用できます。

このテンプレートをダウンロードして、ニーズに最適なクラスターをデプロイするように変更することができます。

## <a name="next-steps"></a>次のステップ

* 関連する[パフォーマンス カウンター](service-fabric-diagnostics-event-generation-perf.md)を収集する。 特定のパフォーマンス カウンターを取得するように OMS エージェントを構成するには、OMS ポータルに移動します (OMS Log Analytics リソースの上部にリンクがあります)。 次に、**[ホーム] > [設定] > [データ] > [Windows パフォーマンス カウンター]** (または **[Linux パフォーマンス カウンター]**) の順にクリックし、収集するカウンターを選択します。
* OMS を構成して、検出と診断に役立つ[自動アラート](../log-analytics/log-analytics-alerts.md)を設定する
