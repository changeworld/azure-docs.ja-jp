---
title: コンテナー用 Azure Monitor のトラブルシューティング方法 | Microsoft Docs
description: この記事では、コンテナー用 Azure Monitor に関する問題をトラブルシューティングして解決する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2018
ms.author: magoedte
ms.openlocfilehash: 5a9211abdbc4c9ea7907dfac00d449317dd13089
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190749"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>コンテナー用 Azure Monitor のトラブルシューティング

コンテナー用 Azure Monitor を使用して Azure Kubernetes Service (AKS) クラスターの監視を構成するとき、データの収集またはレポート作成を妨げる問題が発生する可能性があります。 この記事では、お問い合わせの多い問題とトラブルシューティングの手順について詳しく説明します。

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>コンテナー用 Azure Monitor が有効になっているが、情報がレポートされない
コンテナー用 Azure Monitor が正しく有効化され、構成されているにもかかわらず、状態情報を表示できない場合、または Log Analytics ログ クエリから結果が返されない場合は、次の手順に従って問題を診断します。 

1. 次のコマンドを実行して、エージェントの状態を確認します。 

    `kubectl get ds omsagent --namespace=kube-system`

    出力は次のようになり、適切にデプロイされたことが示されます。

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. 次のコマンドを使用して、エージェント バージョン *06072018* 以降のデプロイ状態を確認します。

    `kubectl get deployment omsagent-rs -n=kube-system`

    出力は次の例のようになり、適切にデプロイされたことが示されます。

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. `kubectl get pods --namespace=kube-system` コマンドを実行して、ポッドの状態を調べ、実行中であることを確認します

    omsagent の状態として、次の例のような *[実行中]* の状態が出力される必要があります。

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. エージェントのログを確認します。 コンテナー化されたエージェントは、デプロイされた時点で、OMI コマンドを実行して簡単なチェックを行い、エージェントとプロバイダーのバージョンを表示します。 

5. エージェントが正常にオンボードされていることを確認するには、`kubectl logs omsagent-484hw --namespace=kube-system` コマンドを実行します

    状態は次の例のようになります。

    ```
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
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>エラー メッセージ

次の表は、コンテナー用 Azure Monitor の使用中に発生する可能性のある既知のエラーをまとめたものです。

| エラー メッセージ  | Action |  
| ---- | --- |  
| エラー メッセージ: `No data for selected filters`  | 新しく作成したクラスターの監視データ フローの確立に時間がかかる場合があります。 クラスターのデータが表示されるまで、少なくとも 10 ～ 15 分お待ちください。 |   
| エラー メッセージ: `Error retrieving data` | Azure Kubenetes Service クラスターが正常性とパフォーマンスの監視用に設定される間に、クラスターと Azure Log Analytics ワークスペースの間に接続が確立されます。 Log Analytics ワークスペースは、クラスターのすべての監視データを格納するために使用されます。 Log Analytics ワークスペースが削除されるか失われると、このエラーが発生する可能性があります。 [アクセスの管理](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json#workspace-information)に関する記事を参照して、ワークスペースが使用可能かどうかを確認してください。 ワークスペースがない場合は、コンテナー用 Azure Monitor にクラスターを再オンボードする必要があります。 再オンボードするには、クラスターに対する監視を[無効](container-insights-optout.md)にしてから、コンテナーの Azure Monitor を再び[有効](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster)にする必要があります。 |  
| az aks cli でコンテナーの Azure Monitor を追加した後の `Error retrieving data` | `az aks cli` を使用してオンボードすると、コンテナーの Azure Monitor が正しくオンボードされない可能性があります。 ソリューションがオンボードされているかどうかを確認します。 そのためには、Log Analytics ワークスペースに移動し、左側のウィンドウで **[ソリューション]** を選択して、ソリューションが使用可能かどうかを確認します。 この問題を解決するには、[コンテナー用の Azure Monitor をデプロイする方法](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json)に関する記事の手順に従ってソリューションを再デプロイする必要があります |  

問題の診断を助けるために提供されているトラブルシューティング スクリプトを、[こちら](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)で利用できます。  

## <a name="next-steps"></a>次の手順
AKS クラスター ノードとポッドの両方について正常性メトリックを取得するための監視が有効になったので、これらの正常性メトリックを Azure portal で利用できます。 コンテナー用 Azure Monitor を使用する方法については、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページをご覧ください。