---
title: Azure CLI を使用して AKS クラスター上に Windows Server コンテナーを作成する
description: 迅速に Kubernetes クラスターを作成し、Azure CLI を使用して Azure Kubernetes Service (AKS) で Windows Server コンテナーにアプリケーションをデプロイする方法について説明します。
services: container-service
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: 284114ef1f9c8759eaec061b32282154fe72859c
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235309"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Azure CLI を使用して Azure Kubernetes Service (AKS) クラスター上に Windows Server コンテナーを作成する

Azure Kubernetes Service (AKS) は、クラスターをすばやくデプロイおよび管理することができる、マネージド Kubernetes サービスです。 この記事では、Azure CLI を使用して AKS クラスターをデプロイします。 また、Windows Server コンテナー内の ASP.NET サンプル アプリケーションをクラスターにデプロイします。

![ASP.NET サンプル アプリケーションを参照している画像](media/windows-container/asp-net-sample-app.png)

この記事では、Kubernetes の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>制限事項

複数のノード プールをサポートする AKS クラスターを作成および管理する場合には、次の制限があります。

* 最初のノード プールは削除できません。

次の追加の制限事項は Windows Server ノード プールに該当します。

* AKS クラスターでは、最大で 10 のノード プールを作成できます。
* AKS クラスターでは、最大で 100 のノードを各ノード プールで作成できます。
* Windows Server ノード プール名は 6 文字に制限されています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループは、Azure リソースが展開され管理される論理グループです。 リソース グループを作成する際は、場所を指定するよう求められます。 この場所は、リソース グループのメタデータが格納される場所です。リソースの作成時に別のリージョンを指定しない場合は、Azure でリソースが実行される場所でもあります。 [az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

> [!NOTE]
> この記事では、このチュートリアルのコマンドに対して Bash 構文を使用します。
> Azure Cloud Shell を使用する場合、[Cloud Shell] ウィンドウの左上のドロップダウンが **[Bash]** に設定されていることを確認してください。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

次の出力例では、正常に作成されたリソース グループが示されています。

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

Windows Server コンテナー用のノード プールをサポートする AKS クラスターを実行するには、[Azure CNI][azure-cni-about] の (高度な) ネットワーク プラグインを使用するネットワーク ポリシーを、ご利用のクラスターで使用する必要があります。 必要なサブネット範囲とネットワークに関する考慮事項を計画するのに役立つ詳細情報については、[Azure CNI ネットワークの構成][use-advanced-networking]に関するページを参照してください。 *myAKSCluster* という名前の AKS クラスターを作成するには、[az aks create][az-aks-create] コマンドを使用します。 このコマンドでは、必要なネットワーク リソースが存在しない場合、それらが作成されます。

* クラスターは 2 つのノードで構成されています。
* クラスター上のすべての Windows Server ノードの管理者資格情報は、`--windows-admin-password` パラメーターと `--windows-admin-username` パラメーターによって設定されます。これは [Windows Server のパスワード要件][windows-server-password]を満たしている必要があります。 *windows-admin-password* パラメーターを指定しない場合は、値を指定するように求められます。
* ノード プールは `VirtualMachineScaleSets` を使用します。

> [!NOTE]
> クラスターが確実に動作するようにするには、既定のノード プールで少なくとも 2 つのノードを実行する必要があります。

クラスターの Windows Server ノードの管理者資格情報として使用するユーザー名を作成します。 次のコマンドを実行すると、ユーザー名の入力が求められ、後のコマンドで使用できるように WINDOWS_USERNAME と設定されます (この記事に示すコマンドは BASH シェルで入力されます)。

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server nodes on your cluster: " && read WINDOWS_USERNAME
```

`--windows-admin-username` パラメーターを指定して、クラスターを作成します。 次のコマンド例では、前のコマンドで設定した *WINDOWS_USERNAME* の値を使用してクラスターを作成します。 または、*WINDOWS_USERNAME* を使用する代わりに、パラメーターに直接別のユーザー名を指定することもできます。 次のコマンドを実行すると、クラスターの Windows Server ノード向けに、管理者資格情報のパスワードを作成するように求めるメッセージも表示されます。 または、*windows-admin-password* パラメーターを使用して、独自の値を指定することもできます。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.7 \
    --network-plugin azure
```

> [!NOTE]
> パスワード検証エラーが表示された場合は、設定したパスワードが [Windows Server のパスワード要件][windows-server-password]を満たしていることを確認します。 パスワードが要件を満たしている場合は、別のリージョンでリソース グループの作成を試みます。 その後、新しいリソース グループを使用してクラスターを作成してください。
>
> `--vm-set-type VirtualMachineScaleSets` と `--network-plugin azure` の設定時に管理者のユーザー名とパスワードを指定しない場合、ユーザー名は *azureuser* に設定され、パスワードはランダムな値に設定されます。
> 
> 管理者のユーザー名は変更できませんが、`az aks update` を使用して AKS クラスターが Windows Server ノードに使用する管理者パスワードを変更できます。 詳細については、「[Windows Server ノード プールに関する FAQ][win-faq-change-admin-creds]」を参照してください。

数分後、コマンドが完了し、クラスターに関する情報が JSON 形式で返されます。 場合によっては、クラスターのプロビジョニングに数分以上かかることがあります。 このような場合は、最大 10 分と考えてください。

## <a name="add-a-windows-server-node-pool"></a>Windows Server ノード プールを追加する

既定では、Linux コンテナーを実行できるノード プールを使用して、AKS クラスターが作成されます。 Windows Server コンテナーを Linux ノード プールと共に実行できるノード プールをさらに追加するには、`az aks nodepool add` コマンドを使用します。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

上記のコマンドでは、*npwin* という名前の新しいノード プールが作成され、それが *myAKSCluster* に追加されます。 上記のコマンドではまた、`az aks create` の実行時に作成された既定の VNET 内の既定のサブネットが使用されます。

## <a name="optional-using-containerd-with-windows-server-node-pools-preview"></a>省略可能: Windows Server ノード プール で `containerd` を使用する (プレビュー)

Kubernetes バージョン1.20 以降では、Windows Server 2019 ノード プールのコンテナー ランタイムとして `containerd` を指定できます。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

*aks-preview* Azure CLI 拡張機能バージョン 0.5.24 以降が必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!IMPORTANT]
> Windows Server 2019 ノードプールで `containerd` を使用する場合:
> - コントロール プレーンと Windows Server 2019 の両方のノードプールで、Kubernetes バージョン 1.20 以上を使用する必要があります。
> - Windows Server のコンテナーを実行するためのノード プールを作成または更新する場合、*node-vm-size* の既定値は *Standard_D2s_v3* です。これは、Kubernetes 1.20 までの Windows server 2019 ノードプールの最小推奨サイズです。 `containerd` を使用した Windows Server 2019 ノードプールの最小推奨サイズは *Standard_D4s_v3* です。 *node-vm-size* パラメーターを設定する場合は、[制限された VM サイズ][restricted-vm-sizes]の一覧を確認してください。
> - ワークロードが正しくスケジュールされていることを保証するには、`containerd` を実行している Windows Server 2019 ノードプールで [テイントまたはラベル][aks-taints] を使用し、容認またはノードのセレクターをデプロイで使用することを強くお勧めします。

次の例に示すように [az feature register][az-feature-register] コマンドを使用して、`UseCustomizedWindowsContainerRuntime` 機能フラグを登録します。

```azurecli
az feature register --namespace "Microsoft.ContainerService" --name "UseCustomizedWindowsContainerRuntime"
```

登録状態を確認するには、[az feature list][az-feature-list] コマンドを使用します。

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedWindowsContainerRuntime')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、Microsoft.ContainerService リソース プロバイダーの登録を更新します。

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="add-a-windows-server-node-pool-with-containerd-preview"></a>`containerd`(プレビュー) を使用して Windows Server ノードプールを追加する

Windows Server コンテナーを `containerd` ランタイムで実行できるノード プールをさらに追加するには、`az aks nodepool add` コマンドを使用します。

> [!NOTE]
> *WindowsContainerRuntime=containerd* カスタムヘッダーを指定しない場合、ノード プールは Docker をコンテナー ランタイムとして使用します。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwcd \
    --node-vm-size Standard_D4s_v3 \
    --kubernetes-version 1.20.5 \
    --aks-custom-headers WindowsContainerRuntime=containerd \
    --node-count 1
```

上のコマンドは、*npwcd* という名前のランタイムとして `containerd` を使用して新しい Windows Server ノードプールを作成し、それを *myAKSCluster* に追加します。 上記のコマンドではまた、`az aks create` の実行時に作成された既定の VNET 内の既定のサブネットが使用されます。

### <a name="upgrade-an-existing-windows-server-node-pool-to-containerd-preview"></a>既存の Windows Server ノード プールを `containerd` にアップグレードする (プレビュー)

`az aks nodepool upgrade` コマンドを使用して、特定のノード プールを Docker から `containerd` にアップグレードします。

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name npwd \
    --kubernetes-version 1.20.7 \
    --aks-custom-headers WindowsContainerRuntime=containerd
```

上記のコマンドは、*npwd* という名前のノード プールを `containerd` ランタイムにアップグレードします。

クラスターの既存のすべてのノード プールをアップグレードして、すべての Windows Server ノード プールで `containerd` ランタイムを使用するには:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.20.7 \
    --aks-custom-headers WindowsContainerRuntime=containerd
```

上記のコマンドでは、`containerd` ランタイムを使用するために、*myAKSCluster* のすべての Windows Server ノード プールをアップグレードします。

> [!NOTE]
> `containerd` ランタイムを使用するために、既存のすべての Windows Server ノード プールをアップグレードしても、新しい Windows Server ノード プールを追加すると、Docker が既定のランタイムになります。 

## <a name="connect-to-the-cluster"></a>クラスターに接続する

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使用します。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 `kubectl` をローカルにインストールするには、[az aks install-cli][az-aks-install-cli] コマンドを使用します。

```azurecli
az aks install-cli
```

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを使用します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

クラスターへの接続を確認するには、クラスター ノードの一覧を返す [kubectl get][kubectl-get] コマンドを使用します。

```console
kubectl get nodes -o wide
```

次の出力例は、クラスター内のすべてのノードを示しています。 すべてのノードの状態が *[準備完了]* であることを確認します。

```output
NAME                                STATUS   ROLES   AGE    VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   34m    v1.20.7   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   34m    v1.20.7   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwcd123456                      Ready    agent   9m6s   v1.20.7   10.240.0.97   <none>        Windows Server 2019 Datacenter   10.0.17763.1879    containerd://1.4.4+unknown
aksnpwin987654                      Ready    agent   25m    v1.20.7   10.240.0.66   <none>        Windows Server 2019 Datacenter   10.0.17763.1879    docker://19.3.14
```

> [!NOTE]
> 各ノード プールのコンテナー ランタイムは、 *コンテナー ランタイム* の下に表示されます。 *aksnpwin987654* が `docker://` で始まることに注意してください。これは、コンテナー ランタイムに Docker を使用していることを意味します。 *aksnpwcd123456* が `containerd://` で始まることに注意してください。これは、コンテナー ランタイムに `containerd` を使用していることを意味します。

## <a name="run-the-application"></a>アプリケーションの実行

Kubernetes のマニフェスト ファイルでは、どのコンテナー イメージを実行するかなど、クラスターの望ましい状態を定義します。 この記事では、Windows Server コンテナー内で ASP.NET サンプル アプリケーションを実行するために必要なすべてのオブジェクトを作成するのにマニフェストを使用します。 このマニフェストには、ASP.NET サンプル アプリケーションの [Kubernetes デプロイ][kubernetes-deployment]と、インターネットからアプリケーションにアクセスするための外部 [Kubernetes サービス][kubernetes-service]が含まれています。

ASP.NET サンプル アプリケーションは、[.NET Framework のサンプル][dotnet-samples]の一部として提供され、Windows Server コンテナー内で実行されます。 AKS では、Windows Server コンテナーは *Windows Server 2019* 以降のイメージをベースにしている必要があります。 Kubernetes マニフェスト ファイルではまた、[ノード セレクター][node-selector]を定義する必要があり、この定義では、Windows Server コンテナーを実行できるノード上でご利用の ASP.NET サンプル アプリケーションのポッドを実行するように AKS クラスターに指示します。

`sample.yaml` という名前のファイルを作成し、以下の YAML 定義をコピーします。 Azure Cloud Shell を使用する場合は、仮想システムまたは物理システムで作業するときと同じように、`vi` または `nano` を使用してこのファイルを作成できます。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

[kubectl apply][kubectl-apply] コマンドを使用してアプリケーションをデプロイし、ご利用の YAML マニフェストの名前を指定します。

```console
kubectl apply -f sample.yaml
```

次の出力例は、正常に作成されたデプロイおよびサービスを示しています。

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、Kubernetes サービスによってアプリケーション フロント エンドがインターネットに公開されます。 このプロセスが完了するまでに数分かかることがあります。 場合によっては、サービスのプロビジョニングに数分以上かかることがあります。 このような場合は、最大 10 分と考えてください。

進行状況を監視するには、[kubectl get service][kubectl-get] コマンドを `--watch` 引数と一緒に使用します。

```console
kubectl get service sample --watch
```

最初に、"*サンプル*" サービスの *EXTERNAL-IP* が "*保留中*" として表示されます。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* アドレスが "*保留中*" から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。 次の出力例は、サービスに割り当てられている有効なパブリック IP アドレスを示しています。

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

サンプル アプリが動作していることを確認するには、Web ブラウザーを開いてサービスの外部 IP アドレスにアクセスします。

![ASP.NET サンプル アプリケーションを参照している画像](media/windows-container/asp-net-sample-app.png)

> [!Note]
> ページを読み込もうとしたときに接続タイムアウトが発生する場合は、次のコマンド [kubectl get pods --watch] を使用してサンプル アプリの準備ができていることを確認することをお勧めします。 外部 IP アドレスが使用可能になるまでは、Windows コンテナーが起動しない場合があります。

## <a name="delete-cluster"></a>クラスターを削除する

クラスターが必要なくなったら、[az group delete][az-group-delete] コマンドを使って、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除してください。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。 マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

## <a name="next-steps"></a>次のステップ

この記事では、Kubernetes クラスターをデプロイし、そこに、Windows Server コンテナー内の ASP.NET サンプル アプリケーションをデプロイしました。 先ほど作成したクラスターの [Kubernetes Web ダッシュボードにアクセス][kubernetes-dashboard]します。

AKS の詳細を参照し、デプロイの例の完全なコードを確認するには、Kubernetes クラスター チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [AKS チュートリアル][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[aks-taints]:  use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[win-faq-change-admin-creds]: windows-faq.md#how-do-i-change-the-administrator-password-for-windows-server-nodes-on-my-cluster
