---
title: Azure Kubernetes Service シークレットにシークレット ストア CSI ドライバーを使用する
description: シークレット ストア CSI ドライバーを使用してシークレット ストアを Azure Kubernetes Service (AKS) と統合する方法について説明します。
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: caab5417760c75e46b78241edd65d5c0124d5917
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234093"
---
# <a name="use-the-secrets-store-csi-driver-for-kubernetes-in-an-azure-kubernetes-service-aks-cluster-preview"></a>Azure Kubernetes Service (AKS) クラスターで Kubernetes にシークレット ストア CSI ドライバーを使用する (プレビュー)

Kubernetes 用のシークレット ストア CSI ドライバーを使用すると、[CSI ボリューム][kube-csi]経由で Azure Key Vault をシークレット ストアとして Kubernetes クラスターと統合できます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- 開始する前に、最新バージョンの [Azure CLI](/cli/azure/install-azure-cli-windows) と *aks-preview* 拡張機能をインストールしてください。

### <a name="supported-kubernetes-versions"></a>サポートされている Kubernetes のバージョン

この機能に推奨される Kubernetes の最小バージョンは 1.18 です。 

## <a name="features"></a>フィーチャー

- CSI ボリュームを使用して、ポッドにシークレット、キー、および証明書 (またはそのいずれか) をマウントします
- CSI Inline ボリュームがサポートされています (Kubernetes バージョン v1.15 以降)
- 複数のシークレット ストア オブジェクトを 1 つのボリュームとしてマウントすることがサポートされています
- SecretProviderClass CRD によるポッド移植性がサポートされています
- Windows コンテナーがサポートされています
- Kubernetes シークレットと同期します (シークレット ストア CSI ドライバー v0.0.10 以降)
- マウントされたコンテンツと同期された Kubernetes シークレットの自動ローテーションがサポートされています (シークレット ストア CSI ドライバー v0.0.15 以降)

## <a name="register-the-aks-azurekeyvaultsecretsprovider-preview-feature"></a>`AKS-AzureKeyVaultSecretsProvider` プレビュー機能を登録する

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

シークレット ストア CSI ドライバーを使用できる AKS クラスターを作成するには、サブスクリプションで `AKS-AzureKeyVaultSecretsProvider` 機能フラグを有効にする必要があります。

`AKS-AzureKeyVaultSecretsProvider` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-AzureKeyVaultSecretsProvider"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzureKeyVaultSecretsProvider')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-the-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

*aks-preview* Azure CLI 拡張機能バージョン 0.5.9 以降も必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 この拡張機能が既にインストールされている場合は、[az extension update][az-extension-update] コマンドを使用して、最新の使用可能なバージョンに更新します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-secrets-store-csi-driver-support"></a>シークレット ストア CSI ドライバー サポート付きの AKS クラスターを作成する

最初に、Azure リソース グループを作成します。

```azurecli-interactive
az group create -n myResourceGroup -l eastus2
```

シークレット ストア CSI ドライバー機能を備えた AKS クラスターを作成するには、アドオン `azure-keyvault-secrets-provider` を指定して [az aks create][az-aks-create] コマンドを使用します。

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-managed-identity
```

ユーザー割り当てマネージド ID は、`azurekeyvaultsecretsprovider-*` という名前の Azure リソースにアクセスするために、アドオンによって作成されます。 この ID を使用して、シークレットが格納される Azure Key Vault に接続できます。 出力内の ID の `clientId` をメモします。

```json
...,
 "addonProfiles": {
    "azureKeyvaultSecretsProvider": {
      ...,
      "identity": {
        "clientId": "<client-id>",
        ...
      }
    }
```

## <a name="upgrade-an-existing-aks-cluster-with-secrets-store-csi-driver-support"></a>シークレット ストア CSI ドライバー サポート付きの既存の AKS クラスターをアップグレードする

シークレット ストア CSI ドライバー機能を備えた既存の AKS クラスターをアップグレードするには、アドオン `azure-keyvault-secrets-provider` を指定して [az aks enable-addons][az-aks-enable-addons] コマンドを使用します。

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

前述のように、アドオンによって、ユーザー割り当てマネージド ID が作成され、この ID を使用して、Azure Key Vault を認証できます。

## <a name="verify-secrets-store-csi-driver-installation"></a>シークレット ストア CSI ドライバーのインストールの確認

上記では、シークレット ストア CSI ドライバーと Azure Key Vault プロバイダーがノードにインストールされます。 kube-system 名前空間の secrets-store-csi-driver および secrets-store-provider-azure ラベルを持つすべてのポッドを一覧表示して、完了を確認し、出力が以下のようになることを確認します。

```bash
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'

NAMESPACE     NAME                                     READY   STATUS    RESTARTS   AGE
kube-system   aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
kube-system   aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
kube-system   aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
kube-system   aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
kube-system   aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
kube-system   aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```

## <a name="enabling-and-disabling-autorotation"></a>自動ローテーションの有効化と無効化

> [!NOTE]
> 有効にすると、シークレット ストア CSI ドライバーは、2 分ごとに変更をポーリングして、ポッド マウントと、SecretProviderClass の secretObjects に定義されている Kubernetes シークレットを更新します。

シークレットの自動ローテーションを有効にするには、クラスターの作成時に `enable-secret-rotation` フラグを使用します。

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

または、アドオンが有効になっている既存のクラスターを更新します。

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

無効にするには、フラグ `disable-secret-rotation` を使用します。

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

## <a name="create-or-use-an-existing-azure-key-vault"></a>Azure Key Vault を作成するか既存のものを使用する

AKS クラスターに加えて、シークレット コンテンツを含む Azure Key Vault リソースが必要です。 Key Vault の名前はグローバルに一意である必要があることに注意してください。

```azurecli
az keyvault create -n <keyvault-name> -g myResourceGroup -l eastus2
```

Azure Key Vault では、キー、シークレット、証明書を格納できます。 この例では、`ExampleSecret` というプレーン テキスト シークレットを設定します。

```azurecli
az keyvault secret set --vault-name <keyvault-name> -n ExampleSecret --value MyAKSExampleSecret
```

次のセクションで使用するために、次のプロパティをメモしておきます。

- Key Vault 内のシークレット オブジェクトの名前
- オブジェクトの種類 (シークレット、キー、または証明書)
- Azure Key Vault の名前
- サブスクリプションが属している Azure テナント ID

## <a name="provide-identity-to-access-azure-key-vault"></a>Azure Key Vault にアクセスするために ID を提供する

前の手順の値を使用して、アクセス許可を設定し、アドオンによって作成されたマネージド ID が keyvault オブジェクトにアクセスできるようにします。

```azurecli
az keyvault set-policy -n <keyvault-name> --<object-type>-permissions get --spn <client-id>
```

## <a name="create-and-apply-your-own-secretproviderclass-object"></a>独自の SecretProviderClass オブジェクトを作成して適用する

AKS クラスターに対してシークレット ストア CSI ドライバーを使用および構成するには、SecretProviderClass カスタム リソースを作成します。 `objects` 配列が、Azure Key Vault インスタンスに格納されているオブジェクトと一致することを確認します。

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: <keyvault-name>
spec:
  provider: azure
  parameters:
    keyvaultName: "<keyvault-name>"       # The name of the Azure Key Vault
    useVMManagedIdentity: "true"         
    userAssignedIdentityID: "<client-id>" # The clientId of the addon-created managed identity
    cloudName: ""                         # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud 
    objects:  |
      array:
        - |
          objectName: <secret-name>       # In this example, 'ExampleSecret' 
          objectAlias: <secret-alias>     # [OPTIONAL] specify the filename of the object when written to disk - defaults to objectName if not provided
          objectType: secret              # Object types: secret, key or cert
          objectVersion: ""               # [OPTIONAL] object versions, default to latest if empty
    tenantId: "<tenant-id>"               # the tenant ID containing the Azure Key Vault instance
```

詳細については、「[独自の SecretProviderClass オブジェクトを作成する][sample-secret-provider-class]」を参照してください。 上記でメモした値を必ず使用するようにしてください。

### <a name="apply-the-secretproviderclass-to-your-cluster"></a>クラスターに SecretProviderClass を適用する

次に、作成した SecretProviderClass をデプロイします。 次に例を示します。

```bash
kubectl apply -f ./new-secretproviderclass.yaml
```

## <a name="update-and-apply-your-clusters-deployment-yaml"></a>クラスターのデプロイ YAML を更新して適用する

新しいカスタム リソースが確実にクラスターで使用されているようにするには、デプロイ YAML を更新します。 次に例を示します。

```yml
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
spec:
  containers:
  - name: busybox
    image: k8s.gcr.io/e2e-test-images/busybox:1.29
    command:
      - "/bin/sleep"
      - "10000"
    volumeMounts:
    - name: secrets-store-inline
      mountPath: "/mnt/secrets-store"
      readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "<keyvault-name>"
```

更新されたデプロイをクラスターに適用します。

```bash
kubectl apply -f ./my-deployment.yaml
```

## <a name="validate-the-secrets"></a>シークレットを検証する

ポッドが起動すると、デプロイ YAML で指定されたボリューム パスにマウントされたコンテンツが使用できるようになります。

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'ExampleSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/ExampleSecret
```

## <a name="disable-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>既存の AKS クラスターでシークレット ストア CSI ドライバーを無効にする

既存のクラスターでシークレット ストア CSI ドライバー機能を無効にするには、`azure-keyvault-secrets-provider` フラグを指定して [az aks disable-addons][az-aks-disable-addons] コマンドを使用します。

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

## <a name="next-steps"></a>次の手順
<!-- Add a context sentence for the following links -->
AKS クラスターで CSI シークレット ストア ドライバーを使用する方法を学習したら、次のリソースを参照してください。

- [AKS で Azure ディスクおよび Azure Files 用の CSI ドライバーを有効にする][csi-storage-drivers]

<!-- Links -->
<!-- Internal -->
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-aks-disable-addons]: /cli/azure/aks#az_aks_disable_addons
[key-vault-provider]: ../key-vault/general/key-vault-integrate-kubernetes.md
[csi-storage-drivers]: ./csi-storage-drivers.md
[create-key-vault]: ../key-vault/general/quick-create-cli.md
[set-secret-key-vault]: ../key-vault/secrets/quick-create-portal.md
[aks-managed-identity]: ./use-managed-identity.md

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
