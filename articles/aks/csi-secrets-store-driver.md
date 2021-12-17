---
title: Azure Kubernetes Service シークレットのシークレット ストア CSI ドライバーで Azure Key Vault プロバイダーを使用する
description: シークレット ストア CSI ドライバーで Azure Key Vault プロバイダーを使用してシークレット ストアと Azure Kubernetes Service (AKS) を統合する方法について説明します。
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: cdab72e0a1633aa75a5594acf9b506d944f0eaa0
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893009"
---
# <a name="use-the-azure-key-vault-provider-for-secrets-store-csi-driver-in-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service クラスターでシークレット ストア CSI ドライバーに Azure Key Vault プロバイダーを使用する

シークレット ストア CSI ドライバーに Azure Key Vault プロバイダーを使用すると、[CSI ボリューム][kube-csi]経由で Azure Key Vault をシークレット ストアとして Kubernetes クラスターと統合できます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- 開始する前に、Azure CLI バージョンが `2.30.0` 以上であることを確認するか、[最新バージョンをインストール](/cli/azure/install-azure-cli)してください。

### <a name="supported-kubernetes-versions"></a>サポートされている Kubernetes のバージョン

推奨される Kubernetes の最小バージョンは、[ローリング Kubernetes バージョン サポート ウィンドウ][kubernetes-version-support]に基づいています。 N-2 以上の最新バージョンを実行している必要があります。

## <a name="features"></a>フィーチャー

- CSI ボリュームを使用して、ポッドにシークレット、キー、および証明書 (またはそのいずれか) をマウントします
- CSI インライン ボリュームがサポートされています
- 複数のシークレット ストア オブジェクトを 1 つのボリュームとしてマウントすることがサポートされています
- SecretProviderClass CRD によるポッド移植性がサポートされています
- Windows コンテナーがサポートされています
- Kubernetes シークレットと同期します
- マウントされたコンテンツと同期された Kubernetes シークレットの自動ローテーションがサポートされています

## <a name="create-an-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>シークレット ストア CSI ドライバー サポートのために Azure Key Vault プロバイダーを使用して AKS クラスターを作成する

最初に、Azure リソース グループを作成します。

```azurecli-interactive
az group create -n myResourceGroup -l eastus2
```

シークレット ストア CSI ドライバー機能のために AKS クラスターを Azure Key Vault プロバイダーで作成するには、アドオン `azure-keyvault-secrets-provider` を指定して [az aks create][az-aks-create] コマンドを使用します。

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-managed-identity
```

ユーザー割り当てマネージド ID は、`azurekeyvaultsecretsprovider-*` という名前の Azure リソースにアクセスするために、アドオンによって作成されます。 この例では、この ID を使用して、シークレットが格納される Azure Key Vault に接続しますが、他の [ID アクセス方法][identity-access-methods]を使用できます。 出力内の ID の `clientId` をメモします。

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

## <a name="upgrade-an-existing-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>シークレット ストア CSI ドライバー サポートのために Azure Key Vault プロバイダーを使用して既存の AKS クラスターをアップグレードする

シークレット ストア CSI ドライバー機能のために既存の AKS クラスターを Azure Key Vault プロバイダーを使用してアップグレードするには、アドオン `azure-keyvault-secrets-provider` を指定して [az aks enable-addons][az-aks-enable-addons] コマンドを使用します。

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

前述のように、アドオンによって、ユーザー割り当てマネージド ID が作成され、この ID を使用して、Azure Key Vault を認証できます。

## <a name="verify-azure-key-vault-provider-for-secrets-store-csi-driver-installation"></a>シークレット ストア CSI ドライバー インストール用に Azure Key Vault プロバイダーを確認する

上記では、シークレット ストア CSI ドライバーと Azure Key Vault プロバイダーがノードにインストールされます。 kube-system 名前空間の `secrets-store-csi-driver` および `secrets-store-provider-azure` ラベルを持つすべてのポッドを一覧表示して、完了を確認し、出力が以下のようになることを確認します。

```bash
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'

NAME                                     READY   STATUS    RESTARTS   AGE
aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```

シークレット ストア CSI ドライバー ポッドと Azure Key Vault プロバイダー ポッドが、クラスターのノード プール内の各ノードで実行されている必要があります。

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

シークレット ストア CSI ドライバーを使用すると、次の方法を使用して、Azure Key Vault インスタンスにアクセスできます。
- [Azure Active Directory のポッド ID][aad-pod-identity]
- システム割り当てマネージド ID の使用

ステップに従って、選択した方法の [ID を指定し、Azure Key Vault にアクセスします][identity-access-methods]。

## <a name="validate-the-secrets"></a>シークレットを検証する

ポッドが起動すると、デプロイ YAML で指定されたボリューム パスにマウントされたコンテンツが使用できるようになります。

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'ExampleSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/ExampleSecret
```

## <a name="obtaining-certificates-and-keys"></a>証明書とキーの取得

Azure Key Vault の設計では、キー、シークレット、証明書が明確に区別されます。 Key Vault サービスの証明書機能は、そのキーとシークレットの機能を利用して設計されています。 Key Vault 証明書が作成されると、アドレス指定可能なキーとシークレットも同じ名前で作成されます。 キーを使うとキー操作を行うことができ、シークレットを使うとシークレットとして証明書の値を取得できます。 Key Vault 証明書には、公開 x509 証明書メタデータも含まれます。 Azure Key Vault によって証明書の公開と非公開の両方の部分がシークレットに格納されます。 各コンポーネントは、SecretProviderClass で `objectType`を指定することで取得できます。 次の表は、証明書に関連付けられているさまざまなリソースにマップされるオブジェクトを示しています。

|Object|戻り値|証明書チェーン全体を返します|
|---|---|---|
|`key`|PEM 形式の公開キー|該当なし|
|`cert`|PEM 形式の証明書|いいえ|
|`secret`|PEM 形式の秘密キーと証明書|はい|

## <a name="disable-azure-key-vault-provider-for-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>既存の AKS クラスターでシークレット ストア CSI ドライバーの Azure Key Vault プロバイダーを無効にする

> [!NOTE]
> アドオンを無効にする前に、`SecretProviderClass` が使用されていないことを必ず確認してください。 `SecretProviderClass` が存在している間にアドオンを無効にしようとすると、エラーが発生します。

既存のクラスターで Azure Key Vault プロバイダーのシークレット ストア CSI ドライバー機能を無効にするには、`azure-keyvault-secrets-provider` フラグを指定して [az aks disable-addons][az-aks-disable-addons] コマンドを使用します。

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

> [!NOTE]
> アドオンが無効になっている場合、既存のワークロードでは問題が発生せず、マウントされたシークレットに更新プログラムは表示されません。 ポッドが再起動するか、スケールアップ イベントの一部として新しいポッドが作成された場合、ドライバーが実行されなくなるため、ポッドの起動に失敗します。

## <a name="additional-configuration-options"></a>追加の構成オプション

### <a name="enabling-and-disabling-autorotation"></a>自動ローテーションの有効化と無効化

> [!NOTE]
> 有効にすると、シークレット ストア CSI ドライバーの Azure Key Vault プロバイダーは、定義されたローテーション ポーリング間隔に基づいて定期的に変更をポーリングすることで、SecretProviderClass の secretObjects で定義されているポッド マウントと Kubernetes シークレットを更新します。 既定のローテーション ポーリング間隔は 2 分です。

シークレットの自動ローテーションを有効にするには、クラスターの作成時に `enable-secret-rotation` フラグを使用します。

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

または、アドオンが有効になっている既存のクラスターを更新します。

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

カスタム ローテーション間隔を指定するには、フラグ `rotation-poll-interval` を使用します。

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation --rotation-poll-interval 5m
```

無効にするには、フラグ `disable-secret-rotation` を使用します。

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

### <a name="sync-mounted-content-with-a-kubernetes-secret"></a>マウントされたコンテンツを Kubernetes シークレットと同期する

場合によっては、マウントされたコンテンツをミラー化するために Kubernetes シークレットを作成できます。

SecretProviderClass を作成する場合は、`secretObjects` フィールドを使用して、Kubernetes シークレットの目的の状態を定義します。

> [!NOTE]
> この例がすべてではありません。 Azure Key Vault ID アクセスの選択した方法をサポートするには、この例を変更しなければなりません。

> [!NOTE]
> シークレットは、シークレットをマウントするポッドを起動した後にのみ同期されます。 Kubernetes シークレット機能との同期のみに依存してもうまくいきません。 シークレットを使用しているすべてのポッドが削除されると、Kubernetes シークレットも削除されます。


> [!NOTE]
> `secretObjects` の `objectName` がマウントされたコンテンツのファイル名と一致していることを確認します。 `objectAlias` を代わりに使用する場合は、オブジェクトの別名と一致する必要があります。

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-sync
spec:
  provider: azure                             
  secretObjects:                              # [OPTIONAL] SecretObject defines the desired state of synced K8s secret objects
  - data:
    - key: username                           # data field to populate
      objectName: foo1                        # name of the mounted content to sync. this could be the object name or the object alias
    secretName: foosecret                     # name of the Kubernetes Secret object
    type: Opaque                              # type of the Kubernetes Secret object e.g. Opaque, kubernetes.io/tls
```

#### <a name="set-environment-variables-to-reference-kubernetes-secrets"></a>Kubernetes シークレットを参照する環境変数を設定する

Kubernetes シークレットが作成されると、Kubernetes シークレットを参照する環境変数をポッドに設定できます。

> [!NOTE]
> この例がすべてではありません。 Azure Key Vault ID アクセスの選択した方法をサポートするには、この例を変更しなければなりません。

```yml
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
      env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: foosecret
            key: username
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-sync"
```

## <a name="metrics"></a>メトリック

### <a name="azure-key-vault-provider"></a>Azure Key Vault プロバイダー

メトリックはポート 8898 から Prometheus 経由で提供されますが、このポートは既定ではポッドの外部に公開されません。 `kubectl port-forward` を使用して localhost でメトリックにアクセスします。

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-provider-azure 8898:8898 &
curl localhost:8898/metrics
```

次の表は、シークレット ストア CSI ドライバーの Azure Key Vault プロバイダーによって提供されるメトリックの一覧です。

|メトリック|説明|タグ|
|----|----|----|
|keyvault_request|Keyvault からの取得にかかった時間の分布|`os_type=<runtime os>`, `provider=azure`, `object_name=<keyvault object name>`, `object_type=<keyvault object type>`, `error=<error if failed>`|
|grpc_request|gRPC 要求からの取得にかかった時間の分布|`os_type=<runtime os>`, `provider=azure`, `grpc_method=<rpc full method>`, `grpc_code=<grpc status code>`, `grpc_message=<grpc status message>`|

### <a name="secrets-store-csi-driver"></a>シークレット ストア CSI ドライバー

メトリックはポート 8095 から提供されますが、このポートは既定ではポッドの外部に公開されません。 `kubectl port-forward` を使用して localhost でメトリックにアクセスします。

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-csi-driver 8095:8095 &
curl localhost:8095/metrics
```

次の表に、シークレットストア CSI ドライバーによって提供されるメトリックを示します。

|メトリック|説明|タグ|
|----|----|----|
|total_node_publish|成功したボリューム マウント要求の合計数|`os_type=<runtime os>`, `provider=<provider name>`|
|total_node_unpublish|成功したボリューム マウント解除要求の合計数|`os_type=<runtime os>`|
|total_node_publish_error|エラーがあったボリューム マウント要求の合計数|`os_type=<runtime os>`, `provider=<provider name>`, `error_type=<error code>`|
|total_node_unpublish_error|エラーがあったボリューム マウント解除要求の合計数|`os_type=<runtime os>`|
|total_sync_k8s_secret|同期された k8s シークレットの合計数|`os_type=<runtime os`, `provider=<provider name>`|
|sync_k8s_secret_duration_sec|K8s シークレットの同期にかかった時間の分布|`os_type=<runtime os>`|
|total_rotation_reconcile|ローテーションの調整の合計数|`os_type=<runtime os>`, `rotated=<true or false>`|
|total_rotation_reconcile_error|エラーがあったローテーションの調整の合計数|`os_type=<runtime os>`, `rotated=<true or false>`, `error_type=<error code>`|
|total_rotation_reconcile_error|ポッドのシークレット ストアのコンテンツをローテーションするためにかかった時間の分布|`os_type=<runtime os>`|

## <a name="next-steps"></a>次のステップ
<!-- Add a context sentence for the following links -->
AKS クラスターでのシークレットストア CSI ドライバーの Azure Key Vault プロバイダーの使用方法を学習したら、次のリソースを参照してください。

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
[identity-access-methods]: ./csi-secrets-store-identity-access.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[kubernetes-version-support]: ./supported-kubernetes-versions.md?tabs=azure-cli#kubernetes-version-support-policy

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
