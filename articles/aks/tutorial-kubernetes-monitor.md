---
title: Kubernertes on Azure チュートリアル - Kubernetes の監視
description: AKS チュートリアル - Azure Log Analytics で Kubernetes を監視する
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 522109e37a0e8a54848b524697e601b4ea8992d5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-monitor-azure-kubernetes-service-aks"></a>チュートリアル: Azure Kubernetes Service (AKS) の監視

Kubernetes クラスターとコンテナーの監視は重要なことであり、複数のアプリケーションを含む大規模な運用クラスターを実行するときは特に重要です。

このチュートリアルでは、[Log Analytics のコンテナー監視ソリューション][log-analytics-containers]を使用して AKS クラスターの監視を構成します。

この 8 部構成の 7 番目のチュートリアルでは、次のタスクについて説明します。

> [!div class="checklist"]
> * コンテナー監視ソリューションの構成
> * 監視エージェントの構成
> * Azure Portal で監視情報にアクセスする

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、イメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。

これらの手順を完了しておらず、手順を実行する場合は、「[チュートリアル 1 - コンテナー イメージを作成する][aks-tutorial-prepare-app]」に戻ってください。

## <a name="configure-the-monitoring-solution"></a>監視ソリューションの構成

Azure Portal で **[リソースの作成]** を選択し、`Container Monitoring Solution` を検索します。 見つかったら、**[作成]** を選択します。

![ソリューションの追加](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

新しい Log Analytics ワークスペースを作成するか、既存の Log Analytics ワークスペースを選択します。 Log Analytics ワークスペース フォームでは、このプロセスについて説明しています。

ワークスペースを作成するときに、簡単に取得できるように **[ダッシュボードにピン留めする]** を選択します。

![Log Analytics ワークスペース](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

操作が完了したら、**[OK]** をクリックします。 検証が完了したら、**[作成]** を選択して、コンテナー監視ソリューションを作成します。

ワークスペースが作成されると、Azure ポータルに表示されます。

## <a name="get-workspace-settings"></a>ワークスペースの設定を取得する

Kubernetes ノードにソリューション エージェントを構成するには、Log analytics ワークスペース ID およびキーが必要です。

これらの値を取得するには、コンテナー ソリューションの左側のメニューから **[OMS ワークスペース]** を選択します。 **[詳細設定]** を選択し、**ワークスペース ID** と**主キー**を書き留めます。

## <a name="create-kubernetes-secret"></a>Kubernetes シークレットを作成する

[kubectl create secret][kubectl-create-secret] コマンドを使用して、Log Analytics ワークスペース設定を `omsagent-secret` という名前の Kubernetes シークレットに格納します。 `WORKSPACE_ID` を Log Analytics ワークスペース ID で更新し、`WORKSPACE_KEY` をワークスペース キーで更新します。

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="configure-monitoring-agents"></a>監視エージェントの構成

次の Kubernetes マニフェスト ファイルを使用して、Kubernetes クラスター上にコンテナー監視エージェントを構成できます。 このファイルでは、各クラスター ノードで単一のポッドを実行する Kubernetes [DaemonSet][kubernetes-daemonset] が作成されます。

次のテキストを `oms-daemonset.yaml` という名前のファイルに保存します。

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
```

次のコマンドを使って DaemonSet を作成します。

```azurecli
kubectl create -f oms-daemonset.yaml
```

DaemonSet が作成されたことを確認するには、次のコマンドを実行します。

```azurecli
kubectl get daemonset
```

次のような出力になります。

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

エージェントが実行状態になった後、Log Analytics がデータを取り込んで処理するまでには数分かかります。

## <a name="access-monitoring-data"></a>監視データにアクセスする

Azure ポータルで、ポータル ダッシュボードにピン留めされた Log Analytics ワークスペースを選択します。 **[コンテナー監視ソリューション]** タイルをクリックします。 ここで、AKS クラスターとクラスターのコンテナーに関する情報を表示できます。

![ダッシュボード](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

監視データの照会と分析の詳しいガイダンスについては、[Azure Log Analytics のドキュメント][log-analytics-docs]をご覧ください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Log Analytics を使用して Kubernetes クラスターを監視しました。 次のタスクを行いました。

> [!div class="checklist"]
> * コンテナー監視ソリューションの構成
> * 監視エージェントの構成
> * Azure Portal で監視情報にアクセスする

次のチュートリアルに進んで、Kubernetes の新しいバージョンへのアップグレードについて学習してください。

> [!div class="nextstepaction"]
> [Kubernetes のアップグレード][aks-tutorial-upgrade]

<!-- LINKS - external -->
[kubectl-create-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[log-analytics-containers]: ../log-analytics/log-analytics-containers.md
[log-analytics-docs]: ../log-analytics/index.yml
