---
title: Azure Kubernetes Service (AKS) コントローラー ログの表示
description: Azure Kubernetes Service (AKS) で Kubernetes マスター ノードのログを有効にし、それらを表示する方法について説明します
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 07/26/2018
ms.author: iainfou
ms.openlocfilehash: 04afd71183bcb8001d017b0027f29338b8d67ddb
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42442369"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Kubernetes マスター ノード ログの有効化とレビュー

Azure Kubernetes Service (AKS) では、*kube-apiserver* や *kube-controller-manager* などのマスター コンポーネントが、マネージド サービスとして提供されます。 管理者は、*kubelet* とコンテナー ランタイムを実行するノードを作成して管理し、 マネージド Kubernetes API サーバーを通じてアプリケーションをデプロイします。 アプリケーションやサービスのトラブルシューティングを行う際には、これらのマスター コンポーネントによって生成されたログを表示する必要が生じることがあります。 この記事では、Azure Log Analytics を使用して、Kubernetes マスター コンポーネントからのログを有効にし、それらのログを照会する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure アカウントで既存の AKS クラスターが実行されていることを前提条件としています。 AKS クラスターがまだない場合は、[Azure CLI][cli-quickstart] または [Azure Portal][portal-quickstart] を使用して AKS クラスターを作成してください。 Log Analytics は、RBAC 対応と非 RBAC 対応のどちらの AKS クラスターとでも動作します。

## <a name="enable-diagnostics-logs"></a>診断ログの有効化

複数のソースからデータを効率よく収集してレビューできるようにするため、Log Analytics では、環境に関するインサイトを取得するためのクエリ言語と分析エンジンが提供されています。 ワークスペースは、データの照合と分析のために使用されます。ワークスペースは、他の Azure サービス (Application Insights や Security Center など) と統合できます。 別のプラットフォームを使用してログを分析する必要がある場合は、Azure ストレージ アカウントやイベント ハブに診断ログを送信することもできます。 詳しくは、「[Azure Log Analytics とは][log-analytics-overview]」をご覧ください。

Log Analytics の有効化と管理は、Azure Portal で行います。 AKS クラスターの Kubernetes マスター コンポーネントのログ収集を有効にするには、Web ブラウザーで Azure Portal を開き、次の手順を実行します。

1. AKS クラスターのリソース グループを選択します (*myResourceGroup* など)。 個別の AKS クラスター リソースを含んだリソース グループは選択しないでください (*MC_myResourceGroup_myAKSCluster_eastus* など)。
1. 左側で、**[診断設定]** を選択します。
1. AKS クラスター (*myAKSCluster* など) を選択し、**[診断をオンにする]** を選択します。
1. 名前 (*myAKSLogs* など) を入力し、**[Log Analytics への送信]** オプションを選択します。
    * Log Analytics の *[構成]* を選択し、既存のワークスペースまたは **[新しいワークスペースの作成]** を選択します。
    * ワークスペースを作成する必要がある場合は、名前、リソース グループ、および場所を指定します。
1. 使用可能なログの一覧で、有効にするログを選択します (*kube-apiserver*、*kube-controller-manager*、*kube-scheduler* など)。 Log Analytics が有効になった後、構成画面に戻り、収集されるログを変更することもできます。
1. 準備ができたら **[保存]** を選択し、選択したログの収集を有効にします。

次に示すポータルのスクリーン ショットは、*[診断設定]* ウィンドウと、OMS ワークスペースを作成するオプションの例です。

![AKS クラスターの Log Analytics に使用する OMS ワークスペースを有効にする](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>AKS クラスター上のテスト ポッドのスケジュール設定

ログを生成するには、AKS クラスター内に新しいポッドを作成します。 基本的な NGINX インスタンスを作成するには、次の YAML マニフェストの例を使用できます。 任意のエディターで `nginx.yaml` という名前のファイルを作成し、次の内容を貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: myfrontend
    image: nginx
    ports:
    - containerPort: 80
```

次の例のように、[kubectl create][kubectl-create] コマンドを使ってポッドを作成し、YAML ファイルを指定します。

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>収集したデータの表示

診断ログが有効になって OMS ワークスペースに表示されるまでには、数分かかる場合があります。 Azure Portal で、Log Analytics ワークスペースのリソース グループ (*myResourceGroup* など) を選択し、Log Analytics リソース (*myAKSLogs* など) を選択します。

![AKS クラスターの Log Analytics ワークスペースを選択する](media/view-master-logs/select-log-analytics-workspace.png)

左側で、**[ログ検索]** を選択します。 *kube-apiserver* を表示するには、テキスト ボックスに次のクエリを入力します。

```
search *
| where Type == "AzureDiagnostics"
| where Category == "kube-apiserver"
| project log_s
```

通常、多くのログは API サーバーに対して返されます。 クエリの対象を絞り込み、前の手順で作成した NGINX ポッドに関するログを表示するには、次の例のように *where* ステートメントを追加して、*pods/nginx* を検索します。

```
search *
| where Type == "AzureDiagnostics"
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

次のスクリーン ショットのように、NGINX ポッドのログが表示されます。

![サンプルの NGINX ポッドに対する Log Analytics クエリ 結果](media/view-master-logs/log-analytics-query-results.png)

クエリの*カテゴリ*名を *kube-controller-manager* や *kube-scheduler* に更新して (つまり、有効にするログの名前を指定して)、追加のログを表示することもできます。 さらに、*where* ステートメントを追加して目的のイベントを絞り込むこともできます。

クエリの実行やログ データの絞り込みの方法について詳しくは、「[Log Analytics のログ検索で収集されたデータの表示または分析][analyze-log-analytics]」をご覧ください。

## <a name="log-event-schema"></a>ログのイベント スキーマ

次の表は、各イベントに使用されるスキーマの説明をまとめたものです。ログ データを分析する際にご使用ください。

| フィールド名               | 説明 |
|--------------------------|-------------|
| *resourceId*             | ログを生成した Azure リソース |
| *time*                   | ログがアップロードされた時刻のタイムスタンプ |
| *category*               | ログを生成するコンテナー/コンポーネントの名前 |
| *operationName*          | 常に *Microsoft.ContainerService/managedClusters/diagnositicLogs/Read* |
| *properties.log*         | コンポーネントから送られたログのフルテキスト |
| *properties.stream*      | *stderr* または *stdout* |
| *properties.pod*         | ログの取得元のポッド名 |
| *properties.containerID* | このログの取得元の docker コンテナーの ID |

## <a name="next-steps"></a>次の手順

この記事では、AKS クラスターの Kubernetes マスター コンポーネントのログを有効にして表示する方法について説明しました。 さらに詳細な監視方法やトラブルシューティング方法については、[Kubelet ログの表示][kubelet-logs]と[SSH ノード アクセスの有効化][aks-ssh]に関する記事をご覧ください。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../log-analytics/log-analytics-tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md