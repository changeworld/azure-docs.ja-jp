---
title: コンテナー用 Azure Monitor のトラブルシューティング方法 | Microsoft Docs
description: この記事では、コンテナー用 Azure Monitor に関する問題をトラブルシューティングして解決する方法について説明します。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403373"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>コンテナー用 Azure Monitor のトラブルシューティング

コンテナー用 Azure Monitor を使用して Azure Kubernetes Service (AKS) クラスターの監視を構成するとき、データの収集またはレポート作成を妨げる問題が発生する可能性があります。 この記事では、お問い合わせの多い問題とトラブルシューティングの手順について詳しく説明します。

## <a name="authorization-error-during-onboarding-or-update-operation"></a>オンボードまたは更新操作中の承認エラー
コンテナーに対して Azure Monitor を有効にするか、またはメトリックの収集をサポートするようにクラスターを更新しているときに、*The client <user's Identity>' with object id '<user's objectId>' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/write' over scope* のようなエラーが表示されることがあります。

オンボードまたは更新プロセス中に、クラスター リソースに対する **[監視メトリック パブリッシャー]** ロールの割り当ての付与が試行されます。 コンテナーに対して Azure Monitor を有効にするためのプロセス、またはメトリックの収集をサポートするための更新を開始するユーザーは、AKS クラスター リソースのスコープに対する **Microsoft.Authorization/roleAssignments/write** アクセス許可にアクセスできる必要があります。 このアクセス許可へのアクセスが付与されるのは、 **[所有者]** および **[ユーザー アクセスの管理者]** 組み込みロールのメンバーだけです。 セキュリティ ポリシーできめ細かなレベルのアクセス許可を割り当てる必要がある場合は、[カスタム ロール](../../role-based-access-control/custom-roles.md)を表示し、それを必要なユーザーに割り当てることをお勧めします。 

また、次の手順を実行することによって、Azure Portal からこのロールを手動で付与することもできます。

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. Azure Portal の左上隅にある **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Kubernetes**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Azure Kubernetes]** を選択します。
3. Kubernetes クラスターの一覧から選択します。
2. 左側のメニューから、 **[アクセス制御 (IAM)]** をクリックします。
3. **[+ 追加]** を選択してロールの割り当てを追加し、 **[監視メトリック パブリッシャー]** ロールを選択し、 **[選択]** ボックスに「**AKS**」と入力して、サブスクリプションで定義されたクラスターのサービス プリンシパルに関してのみ結果をフィルター処理します。 そのクラスターに固有の一覧から選択します。
4. **[保存]** を選択して、ロールの割り当てを完了します。 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>コンテナー用 Azure Monitor が有効になっているが、情報がレポートされない
コンテナー用 Azure Monitor が正しく有効にされ、構成されているにもかかわらず、状態情報を表示できない場合、またはログ クエリから結果が返されない場合は、次の手順に従って問題を診断します。 

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

5. エージェントが正常にデプロイされていることを確認するには、`kubectl logs omsagent-484hw --namespace=kube-system` コマンドを実行します。

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

| エラー メッセージ  | アクション |  
| ---- | --- |  
| エラー メッセージ: `No data for selected filters`  | 新しく作成したクラスターの監視データ フローの確立に時間がかかる場合があります。 クラスターのデータが表示されるまで、少なくとも 10 ～ 15 分お待ちください。 |   
| エラー メッセージ: `Error retrieving data` | Azure Kubernetes Service クラスターが正常性とパフォーマンスの監視用に設定される間に、クラスターと Azure Log Analytics ワークスペースの間に接続が確立されます。 Log Analytics ワークスペースは、クラスターのすべての監視データを格納するために使用されます。 Log Analytics ワークスペースが削除されると、このエラーが発生する可能性があります。 ワークスペースが削除されたかどうかを確認します。削除されている場合は、コンテナーの Azure Monitor によるクラスターの監視を再度有効にして、既存のワークスペースを指定するか、新しいワークスペースを作成する必要があります。 再有効化するには、クラスターに対する監視を[無効](container-insights-optout.md)にしてから、コンテナーの Azure Monitor を再び[有効](container-insights-enable-new-cluster.md)にする必要があります。 |  
| az aks cli でコンテナーの Azure Monitor を追加した後の `Error retrieving data` | `az aks cli` を使用して監視を有効にすると、コンテナーの Azure Monitor が正しくデプロイされない可能性があります。 ソリューションがデプロイされているかどうかを確認します。 そのためには、Log Analytics ワークスペースに移動し、左側のウィンドウで **[ソリューション]** を選択して、ソリューションが使用可能かどうかを確認します。 この問題を解決するには、[コンテナー用の Azure Monitor をデプロイする方法](container-insights-onboard.md)に関する記事の手順に従ってソリューションを再デプロイする必要があります |  

問題の診断を助けるために提供されているトラブルシューティング スクリプトを、[こちら](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)で利用できます。

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>コンテナーの Azure Monitor エージェント ReplicaSet Pods は、非 Azure Kubernetes クラスターではスケジュールされない

コンテナーの Azure Monitor エージェント ReplicaSet Pods は、スケジュール用のワーカー (またはエージェント) ノードの次のノード セレクターと依存関係があります。

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

ワーカー ノードにノード ラベルが関連付けられていない場合、エージェント ReplicaSet Pods はスケジュールされません。 ラベルを添付する方法の手順については、[Kubernetes のラベル セレクターの割り当て](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/)に関するページを参照してください。

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>パフォーマンス グラフに Azure 以外のクラスター上のノードとコンテナーの CPU またはメモリが表示されない

コンテナーの Azure Monitor エージェント Pods では、ノード エージェントで cAdvisor エンドポイントを使用して、パフォーマンス メトリックを収集します。 ノードのコンテナー化されたエージェントが、クラスター内のすべてのノードで `cAdvisor port: 10255` を開いてパフォーマンス メトリックを収集できるように構成されていることを確認します。

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>非 Azure Kubernetes クラスターがコンテナーの Azure Monitor に表示されない

コンテナーの Azure Monitor で非 Azure Kubernetes クラスターを表示するには、この分析情報をサポートする Log Analytics ワークスペースと、コンテナーの分析情報ソリューション リソース **ContainerInsights (*ワークスペース*)** で読み取りアクセスが必要です。

## <a name="next-steps"></a>次のステップ

AKS クラスター ノードとポッドの両方について正常性メトリックを取得するための監視が有効になったので、これらの正常性メトリックを Azure portal で利用できます。 コンテナー用 Azure Monitor を使用する方法については、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページをご覧ください。
