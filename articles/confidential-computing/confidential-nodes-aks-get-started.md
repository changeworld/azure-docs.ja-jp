---
title: クイック スタート:コンフィデンシャル コンピューティング ノードを含んだ Azure Kubernetes Service (AKS) クラスターを Azure CLI を使用してデプロイする
description: Azure CLI を使用して、機密ノードを含んだ AKS クラスターを作成し、Hello World アプリをデプロイする方法について説明します。
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/25/2020
ms.author: amgowda
ms.openlocfilehash: 51b0813849236d9335d1482019f740fc8b23749f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703288"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>クイックスタート: コンフィデンシャル コンピューティング ノード (DCsv2) を含む Azure Kubernetes Service (AKS) クラスターを Azure CLI を使用してデプロイする

このクイックスタートは、Azure のマネージド Kubernetes サービスを使用してアプリケーションを監視するために、AKS クラスターをすぐに作成してアプリケーションをデプロイしたいと考える開発者またはクラスター オペレーターを対象としています。 Azure portal からクラスターをプロビジョニングしてコンフィデンシャル コンピューティング ノードを追加することもできます。

## <a name="overview"></a>概要

このクイックスタートでは、Azure CLI を使用して、コンフィデンシャル コンピューティング ノードを含む Azure Kubernetes Service (AKS) クラスターをデプロイし、単純な Hello World アプリケーションをエンクレーブ内で実行する方法について説明します。 AKS は、クラスターをすばやくデプロイおよび管理することができる、マネージド Kubernetes サービスです。 AKS の詳細については、[こちら](../aks/intro-kubernetes.md)を参照してください。

> [!NOTE]
> コンフィデンシャル コンピューティング DCsv2 VM には、上位の価格が適用されて利用可能なリージョンが限られる特殊なハードウェアが活用されています。 詳細については、仮想マシンに関するページで[利用可能な SKU とサポートされるリージョン](virtual-machine-solutions.md)を参照してください。

### <a name="confidential-computing-node-features-dcxs-v2"></a>コンフィデンシャル コンピューティング ノードの機能 (DC<x>s-v2)

1. Linux コンテナーをサポートする Linux ワーカー ノード
1. Ubuntu 18.04 仮想マシン ノードを含む第 2 世代 VM
1. Intel SGX ベースの CPU と EPC (Encrypted Page Cache) メモリ。 詳細については、[こちら](./faq.md)をご覧ください。
1. Kubernetes バージョン 1.16 以降をサポート
1. AKS ノードにプレインストールされた Intel SGX DCAP ドライバー。 詳細については、[こちら](./faq.md)をご覧ください。

## <a name="deployment-prerequisites"></a>デプロイの前提条件
デプロイのチュートリアルでは、以下が必要です。

1. 有効な Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)アカウントを作成してください。
1. デプロイ マシンに Azure CLI バージョン 2.0.64 以降がインストールされ、構成されている (バージョンを調べるには `az --version` を実行します)。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](../container-registry/container-registry-get-started-azure-cli.md)に関するページを参照してください
1. ご利用のサブスクリプションで最低 6 つの **DC<x>s-v2** コアが利用できる。 既定では、コンフィデンシャル コンピューティングの VM コア クォータは、Azure サブスクリプションごとに 8 コアです。 プロビジョニングする予定のクラスターに必要なコア数が 8 を超える場合は、[こちら](../azure-portal/supportability/per-vm-quota-requests.md)の手順に従って、クォータの引き上げチケットを起票してください。

## <a name="creating-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>コンフィデンシャル コンピューティング ノードとアドオンを含む AKS クラスターを新規作成する
下の手順に従って、アドオンを含むコンフィデンシャル コンピューティング対応ノードを追加します。

### <a name="step-1-creating-an-aks-cluster-with-system-node-pool"></a>手順 1: システム ノード プールを含む AKS クラスターを作成する

前述の要件を満たしている AKS クラスターが既にある場合は、[既存のクラスターのセクションに進み](#existing-cluster)、新しいコンフィデンシャル コンピューティング ノード プールを追加してください。

まず、az group create コマンドを使用して、クラスターのリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *westus2* リージョンに作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

ここで、az aks create コマンドを使用して、AKS クラスターを作成します。

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
上記を実行すると、システム ノード プールを含む、アドオンが有効な状態の新しい AKS クラスターが作成されます。 次に、AKS (DCsv2) に、コンフィデンシャル コンピューティング ノードプール型のユーザー ノードを追加します

### <a name="step-2-adding-confidential-computing-node-pool-to-aks-cluster"></a>手順 2: コンフィデンシャル コンピューティング ノード プールを AKS クラスターに追加する 

下のコマンドを 3 つのノードを持つ `Standard_DC2s_v2` サイズのユーザー ノードプールに対して実行します。 その他、サポートされている一連の DCsv2 SKU およびリージョンは、[こちら](../virtual-machines/dcv2-series.md)から選択できます。

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```
上のコマンドを実行すると、**DC<x>s-v2** の新しいノード プールが完成し、コンフィデンシャル コンピューティング アドオン デーモンセット ([SGX デバイス プラグイン](confidential-nodes-aks-overview.md#sgx-plugin)) と共に表示されます。
 
### <a name="step-3-verify-the-node-pool-and-add-on"></a>手順 3: ノードプールとアドオンを確認する
az aks get-credentials コマンドを使用して、AKS クラスターの資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
**DC<x>s-v2** ノード プールにノードが適切に作成され、SGX 関連のデーモンセットが実行されていることを確認します。以下のように、kubectl get pods と kubectl get nodes コマンドを使用します。

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
上に示したような出力結果が得られたら、AKS クラスターで機密アプリケーションを実行する準備は完了です。

[エンクレーブからの Hello World](#hello-world) のデプロイ セクションに移動して、エンクレーブでアプリをテストしてください。 または、下の手順に従ってさらにノード プールを AKS に追加します (AKS では SGX ノード プールと非 SGX ノード プールの混在がサポートされます)。

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>コンフィデンシャル コンピューティング ノード プールを既存の AKS クラスターに追加する<a id="existing-cluster"></a>

このセクションでは、「前提条件」セクションに記載した条件を満たす AKS クラスターが既に実行されていることを前提としています (アドオンに適用)。

### <a name="step-1-enabling-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>手順 1: 既存のクラスターでコンフィデンシャル コンピューティング AKS アドオンを有効にする

下のコマンドを実行して、コンフィデンシャル コンピューティング アドオンを有効にします。

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
### <a name="step-2-add-dcxs-v2-user-node-pool-to-the-cluster"></a>手順 2: **DC<x>s-v2** ユーザー ノード プールをクラスターに追加する
    
> [!NOTE]
> コンフィデンシャル コンピューティングの機能を使用するためには、既存の AKS クラスターに **DC<x>s-v2** VM SKU ベースのノード プールが少なくとも 1 つ存在する必要があります。 コンフィデンシャル コンピューティング DCsv2 VM SKU について詳しくは、[使用可能な SKU とサポートされるリージョン](virtual-machine-solutions.md)に関するページを参照してください。
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```
上のコマンドにより、confcompool1 という名前で追加した最新のノードプールが表示されます。

### <a name="step-3-verify-that-daemonsets-are-running-on-confidential-node-pools"></a>手順 3: デーモンセットがコンフィデンシャル ノード プールで実行されていることを確認する

既存の AKS クラスターにログインし、下の検証を実行します。 

```console
kubectl get nodes
```
出力結果に、AKS クラスターに新しく追加された confcompool1 が表示されます。

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
上に示したような出力結果が得られたら、AKS クラスターで機密アプリケーションを実行する準備は完了です。 下のテスト アプリケーションのデプロイに従ってください。

## <a name="hello-world-from-isolated-enclave-application"></a>隔離されたエンクレーブ アプリケーションからの Hello World<a id="hello-world"></a>
*hello-world-enclave.yaml* という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 この Open Enclave ベースのサンプル アプリケーション コードは、[Open Enclave プロジェクト](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)でご覧いただけます。 以下のデプロイでは、アドオン "confcom" がデプロイされていることを前提としています。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

今度は、kubectl apply コマンドを使用し、セキュア エンクレーブで起動するサンプル ジョブを作成します (次の出力例を参照)。

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

ワークロードによって Trusted Execution Environment (エンクレーブ) が正常に作成されたことは、次のコマンドを実行することで確認できます。

```console
$ kubectl get jobs -l app=sgx-test
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

関連付けられているノード プールを削除するか、AKS クラスターを削除するには、以下のコマンドを使用します。

AKS クラスターを削除する
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>次のステップ

[機密コンテナーのサンプル](https://github.com/Azure-Samples/confidential-container-samples)を参照します。機密コンテナーを使用して、Python、Node などのアプリケーションを、機密性を保った状態で実行してみましょう。

[エンクレーブ対応の Azure Container サンプル](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)を参照して、エンクレーブ対応のアプリケーションを実行します。
