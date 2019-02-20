---
title: Azure Kubernetes Service (AKS) のネットワーク ポリシーによるポッドの保護
description: Azure Kubernetes Service (AKS) の Kubernetes ネットワーク ポリシーを使用して、ポッドで送受信されるトラフィックを保護する方法について説明します
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: ade5a39273aa807f6c69f76342a0f715c7a96309
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232176"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護

Kubernetes で最新のマイクロサービス ベースのアプリケーションを実行するときは、どのコンポーネントが互いに通信できるかを制御したいことがよくあります。 最小特権の原則は、AKS クラスターのポッド間におけるトラフィック フローに対して適用する必要があります。 たとえば、バックエンド アプリケーションへの直接のトラフィックをブロックしたい場合があります。 Kubernetes では、*ネットワーク ポリシー*機能を使用して、クラスター内のポッド間のイングレス トラフィックとエグレス トラフィックのルールを定義できます。

この記事では、AKS のポッド間のトラフィック フローを制御するためのネットワーク ポリシーの使用方法について説明します。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.56 以降がインストールされて構成されている必要があります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="overview-of-network-policy"></a>ネットワーク ポリシーの概要

既定では、AKS クラスター内のすべてのポッドは制限なしにトラフィックを送受信できます。 セキュリティを向上させるために、トラフィック フローを制御するルールを定義できます。 たとえば、バックエンド アプリケーションが、必要なフロントエンド サービスにしか公開されない場合や、データベース コンポーネントが、そのコンポーネントに接続するアプリケーション層からしかアクセスできない場合がよくあります。

ネットワーク ポリシーは、ポッド間のトラフィック フローを制御できる Kubernetes リソースです。 割り当てられたラベル、名前空間、トラフィック ポートなどの設定に基づいて、トラフィックを許可するか拒否するかを選択できます。 ネットワーク ポリシーは YAML マニフェストとして定義され、デプロイやサービスも作成するより広いマニフェストの一部として含めることができます。

ネットワーク ポリシーの動作を確認するために、トラフィック フローを定義するポリシーを次のように作成して展開します。

* ポッドへのトラフィックをすべて拒否します。
* ポッド ラベルに基づいてトラフィックを許可します。
* 名前空間に基づいてトラフィックを許可します。

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS クラスターを作成してネットワーク ポリシーを有効にする

ネットワーク ポリシーは、クラスターが作成されたときにのみ有効にできます。 既存の AKS クラスターでネットワーク ポリシーを有効にすることはできません。 ネットワーク ポリシーを使用して AKS を作成するには、まずサブスクリプションで機能フラグを有効にします。 *EnableNetworkPolicy* 機能フラグを登録するには、次の例に示すように [az feature register][az-feature-register] コマンドを使用します。

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録状態を確認するには、[az feature list][az-feature-list] コマンドを使用します。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

AKS クラスターでネットワーク ポリシーを使用するには、[Azure CNI プラグイン][azure-cni]を使用して独自の仮想ネットワークとサブネットを定義する必要があります。 必要なサブネット範囲を計画する方法の詳細については、[高度なネットワークの構成][use-advanced-networking]に関するページを参照してください。 次のサンプル スクリプトでは、

* 仮想ネットワークとサブネットを作成します。
* AKS クラスターで使用するための Azure Active Directory (AD) サービス プリンシパルを作成します。
* 仮想ネットワーク上の AKS クラスター サービス プリンシパルに*共同作成者*のアクセス許可を割り当てます。
* 定義した仮想ネットワークに AKS クラスターを作成し、ネットワーク ポリシーを有効にします。

独自の安全な *SP_PASSWORD* を指定してください。 必要に応じて、*RESOURCE_GROUP_NAME* 変数と *CLUSTER_NAME* 変数の値を置き換えます。

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
read SP_ID <<< $(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

クラスターの作成には数分かかります。 終了したら、[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、Kubernetes クラスターに接続するように `kubectl` を構成します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>ポッドへのすべての受信トラフィックを拒否する

特定のネットワーク トラフィックを許可するルールを定義する前に、まずすべてのトラフィックを拒否するネットワーク ポリシーを作成します。 このポリシーは、目的のトラフィックのみをホワイトリストに載せるための出発点になります。 また、ネットワーク ポリシーの適用時にトラフィックがドロップされることを明確に確認できます。

サンプルのアプリケーション環境とトラフィック ルールに対して、サンプルのポッドを実行するために *development* という名前空間を最初に作成します。

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

次に、NGINX を実行するサンプルのバックエンド ポッドを作成します。 このバックエンド ポッドは、サンプルのバックエンド Web ベース アプリケーションをシミュレートするために使用できます。 このポッドを *development* 名前空間に作成し、ポート *80* を開いて Web トラフィックを処理します。 次のセクションでネットワーク ポリシーを使用してターゲットに指定できるように、ポッドに *app=webapp,role=backend* のラベルを付けます。

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

既定の NGINX Web ページに正しくアクセスできることをテストするために、別のポッドを作成してターミナル セッションをアタッチします。

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

シェル プロンプトが表示されたら、`wget` を使用して既定の NGINX Web ページにアクセスできることを確認します。

```console
wget -qO- http://backend
```

次のサンプル出力は、既定の NGINX Web ページが返されたことを示しています。

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

アタッチされているターミナル セッションを終了します。 テスト ポッドは自動的に削除されます。

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>ネットワーク ポリシーを作成して適用する

サンプル バックエンド ポッドの基本的な NGINX Web ページにアクセスできることを確認したので、すべてのトラフィックを拒否するネットワーク ポリシーを作成します。 `backend-policy.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 このマニフェストは *podSelector* を使用して、サンプルの NGINX ポッドなど、*app:webapp,role:backend* ラベルを持つポッドにポリシーを添付します。 *ingress* ではルールが定義されていないため、ポッドへのすべての受信トラフィックは拒否されます。

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

[kubectl apply][kubectl-apply] コマンドを使用してネットワーク ポリシーを適用し、YAML マニフェストの名前を指定します。

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>ネットワーク ポリシーをテストする

バックエンド ポッドの NGINX Web ページに再度アクセスできるかどうか確認します。 別のテスト ポッドを作成してターミナル セッションをアタッチします。

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

シェル プロンプトが表示されたら、`wget` を使用して既定の NGINX Web ページにアクセスできるかどうかを確認します。 今回は、タイムアウト値を *2* 秒に設定します。 次の例に示すように、ネットワーク ポリシーはすべての受信トラフィックをブロックするようになっているため、ページを読み込むことができません。

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

アタッチされているターミナル セッションを終了します。 テスト ポッドは自動的に削除されます。

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>ポッド ラベルに基づいて受信トラフィックを許可する

前のセクションでは、バックエンドの NGINX ポッドがスケジュールされ、すべてのトラフィックを拒否するためのネットワーク ポリシーが作成されました。 ここでは、フロントエンド ポッドを作成し、フロントエンド ポッドからのトラフィックを許可するようにネットワーク ポリシーを更新します。

ラベル *app:webapp,role:frontend* を持つポッドからのトラフィックおよび任意の名前空間のトラフィックを許可するようにネットワーク ポリシーを更新します。 前の *backend-policy.yaml* ファイルを編集して、マニフェストが次の例のようになるように *matchLabels* イングレス ルールを追加します。

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

[kubectl apply][kubectl-apply] コマンドを使用して更新済みのネットワーク ポリシーを適用し、YAML マニフェストの名前を指定します。

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

次に、*app=webapp,role=frontend* とラベル付けされたポッドをスケジュールし、ターミナル セッションをアタッチします。

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

シェル プロンプトが表示されたら、`wget` を使用して既定の NGINX Web ページにアクセスできるかどうかを確認します。

```console
wget -qO- http://backend
```

イングレス ルールでは、ラベルが *app: webapp,role: frontend* のポッドのトラフィックが許可されるので、フロントエンド ポッドからのトラフィックは許可されます。 次のサンプル出力は、既定の NGINX Web ページが返されたことを示しています。

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

アタッチされているターミナル セッションを終了します。 ポッドは自動的に削除されます。

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>ラベルが一致しないポッドをテストする

ネットワーク ポリシーは、*app: webapp,role: frontend* というラベルの付いたポッドからのトラフィックを許可しますが、その他のトラフィックはすべて拒否する必要があります。 それらのラベルのない別のポッドがバックエンドの NGINX ポッドにアクセスできないことをテストします。 別のテスト ポッドを作成してターミナル セッションをアタッチします。

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

シェル プロンプトが表示されたら、`wget` を使用して既定の NGINX Web ページにアクセスできるかどうかを確認します。 次の例に示すように、ネットワーク ポリシーは受信トラフィックをブロックするため、ページを読み込むことができません。

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

アタッチされているターミナル セッションを終了します。 テスト ポッドは自動的に削除されます。

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>定義された名前空間内からのトラフィックのみを許可する

前の例では、すべてのトラフィックを拒否するネットワーク ポリシーを作成し、特定のラベルを持つポッドからのトラフィックを許可するようにポリシーを更新しました。 もう 1 つの一般的なニーズは、トラフィックを特定の名前空間内に限定することです。 前の例が *development* 名前空間のトラフィック用である場合は、*production* などの別の名前空間からのトラフィックがポッドに到達しないようにするネットワーク ポリシーを作成したい場合があります。

まず、production 名前空間をシミュレートするための新しい名前空間を作成します。

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

*app=webapp,role=frontend* とラベル付けされた *production* 名前空間でテスト ポッドをスケジュールします。 ターミナル セッションをアタッチします。

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

シェル プロンプトが表示されたら、`wget` を使用して既定の NGINX Web ページにアクセスできることを確認します。

```console
wget -qO- http://backend.development
```

ポッドのラベルはネットワーク ポリシーで現在許可されているラベルと一致するため、トラフィックは許可されます。 ネットワーク ポリシーは名前空間を調べず、ポッド ラベルだけを調べます。 次のサンプル出力は、既定の NGINX Web ページが返されたことを示しています。

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

アタッチされているターミナル セッションを終了します。 テスト ポッドは自動的に削除されます。

```console
exit
```

### <a name="update-the-network-policy"></a>ネットワーク ポリシーを更新する

ここで、*development* 名前空間内からのトラフィックのみを許可するように、イングレス ルールの *namespaceSelector* セクションを更新します。 次の例に示すように、*backend-policy.yaml* マニフェスト ファイルを編集します。

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

より複雑な例では、*namespaceSelector* を使用してから *podSelector* を使用するなど、複数のイングレス ルールを定義できます。

[kubectl apply][kubectl-apply] コマンドを使用して更新済みのネットワーク ポリシーを適用し、YAML マニフェストの名前を指定します。

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>更新されたネットワーク ポリシーをテストする

*production* 名前空間に別のポッドをスケジュールし、ターミナル セッションをアタッチします。

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

シェル プロンプトが表示されたら、`wget` を使用してネットワーク ポリシーでトラフィックが拒否されていることを確認します。

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

テスト ポッドを終了します。

```console
exit
```

*production* 名前空間からのトラフィックが拒否されたら、テスト ポッドを *development* 名前空間にスケジュールし、ターミナル セッションをアタッチします。

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

シェル プロンプトが表示されたら、`wget` を使用してネットワーク ポリシーでトラフィックが許可されていることを確認します。

```console
wget -qO- http://backend
```

ポッドは、ネットワーク ポリシーで許可されている名前空間と一致する名前空間でスケジュールされているため、トラフィックは許可されます。 次のサンプル出力は、既定の NGINX Web ページが返されたことを示しています。

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

アタッチされているターミナル セッションを終了します。 テスト ポッドは自動的に削除されます。

```console
exit
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事では、2 つの名前空間を作成し、ネットワーク ポリシーを適用しました。 これらのリソースをクリーンアップするには、[kubectl delete][kubectl-delete] コマンドを使用して、以下のようにリソース名を指定します。

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>次の手順

ネットワーク リソースの詳細については、「[Azure Kubernetes Service (AKS) でのアプリケーションに対するネットワークの概念][concepts-network]」を参照してください。

ポリシーの使用について詳しくは、[Kubernetes のネットワーク ポリシー][kubernetes-network-policies]を参照してください。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
