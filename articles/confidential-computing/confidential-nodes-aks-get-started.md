---
title: 'クイックスタート: コンフィデンシャル コンピューティング ノードを含む AKS クラスターを Azure CLI を使用してデプロイする'
description: Azure CLI を使用して、コンフィデンシャル ノードを含む Azure Kubernetes Service (AKS) クラスターを作成し、Hello World アプリをデプロイする方法について説明します。
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 261deb0c4f5f28be51e806ab76261278709efc3b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482876"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>クイックスタート: コンフィデンシャル コンピューティング ノードを含む AKS クラスターを Azure CLI を使用してデプロイする

このクイックスタートでは、Azure CLI を使用して、コンフィデンシャル コンピューティング (DCsv2) ノードを含む Azure Kubernetes Service (AKS) クラスターをデプロイします。 その後、シンプルな Hello World アプリケーションをエンクレーブで実行します。 クラスターのプロビジョニングとコンフィデンシャル コンピューティング ノードの追加は Azure portal から行うこともできますが、このクイックスタートでは、Azure CLI に焦点を絞って説明します。

AKS は、開発者またはクラスター オペレーターが迅速にクラスターをデプロイして管理できるようにするマネージド Kubernetes サービスです。 詳細については、[AKS の概要](../aks/intro-kubernetes.md)に関するページと [AKS のコンフィデンシャル ノードの概要](confidential-nodes-aks-overview.md)に関するページを参照してください。

コンフィデンシャル コンピューティング ノードの特徴は次のとおりです。

- Linux コンテナーをサポートする Linux ワーカー ノード。
- 第 2 世代仮想マシン (VM) と Ubuntu 18.04 の VM ノード。
- Intel SGX 対応 CPU。EPC (Encrypted Page Cache Memory) を活用し、機密性が保護されたエンクレーブ内でコンテナーを実行するのに役立ちます。 詳細については、「[Azure コンフィデンシャル コンピューティングについてよく寄せられる質問](./faq.md)」を参照してください。
- コンフィデンシャル コンピューティング ノードと共にプレインストールされた Intel SGX DCAP ドライバー。 詳細については、「[Azure コンフィデンシャル コンピューティングについてよく寄せられる質問](./faq.md)」を参照してください。

> [!NOTE]
> DCsv2 VM には、より高い価格が適用され、利用可能なリージョンが限られる特殊なハードウェアが使用されます。 詳細については、[利用可能な SKU とサポートされるリージョン](virtual-machine-solutions.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

- 有効な Azure サブスクリプション Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
- デプロイ マシンに Azure CLI バージョン 2.0.64 以降がインストールされ、構成されていること。 

  バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](../container-registry/container-registry-get-started-azure-cli.md)に関するページを参照してください。
- 自分のサブスクリプションで最低 6 つの DCsv2 コアが利用できること。 

  既定では、コンフィデンシャル コンピューティングのクォータは、Azure サブスクリプションあたり 8 VM コアです。 プロビジョニングする予定のクラスターに必要なコア数が 8 を超える場合は、[こちらの手順](../azure-portal/supportability/per-vm-quota-requests.md)に従って、クォータの引き上げチケットを起票してください。

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>コンフィデンシャル コンピューティング ノードとアドオンを含む AKS クラスターを作成する

コンフィデンシャル コンピューティング アドオンが有効になっている AKS クラスターを作成し、そのクラスターにノード プールを追加して、作成した内容を確認するには、以降の手順に従います。

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>システム ノード プールを含む AKS クラスターを作成する

> [!NOTE]
> 前述の前提条件を満たす AKS クラスターが既にある場合は、[次のセクションに進み](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster)、コンフィデンシャル コンピューティング ノード プールを追加してください。

まず、[az group create][az-group-create] コマンドを使用して、このクラスター用のリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループが *westus2* リージョンに作成されます。

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

次に、[az aks create][az-aks-create] コマンドを使用して、コンフィデンシャル コンピューティング アドオンが有効になっている AKS クラスターを作成します。

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>コンフィデンシャル コンピューティング機能を備えたユーザー ノード プールを AKS クラスターに追加する 

次のコマンドを実行して、3 つのノードを含む `Standard_DC2s_v2` サイズのユーザー ノード プールを AKS クラスターに追加します。 [サポートされている DCsv2 SKU とリージョン](../virtual-machines/dcv2-series.md)の一覧から、別の SKU を選択できます。

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

このコマンドの実行後、DCsv2 の新しいノード プールが、コンフィデンシャル コンピューティング アドオン デーモンセット ([SGX デバイス プラグイン](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)) と共に表示されます。

### <a name="verify-the-node-pool-and-add-on"></a>ノード プールとアドオンを確認する

[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、AKS クラスターの資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

ノードが正しく作成され、SGX 関連のデーモンセットが DCsv2 ノード プールで実行されていることを確認するには、`kubectl get pods` コマンドを使用します。

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

出力内容が上記のコードと一致する場合は、AKS クラスターで機密性の高いアプリケーションを実行する準備ができています。

このクイックスタートの「[分離されたエンクレーブ アプリケーションから Hello World をデプロイする](#hello-world)」セクションに進んで、エンクレーブでアプリをテストすることができます。 または、次の手順に従って AKS にさらにノード プールを追加します (AKS は、SGX ノードプールと非 SGX ノードプールの混在をサポートしています。)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>コンフィデンシャル コンピューティング ノード プールを既存の AKS クラスターに追加する<a id="existing-cluster"></a>

このセクションでは、このクイックスタートで前述した前提条件を満たす AKS クラスターが既に実行されていることを前提としています。

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>既存のクラスターでコンフィデンシャル コンピューティング AKS アドオンを有効にする

次のコマンドを実行して、コンフィデンシャル コンピューティング アドオンを有効にします。

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>DCsv2 ユーザー ノード プールをクラスターに追加する

> [!NOTE]
> コンフィデンシャル コンピューティング機能を使用するには、既存の AKS クラスターに、DCsv2 VM SKU をベースとしたノード プールが少なくとも 1 つ必要です。 コンフィデンシャル コンピューティングに使用される DCs-v2 VM SKU の詳細については、[利用可能な SKU とサポートされるリージョン](virtual-machine-solutions.md)に関するページを参照してください。

次のコマンドを実行して、ノード プールを作成します。

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

*confcompool1* という名前の新しいノード プールが作成されたことを確認します。

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>デーモンセットがコンフィデンシャル ノード プールで実行されていることを確認する

既存の AKS クラスターにサインインして、次の確認を実行します。

```console
kubectl get nodes
```

出力内容に、AKS クラスターに新しく追加された *confcompool1* プールが表示されます。 他のデーモンセットも表示される場合があります。

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

出力内容が上記のコードと一致する場合は、AKS クラスターで機密性の高いアプリケーションを実行する準備ができています。 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>分離されたエンクレーブ アプリケーションから Hello World をデプロイする<a id="hello-world"></a>
これで、テスト アプリケーションをデプロイする準備ができました。 

*hello-world-enclave.yaml* という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 このサンプル アプリケーション コードは、[Open Enclave プロジェクト](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)にあります。 このデプロイでは、*confcom* アドオンがデプロイ済みであることを前提としています。

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
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

ここで、`kubectl apply` コマンドを使用して、セキュア エンクレーブで開くサンプル ジョブを作成します (次の出力例を参照)。

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

次のコマンドを実行すると、このワークロードによって高信頼実行環境 (エンクレーブ) が正常に作成されたことを確認できます。

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成したコンフィデンシャル コンピューティング ノード プールを削除するには、次のコマンドを使用します。 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

AKS クラスターを削除するには、次のコマンドを使用します。 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>次の手順

* [GitHub のコンフィデンシャル コンテナー サンプル](https://github.com/Azure-Samples/confidential-container-samples)を使用して、Python や Node などのアプリケーションをコンフィデンシャル コンテナーから実行します。

* [GitHub のエンクレーブ対応 Azure コンテナー サンプル](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)を使用して、エンクレーブ対応アプリケーションを実行します。

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
