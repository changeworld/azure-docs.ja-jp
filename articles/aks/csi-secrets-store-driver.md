---
title: Azure Kubernetes Service シークレットにシークレット ストア CSI ドライバーを使用する
description: シークレット ストア CSI ドライバーを使用してシークレット ストアを Azure Kubernetes Service (AKS) と統合する方法について説明します。
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 535c79dba122fd22cc09b4a74b04b846d55538f9
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331270"
---
# <a name="use-the-secrets-store-csi-driver-for-kubernetes-in-an-azure-kubernetes-service-aks-cluster-preview"></a>Azure Kubernetes Service (AKS) クラスターで Kubernetes にシークレット ストア CSI ドライバーを使用する (プレビュー)

Kubernetes 用のシークレット ストア CSI ドライバーを使用すると、[CSI ボリューム][kube-csi]経由で Azure Key Vault をシークレット ストアとして Kubernetes クラスターと統合できます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- 開始する前に、最新バージョンの [Azure CLI](/cli/azure/install-azure-cli-windows) と *aks-preview* 拡張機能をインストールしてください。

## <a name="features"></a>フィーチャー

- CSI ボリュームを使用して、ポッドにシークレット、キー、および証明書 (またはそのいずれか) をマウントします
- CSI Inline ボリュームがサポートされています (Kubernetes バージョン v1.15 以降)
- 複数のシークレット ストア オブジェクトを 1 つのボリュームとしてマウントすることがサポートされています
- SecretProviderClass CRD によるポッド移植性がサポートされています
- Windows コンテナーがサポートされています (Kubernetes バージョン v1.18 以降)
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

*aks-preview* Azure CLI 拡張機能バージョン 0.5.10 以降も必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 この拡張機能が既にインストールされている場合は、[az extension update][az-extension-update] コマンドを使用して、最新の使用可能なバージョンに更新します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-secrets-store-csi-driver-support"></a>シークレット ストア CSI ドライバー サポート付きの AKS クラスターを作成する

> [!NOTE]
> ユーザー割り当てまたはシステム割り当てマネージド ID を使用してクラスターへのアクセスを提供する予定がある場合は、`enable-managed-identity` フラグを使用してクラスターで Azure Active Directory を有効にします。 詳細については、「[Azure Kubernetes Service でマネージド ID を使用する][aks-managed-identity]」を参照してください。

シークレット ストア CSI ドライバー機能を備えた AKS クラスターを作成するには、アドオン `azure-keyvault-secrets-provider` を指定して [az-aks-create][az-aks-create] コマンドを使用します。

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider
```

## <a name="upgrade-an-existing-aks-cluster-with-secrets-store-csi-driver-support"></a>シークレット ストア CSI ドライバー サポート付きの既存の AKS クラスターをアップグレードする

> [!NOTE]
> ユーザー割り当てまたはシステム割り当てマネージド ID を使用してクラスターへのアクセスを提供する予定がある場合は、`enable-managed-identity` フラグを使用してクラスターで Azure Active Directory を有効にします。 詳細については、「[Azure Kubernetes Service でマネージド ID を使用する][aks-managed-identity]」を参照してください。

シークレット ストア CSI ドライバー機能を備えた既存の AKS クラスターをアップグレードするには、アドオン `azure-keyvault-secrets-provider` を指定して [az-aks-create][az-aks-create] コマンドを使用します。

```azurecli-interactive
az aks upgrade -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider
```

これらのコマンドにより、シークレット ストア CSI ドライバーと Azure Key Vault プロバイダーがノードにインストールされます。 すべての名前空間のすべてのポッドを一覧表示し、出力が次のようになっていることを確認することで検証します。

```bash
kubectl get pods -n kube-system

NAMESPACE     NAME                                     READY   STATUS    RESTARTS   AGE
kube-system   aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
kube-system   aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
kube-system   aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
kube-system   aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
kube-system   aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
kube-system   aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```

### <a name="enabling-autorotation"></a>自動ローテーションの有効化

> [!NOTE]
> 有効にすると、シークレット ストア CSI ドライバーは、2 分ごとに変更をポーリングして、ポッド マウントと、SecretProviderClass の secretObjects に定義されている Kubernetes シークレットを更新します。

シークレットの自動ローテーションを有効にするには、クラスターの作成時に `enable-secret-rotation` フラグを使用します。

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation --rotation-poll-interval 5m
```

## <a name="create-or-use-an-existing-azure-key-vault"></a>Azure Key Vault を作成するか既存のものを使用する

AKS クラスターに加えて、シークレット コンテンツを含む Azure Key Vault リソースが必要です。 Azure Key Vault インスタンスをデプロイするには、こちらの手順に従います。

1. [Key Vault を作成します][create-key-vault]
2. [キー コンテナー内のシークレットを設定します][set-secret-key-vault]

次のセクションで使用するために、次のプロパティをメモしておきます。

- Key Vault 内のシークレット オブジェクトの名前
- シークレットのコンテンツの種類 (シークレット、キー、証明書)
- Key Vault リソースの名前
- サブスクリプションが属している Azure テナント ID

## <a name="create-and-apply-your-own-secretproviderclass-object"></a>独自の SecretProviderClass オブジェクトを作成して適用する

AKS クラスターに対してシークレット ストア CSI ドライバーを使用および構成するには、SecretProviderClass カスタム リソースを作成します。

キー コンテナーにアクセスするためのサービス プリンシパルの使用例を次に示します。

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"         # [OPTIONAL] if not provided, will default to "false"
    keyvaultName: "kvname"          # the name of the KeyVault
    cloudName: ""                   # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud 
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret        # object types: secret, key or cert
          objectVersion: ""         # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: "<tenant-id>"                 # the tenant ID of the KeyVault
```

詳細については、「[独自の SecretProviderClass オブジェクトを作成する][sample-secret-provider-class]」を参照してください。 上記でメモした値を必ず使用するようにしてください。

## <a name="provide-identity-to-access-azure-key-vault"></a>Azure Key Vault にアクセスするために ID を提供する

この記事の例ではサービス プリンシパルを使用していますが、Azure Key Vault プロバイダーには 4 つのアクセス方法が用意されています。 それらを確認し、お客様のユース ケースに最適なものを選択します。 選択した方法によっては、キー コンテナーからシークレットを取得するためのサービス プリンシパルのアクセス許可の付与など、追加の手順が必要になる場合があることに注意してください。

- [サービス プリンシパル][service-principal-access]
- [ポッド ID][pod-identity-access]
- [ユーザー割り当てマネージド ID][ua-mi-access]
- [システム割り当てマネージド ID][sa-mi-access]

### <a name="apply-the-secretproviderclass-to-your-cluster"></a>クラスターに SecretProviderClass を適用する

次に、作成した SecretProviderClass をデプロイします。 次に例を示します。

```bash
kubectl apply -f ./new-secretproviderclass.yaml
```

## <a name="update-and-apply-your-clusters-deployment-yaml"></a>クラスターのデプロイ YAML を更新して適用する

新しいカスタム リソースが確実にクラスターで使用されているようにするには、デプロイ YAML を更新します。 より包括的な例については、サービス プリンシパルを使用して Azure Key Vault にアクセスする[サンプル デプロイ][sample-deployment]を参照してください。 必ず、選択したキー コンテナー アクセス方法の追加の手順をすべて行うようにしてください。

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
      - "/bin/sh"
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
          secretProviderClass: "azure-kvname"
        nodePublishSecretRef:                       # Only required when using service principal mode
          name: secrets-store-creds                 # Only required when using service principal mode. The name of the Kubernetes secret that contains the service principal credentials to access keyvault.
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

## print a test secret 'secret1' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

## <a name="disable-secrets-store-csi-driver"></a>シークレット ストア CSI ドライバーを無効にする

既存のクラスターでシークレット ストア CSI ドライバー機能を無効にするには、無効化のアドオン `azure-keyvault-secrets-provider` を指定して az aks コマンドを使用します。

```azurecli-interactive
az aks disable-addons -n myAKSCluster -g myResourceGroup --addons azure-keyvault-secrets-provider
```

## <a name="next-steps"></a>次の手順
<!-- Add a context sentence for the following links -->
AKS クラスターで CSI シークレット ストア ドライバーを使用する方法を学習したら、次のリソースを参照してください。

- [シークレット ストア CSI ドライバー用に Azure Key Vault プロバイダーを実行する][key-vault-provider]
- [AKS で Azure ディスクおよび Azure Files 用の CSI ドライバーを有効にする][csi-storage-drivers]

<!-- Links -->
<!-- Internal -->
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[key-vault-provider]: ../key-vault/general/key-vault-integrate-kubernetes.md
[csi-storage-drivers]: ./csi-storage-drivers.md
[create-key-vault]: ../key-vault/general/quick-create-cli.md
[set-secret-key-vault]: ../key-vault/secrets/quick-create-portal.md
[aks-managed-identity]: ./use-managed-identity.md

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
[service-principal-access]: https://azure.github.io/secrets-store-csi-driver-provider-azure/configurations/identity-access-modes/service-principal-mode/
[pod-identity-access]: https://azure.github.io/secrets-store-csi-driver-provider-azure/configurations/identity-access-modes/pod-identity-mode/
[ua-mi-access]: https://azure.github.io/secrets-store-csi-driver-provider-azure/configurations/identity-access-modes/user-assigned-msi-mode/
[sa-mi-access]: https://azure.github.io/secrets-store-csi-driver-provider-azure/configurations/identity-access-modes/system-assigned-msi-mode/
[sample-deployment]: https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/examples/service-principal/pod-inline-volume-service-principal.yaml
