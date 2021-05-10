---
title: Azure Kubernetes Service で Azure Active Directory ポッドマネージド ID を使用する (プレビュー)
description: Azure Kubernetes Service (AKS) で AAD ポッドマネージド ID を使用する方法について説明します
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: f090f5e11688f35ce090bb07ec0d23530bf9d90e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777853"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Azure Kubernetes Service で Azure Active Directory ポッドマネージド ID を使用する (プレビュー)

Azure Active Directory ポッドマネージド ID では、Kubernetes プリミティブを使用して [Azure リソース用マネージド ID][az-managed-identities] と Azure Active Directory (AAD) の ID をポッドに関連付けます。 管理者は、ID とバインドを Kubernetes プリミティブとして作成し、ID プロバイダーとして AAD に依存する Azure リソースにポッドがアクセスできるようにします。

> [!NOTE]
> AADPODIDENTITY が既にインストールされている場合、既存のインストールを削除する必要があります。 この機能を有効にすると、MIC コンポーネントは不要になります。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

* Azure CLI バージョン 2.20.0 以降
* `azure-preview` 拡張機能バージョン 0.5.5 以降

### <a name="limitations"></a>制限事項

* クラスターに対して最大 200 のポッド ID が許可されます。
* クラスターに対して最大 200 のポッド ID の例外が許可されます。
* ポッドマネージド ID は、Linux ノード プールでのみ使用できます。

### <a name="register-the-enablepodidentitypreview"></a>`EnablePodIdentityPreview` を登録する

`EnablePodIdentityPreview` 機能を登録します。

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI をインストールする

*aks-preview* Azure CLI 拡張機能バージョン 0.4.64 以降も必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-azure-cni"></a>Azure CNI が有効になっている AKS クラスターを作成する

> [!NOTE]
> これは、推奨される既定の構成です。

Azure CNI とポッドマネージド ID が有効になっている AKS クラスターを作成します。 次のコマンドを実行すると、[az group create][az-group-create] を使用して *myResourceGroup* という名前のリソース グループが作成され、[az aks create][az-aks-create] コマンドを使用して *myResourceGroup* リソース グループに *myAKSCluster* という名前の AKS クラスターが作成されます。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

[az aks get-credentials][az-aks-get-credentials] を使用して、AKS クラスターにサインインします。 また、このコマンドにより、ご使用の開発用コンピューターに `kubectl` クライアント証明書がダウンロードされて構成されます。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Azure CNI で既存の AKS クラスターを更新する

ポッドマネージド ID が含まれるように、Azure CNI で既存の AKS クラスターを更新します。

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --network-plugin azure
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Azure Active Directory ポッドマネージド ID で Kubernet ネットワーク プラグインを使用する 

> [!IMPORTANT]
> Kubernet を使用してクラスターで aad-pod-identity を実行すると、セキュリティに影響するため、推奨される構成ではありません。 Kubernet を使用してクラスターで aad-pod-identity を有効にする前に、対応策の手順に従い、ポリシーを構成してください。

## <a name="mitigation"></a>対応策

クラスター レベルで脆弱性を軽減するために、OpenPolicyAgent 受付コントローラーをゲートキーパー検証 Webhook と共に使用できます。 クラスターに既にゲートキーパーがインストールされている場合は、K8sPSPCapabilities タイプの ConstraintTemplate を追加します。

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
NET_RAW 機能を使用してポッドの生成を制限するテンプレートを追加します。

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Kubernet ネットワーク プラグインを使用して AKS クラスターを作成する

Kubenet ネットワーク プラグインとポッドマネージド ID が有効になっている AKS クラスターを作成します。

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Kubernet ネットワーク プラグインを使用して既存の AKS クラスターを更新する

ポッドマネージド ID が含まれるように、Kubnet ネットワーク プラグインを使用して、既存の AKS クラスターを更新します。

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>ID の作成

[az identity create][az-identity-create] を使用して ID を作成し、*IDENTITY_CLIENT_ID* および *IDENTITY_RESOURCE_ID* の変数を設定します。

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>マネージド ID にアクセス許可を割り当てる

*IDENTITY_CLIENT_ID* マネージド ID には、AKS クラスターの仮想マシン スケール セットを含むリソース グループに対する閲覧者のアクセス許可が付与されている必要があります。

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>ポッド ID を作成する

`az aks pod-identity add` を使用して、クラスターのポッド ID を作成します。

> [!IMPORTANT]
> ID とロールのバインドを作成するには、サブスクリプションに対する適切なアクセス許可 (`Owner` など) が必要です。

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> AKS クラスターでポッドマネージド ID を有効にすると、*aks-addon-exception* という名前の AzurePodIdentityException が *kube-system* 名前空間に追加されます。 AzurePodIdentityException を使用すると、Node Managed Identity (NMI) サーバーによって傍受されることなく、特定のラベルを持つポッドから Azure Instance Metadata Service (IMDS) エンドポイントにアクセスできます。 *aks-addon-exception* を使用すると、AAD ポッドマネージド ID などの AKS ファーストパーティのアドオンを、AzurePodIdentityException を手動で設定しなくても動作させることができます。 必要に応じて、`az aks pod-identity exception add`、`az aks pod-identity exception delete`、`az aks pod-identity exception update`、または `kubectl` を使用して、AzurePodIdentityException を追加、削除、更新することができます。

## <a name="run-a-sample-application"></a>サンプル アプリケーションを実行する

ポッドで AAD ポッドマネージド ID を使用すには、ポッドに *AzureIdentityBinding* のセレクターと一致する値を持つ *aadpodidbinding* ラベルが必要です。 AAD ポッドマネージド ID を使用してサンプル アプリケーションを実行するには、次の内容を含む `demo.yaml` ファイルを作成します。 *POD_IDENTITY_NAME*、*IDENTITY_CLIENT_ID*、および *IDENTITY_RESOURCE_GROUP* を、前の手順の値に置き換えます。 *SUBSCRIPTION_ID* を実際のサブスクリプション ID に置き換えます。

> [!NOTE]
> 前の手順では、*POD_IDENTITY_NAME*、*IDENTITY_CLIENT_ID*、および *IDENTITY_RESOURCE_GROUP* の変数を作成しました。 `echo` などのコマンドを使用して、変数に設定した値 (`echo $IDENTITY_NAME` など) を表示できます。

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

ポッド定義には、前の手順で実行した `az aks pod-identity add` のポッド ID の名前と一致する値を持つ *aadpodidbinding* ラベルが含まれていることに注目してください。

`kubectl apply` を使用して、ポッド ID と同じ名前空間に `demo.yaml` をデプロイします。

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

`kubectl logs` を使用して、サンプル アプリケーションが正常に実行されていることを確認します。

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

ログに、トークンが正常に取得されたこと、および *GET* 操作が正常に完了したことが表示されていることを確認します。
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>クリーンアップ

クラスターから AAD ポッドマネージド ID を削除するには、クラスターからサンプル アプリケーションとポッド ID を削除します。 その後、ID を削除します。

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>次の手順

マネージド ID の詳細については、[Azure リソースのマネージド ID][az-managed-identities] に関するページを参照してください。

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
