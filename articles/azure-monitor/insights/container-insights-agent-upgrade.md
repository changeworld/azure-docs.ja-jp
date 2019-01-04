---
title: コンテナーに対する Azure Monitor (プレビュー) エージェントをアップグレードする方法 | Microsoft Docs
description: この記事では、コンテナーに対する Azure Monitor で使用される Log Analytics エージェントをアップグレードする方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 08206b9de4fca3b422c5b076d7e0c1c180f82fbd
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184087"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>コンテナーに対する Azure Monitor (プレビュー) エージェントをアップグレードする方法
コンテナーに対する Azure Monitor では、コンテナー化されたバージョンの Linux 用 Log Analytics エージェント が使用されます。 エージェントの新しいバージョンがリリースされると、Azure Kubernetes Service (AKS) でホストされているマネージド Kubernetes クラスター上のエージェントが自動的にアップグレードされます。  

エージェントのアップグレードが失敗した場合のために、この記事では、エージェントを手動でアップグレードするプロセスについて説明します。 リリースされたバージョンを確認するには、[エージェントのリリースのお知らせ](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)を参照してください。   

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>監視対象の Kubernetes クラスター上のエージェントのアップグレード
エージェントをアップグレードするプロセスは、2 つの単純な手順で構成されます。 最初の手順は、コンテナーに対する Azure Monitor の監視を Azure CLI を使用して無効にすることです。  [監視の無効化](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli)に関する記事の手順に従ってください。 Azure CLI を使用して、ソリューションとワークスペースに格納されている対応するデータに影響を与えることなく、クラスター内のノードからエージェントを削除できます。 

>[!NOTE]
>このメンテナンス アクティビティを実行している間、クラスター内のノードによる収集されたデータの転送は行われず、エージェントを削除して新しいバージョンをインストールするまでの間、パフォーマンス ビューにデータは表示されません。 
>

新しいバージョンのエージェントをインストールするには、Azure CLI を使用する[監視の利用開始](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli)に関する記事の手順に従って、このプロセスを完了します。  

監視を再び有効にした後、クラスターの更新された正常性メトリックが表示されるまで、約 15 分かかる場合があります。 エージェントが正常にアップグレードされたことを確認するには、`kubectl logs omsagent-484hw --namespace=kube-system` コマンドを実行します

ステータスは次の例のようになります。ここで、*omi* と *omsagent* の値は、[エージェントのリリース履歴](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)で指定された最新バージョンと一致する必要があります。  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="next-steps"></a>次の手順
エージェントのアップグレード中に問題が発生した場合は、[トラブルシューティング ガイド](container-insights-troubleshoot.md)を参照してください。