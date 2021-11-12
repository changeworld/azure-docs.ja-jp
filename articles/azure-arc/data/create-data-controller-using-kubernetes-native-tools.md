---
title: Kubernetes ツールを使用したデータ コントローラーの作成
description: Kubernetes ツールを使用したデータ コントローラーの作成
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 2380a10d9a4a054a5d83b2c1096797e41bba7d0f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563608"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Kubernetes ツールを使用した Azure Arc データ コントローラーの作成


## <a name="prerequisites"></a>前提条件

概要情報については、「[Azure Arc データ コントローラーを作成する](create-data-controller.md)」のトピックを確認してください。

Kubernetes ツールを使用して Azure Arc データ コントローラーを作成するには、Kubernetes ツールがインストールされている必要があります。  この記事の例では `kubectl` を使用しますが、Kubernetes ダッシュボード、`oc`、`helm` などの Kubernetes の他のツールや Kubernetes yaml/json を使い慣れている場合は、それらのツールでも同様のアプローチを使用できます。

[kubectl ツールをインストールする](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> 以下に示す Azure Arc データ コントローラーを作成する手順の一部では、Kubernetes クラスター管理者のアクセス許可が必要です。  Kubernetes クラスター管理者でない場合、代わりにそれらのステップを実行するには、Kubernetes クラスター管理者の権限が必要です。

### <a name="cleanup-from-past-installations"></a>以前のインストールからのクリーンアップ

以前に Azure Arc データ コントローラーをインストールし、同じクラスターで Azure Arc データ コントローラーを削除した場合は、まだ削除が必要なクラスター レベルのオブジェクトが存在する可能性があります。 次のコマンドを実行して、Azure Arc データ コントローラーのクラスター レベルのオブジェクトを削除します。

```console
# Cleanup azure arc data service artifacts

# Note: not all of these objects will exist in your environment depending on which version of the Arc data controller was installed

# Custom resource definitions (CRD)
kubectl delete crd datacontrollers.arcdata.microsoft.com
kubectl delete crd postgresqls.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com
kubectl delete crd dags.sql.arcdata.microsoft.com
kubectl delete crd exporttasks.tasks.arcdata.microsoft.com
kubectl delete crd monitors.arcdata.microsoft.com

# Cluster roles and role bindings
kubectl delete clusterrole arcdataservices-extension
kubectl delete clusterrole arc:cr-arc-metricsdc-reader
kubectl delete clusterrole arc:cr-arc-dc-watch
kubectl delete clusterrole cr-arc-webhook-job
kubectl delete clusterrole {namespace}:cr-upgrade-worker
kubectl delete clusterrolebinding {namespace}:crb-arc-metricsdc-reader
kubectl delete clusterrolebinding {namespace}:crb-arc-dc-watch
kubectl delete clusterrolebinding crb-arc-webhook-job
kubectl delete clusterrolebinding {namespace}:crb-upgrade-worker

# Substitute the name of the namespace the data controller was deployed in into {namespace}.  If unsure, get the name of the mutatingwebhookconfiguration using 'kubectl get clusterrolebinding'

# API services
# Up to May 2021 release
kubectl delete apiservice v1alpha1.arcdata.microsoft.com
kubectl delete apiservice v1alpha1.sql.arcdata.microsoft.com

# June 2021 release
kubectl delete apiservice v1beta1.arcdata.microsoft.com
kubectl delete apiservice v1beta1.sql.arcdata.microsoft.com

# GA/July 2021 release
kubectl delete apiservice v1.arcdata.microsoft.com
kubectl delete apiservice v1.sql.arcdata.microsoft.com

# Substitute the name of the namespace the data controller was deployed in into {namespace}.  If unsure, get the name of the mutatingwebhookconfiguration using 'kubectl get mutatingwebhookconfiguration'
kubectl delete mutatingwebhookconfiguration arcdata.microsoft.com-webhook-{namespace}

```

## <a name="overview"></a>概要

Azure Arc データ コントローラーを作成するステップの概要は、次のとおりです。

   > [!IMPORTANT]
   > 以下の一部の手順では、Kubernetes クラスター管理者のアクセス許可が必要です。

1. Arc データ コントローラー、Azure SQL マネージド インスタンス、PostgreSQL Hyperscale のカスタム リソース定義を作成します。 
1. データ コントローラーが作成される名前空間を作成します。 
1. レプリカ セット、サービス アカウント、ロール、ロール バインディングを含め、ブートストラップ サービスを作成します。
1. データ コントローラー管理者のユーザー名とパスワードのシークレットを作成します。
1. Webhook デプロイ ジョブ、クラスター ロール、クラスター ロールのバインドを作成します。 
1. データ コントローラーを作成します。


## <a name="create-the-custom-resource-definitions"></a>カスタム リソース定義を作成する

次のコマンドを実行して、カスタム リソース定義を作成します。  

   > [!IMPORTANT]
   > Kubernetes クラスター管理者のアクセス許可が必要です。

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>データ コントローラーが作成される名前空間を作成する

次のようなコマンドを実行して、データ コントローラーが作成される新しい専用の名前空間を作成します。  この例とこの記事の残りの例では、`arc` の名前空間名が使用されます。 別の名前を使用する場合は、全体で同じ名前を使用してください。

```console
kubectl create namespace arc
```
OpenShift を使用している場合は、`kubectl edit namespace <name of namespace>` を使用して名前空間の `openshift.io/sa.scc.supplemental-groups` 注釈と `openshift.io/sa.scc.uid-range` 注釈を編集する必要があります。  これらの既存の注釈を、これらの _特定_ の UID および fsGroup の ID または範囲に合わせて変更します。

```console
openshift.io/sa.scc.supplemental-groups: 1000700001/10000
openshift.io/sa.scc.uid-range: 1000700001/10000
```

クラスター管理者でない他のユーザーがこの名前空間を使用する場合は、名前空間管理者ロールを作成し、ロール バインディングを通じてそのロールをそれらのユーザーに付与することをお勧めします。  名前空間管理者は、名前空間に対する完全なアクセス許可を持っている必要があります。  より詳細なロールとロール バインドの例については、[Azure Arc GitHub リポジトリ](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/rbac)を参照してください。

## <a name="create-the-bootstrapper-service"></a>ブートストラップ サービスを作成する

ブートストラップ サービスでは、データ コントローラー、SQL マネージド インスタンス、PostgreSQL Hyperscale サーバー グループなどのカスタム リソースの作成、編集、削除を行う受信要求が処理されます。

次のコマンドを実行して、ブートストラップ サービス、そのブートストラップ サービス用のサービス アカウント、そのブートストラップ サービス アカウントのロールとロールのバインドを作成します。

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

次のコマンドを使用して、ブートストラップ ポッドが実行されていることを確認します。  状態が `Running` に変わるまで、数回実行することが必要になる場合があります。

```console
kubectl get pod --namespace arc
```

ブートストラップの yaml テンプレート ファイルにより、既定で Microsoft Container Registry (MCR) からブートストラップのコンテナー イメージがプルされます。  お使いの環境で Microsoft Container Registry に直接アクセスできない場合は、次の操作を実行できます。
- 次のステップに従って、[Microsoft Container Registry からコンテナーイメージをプルし、それらをプライベート コンテナー レジストリにプッシュ](offline-deployment.md)します。
- プライベート コンテナー レジストリ用に[イメージのプル シークレットを作成](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin)します。
- イメージのプル シークレットをブートストラップ コンテナーに追加します。 以下の例を参照してください。
- ブートストラップ イメージのイメージの場所を変更します。 以下の例を参照してください。

次の例では、イメージ プル シークレット名 `arc-private-registry` を作成した場合を想定しています。

```yaml
#Just showing only the relevant part of the bootstrapper.yaml template file here
    spec:
      serviceAccountName: sa-bootstrapper
      nodeSelector:
        kubernetes.io/os: linux
      imagePullSecrets:
      - name: arc-private-registry #Create this image pull secret if you are using a private container registry
      containers:
      - name: bootstrapper
        image: mcr.microsoft.com/arcdata/arc-bootstrapper:v1.1.0_2021-11-02 #Change this registry location if you are using a private container registry.
        imagePullPolicy: Always
```

## <a name="create-secrets-for-the-metrics-and-logs-dashboards"></a>メトリックとログのダッシュボードのシークレットを作成する

メトリックとログのダッシュボードに対する認証に使用するユーザー名とパスワードを管理者として指定できます。 安全なパスワードを選択し、これらの特権を必要とするユーザーとのみ共有します。

Kubernetes シークレットは base64 でエンコードされた文字列として格納されます。1 つはユーザー名、1 つはパスワード用です。

オンライン ツールを使用して、希望するユーザー名とパスワードを base64 でエンコードできます。また、プラットフォームに応じて組み込みの CLI ツールを使用することもできます。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

ユーザー名とパスワードをエンコードした後に、[テンプレート ファイル](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml)に基づいてファイルを作成し、ユーザー名とパスワードの値を独自に置き換えることができます。

それから、次のコマンドを実行してシークレットを作成します。

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-webhook-deployment-job-cluster-role-and-cluster-role-binding"></a>Webhook デプロイ ジョブ、クラスター ロール、クラスター ロールのバインドを作成する

まず、[テンプレート ファイル](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/web-hook.yaml)のコピーをコンピューターのローカルに作成し、設定の一部を変更できるようにします。

ファイルを編集し、すべての場所で `{{namespace}}` を前の手順で作成した名前空間の名前に置き換えます。 **ファイルを保存します。**

次のコマンドを実行して、クラスター ロールとクラスター ロールのバインドを作成します。  

   > [!IMPORTANT]
   > Kubernetes クラスター管理者のアクセス許可が必要です。

```console
kubectl create -n arc -f <path to the edited template file on your computer>
```


## <a name="create-the-data-controller"></a>データ コントローラーを作成する

これで、データ コントローラー自体を作成する準備が整いました。

まず、[テンプレート ファイル](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml)のコピーをコンピューターのローカルに作成し、設定の一部を変更できるようにします。

必要に応じて、次の値を編集します。

**必須**
- **location**:これを、データ コントローラーに関する "_メタデータ_" が格納される Azure の場所に変更します。  使用できる Azure の場所の一覧は、[データ コントローラーの作成の概要](create-data-controller.md)に関する記事で確認できます。
- **resourceGroup**: Azure Resource Manager でデータ コントローラーの Azure リソースを作成する Azure リソース グループ。  通常、このリソース グループは既に存在しているはずですが、データを Azure にアップロードするときまでは必要ありません。
- **subscription**: Azure リソースを作成するサブスクリプションの Azure サブスクリプション GUID。

**見直してできれば既定値を変更することを推奨**
- **storage..className**: データ コントローラーのデータ ファイルとログ ファイルに使用するストレージ クラス。  Kubernetes クラスターで使用できるストレージ クラスがわからない場合は、次のコマンドを実行します。`kubectl get storageclass`  既定値は `default` で、既定のストレージ クラスではなく、`default` という名前の既存のストレージ クラスがあることを前提としています。  注:目的のストレージ クラスに設定される className 設定は 2 つあり、1 つはデータ用で、もう 1 つはログ用です。
- **serviceType**:LoadBalancer を使用していない場合は、サービス タイプを `NodePort` に変更します。
- **Security**: Azure Red Hat OpenShift または Red Hat OpenShift コンテナー プラットフォームの場合は、データ コントローラーの yaml ファイルで、`security:` 設定を下記の値に置き換えます。

```yml
  security:
    allowDumps: false
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
```

**省略可能**
- **name**:データ コントローラーの既定の名前は `arc` ですが、必要に応じて変更できます。
- **displayName**:ファイルの先頭にある name 属性と同じ値に設定します。
- **registry**:既定では Microsoft Container Registry です。  Microsoft Container Registry からイメージを取得して[プライベート コンテナー レジストリにプッシュ](offline-deployment.md)している場合は、ここでレジストリの IP アドレスまたは DNS 名を入力します。
- **dockerRegistry**:必要に応じて、プライベート コンテナー レジストリからイメージをプルするために使用するイメージのプル シークレット。
- **repository**:Microsoft Container Registry 上の既定のリポジトリは `arcdata` です。  プライベート コンテナー レジストリを使用している場合は、Azure Arc 対応データ サービスのコンテナー イメージが格納されているフォルダーまたはリポジトリのパスを入力します。
- **imageTag**: テンプレート内には最新バージョンのタグが既定で設定されていますが、古いバージョンを使用する必要がある場合は変更できます。

次の例は、完成したデータ コントローラーの yaml ファイルを示しています。 お客様の要件と、上記の情報に基づいて、実際の環境に合わせて例を更新します。

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sa-arc-controller
---
apiVersion: arcdata.microsoft.com/v1
kind: DataController
metadata:
  generation: 1
  name: arc-dc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    dockerRegistry: arc-private-registry #Create a registry secret named 'arc-private-registry' if you are going to pull from a private registry instead of MCR.
    serviceAccount: sa-arc-controller
  docker:
    imagePullPolicy: Always
    imageTag: v1.1.0_2021-11-02
    registry: mcr.microsoft.com
    repository: arcdata
  infrastructure: other #Must be a value in the array [alibaba, aws, azure, gcp, onpremises, other]
  security:
    allowDumps: true #Set this to false if deploying on OpenShift
    allowNodeMetricsCollection: true #Set this to false if deploying on OpenShift
    allowPodMetricsCollection: true #Set this to false if deploying on OpenShift
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer # Modify serviceType based on your Kubernetes environment
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: indirect
      location: eastus # Choose a different Azure location if you want
      resourceGroup: <your resource group>
      subscription: <your subscription GUID>
    controller:
      displayName: arc-dc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 10Gi
```

編集したファイルをローカル コンピューターに保存し、次のコマンドを実行してデータ コントローラーを作成します。

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>作成状態の監視

コントローラーの作成が完了するまでに数分かかります。 次のコマンドを使用して、別のターミナル ウィンドウで進行状況を監視できます。

> [!NOTE]
>  次のコマンド例では、`arc` という名前のデータ コントローラーと Kubernetes 名前空間が作成されていることを前提としています。  異なる名前の名前空間/データ コントローラーを使用している場合は、`arc` をその名前で置き換えてください。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

次のようなコマンドを実行して、特定のポッドの作成状態を確認することもできます。  これは特に、何らの問題をトラブルシューティングするのに役立ちます。

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>作成の問題のトラブルシューティング

作成で問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Kubernetes ネイティブ ツールを使用して SQL マネージド インスタンスを作成する](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Kubernetes ネイティブ ツールを使用して PostgreSQL Hyperscale サーバー グループを作成する](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
