---
title: クイック スタート:コンフィデンシャル コンピューティング ノードを含んだ Azure Kubernetes Service (AKS) クラスターを Azure CLI を使用してデプロイする
description: Azure CLI を使用して、機密ノードを含んだ AKS クラスターを作成し、Hello World アプリをデプロイする方法について説明します。
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: c8c64dadebb092d7f376fd2b6590b26f4dde0ee0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "90998479"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-using-azure-cli-preview"></a>クイック スタート:コンフィデンシャル コンピューティング ノードを含んだ Azure Kubernetes Service (AKS) クラスターを Azure CLI を使用してデプロイする (プレビュー)

このクイックスタートは、Azure のマネージド Kubernetes サービスを使用してアプリケーションを監視するために、AKS クラスターをすぐに作成してアプリケーションをデプロイしたいと考える開発者またはクラスター オペレーターを対象としています。

## <a name="overview"></a>概要

このクイックスタートでは、Azure CLI を使用して、コンフィデンシャル コンピューティング ノードを含んだ Azure Kubernetes Service (AKS) クラスターをデプロイし、Hello World アプリケーションをエンクレーブ内で実行する方法について説明します。 AKS は、クラスターをすばやくデプロイおよび管理することができる、マネージド Kubernetes サービスです。 AKS の詳細については、[こちら](https://docs.microsoft.com/azure/aks/intro-kubernetes)を参照してください。

> [!NOTE]
> コンフィデンシャル コンピューティング DCsv2 VM には、上位の価格が適用されて利用可能なリージョンが限られる特殊なハードウェアが活用されています。 詳細については、仮想マシンに関するページで[利用可能な SKU とサポートされるリージョン](virtual-machine-solutions.md)を参照してください。

### <a name="deployment-pre-requisites"></a>デプロイの前提条件

1. 有効な Azure サブスクリプションを持っている。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)アカウントを作成してください。
1. デプロイ マシンに Azure CLI バージョン 2.0.64 以降がインストールされ、構成されている (バージョンを調べるには  `az --version` を実行します)。 インストールまたはアップグレードする必要がある場合は、 [Azure CLI のインストール](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)に関するページを参照してください。
1. [aks-preview 拡張機能](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) (バージョン 0.4.62 以降) 
1. ご利用のサブスクリプションで最低 6 つの DCSv2 コアが利用できる。 既定では、コンフィデンシャル コンピューティングの VM コア クォータは、Azure サブスクリプションごとに 8 コアです。 プロビジョニングする予定のクラスターに必要なコア数が 8 を超える場合は、[こちら](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)の手順に従って、クォータの引き上げチケットを起票してください。

### <a name="confidential-computing-node-features"></a>コンフィデンシャル コンピューティング ノードの機能

1. Linux コンテナーのみをサポートする Linux ワーカー ノード
1. Ubuntu 第 2 世代 18.04 仮想マシン
1. Intel SGX ベースの CPU と EPC (Encrypted Page Cache) メモリ。 詳細については、[こちら](https://docs.microsoft.com/azure/confidential-computing/faq)をご覧ください。
1. Kubernetes バージョン 1.16 以上
1. Intel SGX DCAP ドライバーのプレインストール。 詳細については、[こちら](https://docs.microsoft.com/azure/confidential-computing/faq)をご覧ください。
1. プレビュー期間中は CLI ベースでのデプロイ


## <a name="installing-the-cli-pre-requisites"></a>CLI の前提条件をインストールする

aks-preview 拡張機能 0.4.62 以降をインストールするには、次の Azure CLI コマンドを使用します。

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
aks-preview CLI 拡張機能を更新するには、次の Azure CLI コマンドを使用します。

```azurecli-interactive
az extension update --name aks-preview
```

Gen2VMPreview を登録します。

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
状態が "登録済み" と表示されるまでに数分かかることがあります。 "az feature list" コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
状態が登録済みとして表示されたら、"az provider register" コマンドを使用して Microsoft.ContainerService リソース プロバイダーの登録を最新の情報に更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>AKS クラスターの作成

前述の要件を満たしている AKS クラスターが既にある場合は、[既存のクラスターのセクションに進み](#existing-cluster)、新しいコンフィデンシャル コンピューティング ノード プールを追加してください。

まず、az group create コマンドを使用して、クラスターのリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *westus2* リージョンに作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

ここで、az aks create コマンドを使用して、AKS クラスターを作成します。 次の例では、サイズ `Standard_DC2s_v2` の 1 つのノードを含むクラスターを作成します。 その他、サポートされている一連の DCsv2 SKU は、[こちら](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)から選択できます。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_DC2s_v2 \
    --node-count 3 \
    --enable-addon confcom \
    --network-plugin azure \
    --vm-set-type VirtualMachineScaleSets \
    --aks-custom-headers usegen2vm=true
```
上のコマンドを実行すると、DCSv2 ノード プールを含んだ新しい AKS クラスターがプロビジョニングされ、2 つのデーモン セット ([SGX Device Plugin](confidential-nodes-aks-overview.md#sgx-plugin) および [SGX Quote Helper](confidential-nodes-aks-overview.md#sgx-quote)) が自動的にインストールされます。

az aks get-credentials コマンドを使用して、AKS クラスターの資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
DCSv2 ノード プールにノードが適切に作成され、SGX 関連のデーモン セットが実行されていることを確認します。以下のように、kubectl get pods と kubectl get nodes コマンドを使用します。

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
上に示したような出力結果が得られたら、AKS クラスターで機密アプリケーションを実行する準備は完了です。

[エンクレーブからの Hello World](#hello-world) のデプロイ セクションに移動して、エンクレーブでアプリをテストしてください。 または、以下の手順に従って別のノード プールを AKS に追加します (AKS は SGX ノード プールと非 SGX ノード プールの混在をサポートします)。

>SGX に関連したデーモン セットが DCSv2 ノード プールにインストールされていない場合は、次のコマンドを実行します。

```azurecli-interactive
az aks update --enable-addons confcom --resource-group myResourceGroup --name myAKSCluster
```

## <a name="adding-confidential-computing-node-to-existing-aks-cluster"></a>コンフィデンシャル コンピューティング ノードを既存の AKS クラスターに追加する<a id="existing-cluster"></a>

このセクションでは、「前提条件」セクションに記載した条件を満たす AKS クラスターが既に実行されていることを前提としています。

まず、既存のクラスターで、コンフィデンシャル コンピューティングに関連した AKS アドオンを有効にしましょう。

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
次に、DCSv2 ノード プールをクラスターに追加します。

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
出力結果に、AKS クラスターに新しく追加された confcompool1 が表示されます。

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
上に示したような出力結果が得られたら、AKS クラスターで機密アプリケーションを実行する準備は完了です。

## <a name="hello-world-from-isolated-enclave-application"></a>隔離されたエンクレーブ アプリケーションからの Hello World<a id="hello-world"></a>
*hello-world-enclave.yaml* という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 この Open Enclave ベースのサンプル アプリケーション コードは、[Open Enclave プロジェクト](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)でご覧いただけます。

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



