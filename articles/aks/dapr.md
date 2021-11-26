---
title: Azure Kubernetes Service (AKS) 用 Dapr 拡張機能 (プレビュー)
description: Dapr クラスター拡張機能を使用して、Azure Kubernetes Service (AKS) クラスターに Dapr をインストールして構成します。
author: greenie-msft
ms.author: nigreenf
ms.service: container-service
ms.topic: article
ms.date: 10/15/2021
ms.custom: devx-track-azurecli, ignite-fall-2021
ms.openlocfilehash: 9d7a263a17d117aea162b39682fa5bd9ec4c1fde
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485760"
---
# <a name="dapr-extension-for-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service (AKS) 用 Dapr 拡張機能 (プレビュー)

[Dapr](https://dapr.io/) は移植可能なイベント ドリブン ランタイムです。開発者は、これを使用して、クラウドとエッジで実行される、回復力のあるステートレスでステートフルなアプリケーションを簡単に構築できます。また、多様な言語と開発者フレームワークの採用も可能です。 サイドカー アーキテクチャの利点を活用している Dapr では、マイクロサービスの構築に関連する課題に取り組み、コード プラットフォームを独立した状態に維持できます。 特に、他のサービスを確実かつ安全に呼び出すサービス、Pub-Sub を使用したイベント ドリブン アプリの構築、複数のクラウド サービスとホスト (Kubernetes と VM など) 間で移植可能なアプリケーションの構築に関する問題の解決に役立ちます。

AKS Dapr 拡張機能を使用して AKS クラスターに Dapr をプロビジョニングすることで、Dapr ツールをダウンロードし、AKS クラスターにランタイムを手動でインストールして管理するためのオーバーヘッドがなくなります。 さらに、この拡張機能では、単純なコマンド ライン引数により、すべての[ネイティブ Dapr 構成機能][dapr-configuration-options]がサポートされます。

> [!NOTE]
> Kubernetes の運用環境に Dapr をインストールする予定の場合は、[Dapr の運用環境での使用に関するガイドライン][kubernetes-production]のドキュメント ページを参照してください。

## <a name="how-it-works"></a>しくみ

AKS Dapr 拡張機能では、Azure CLI を使用して、AKS クラスターに Dapr コントロール プレーンをプロビジョニングします。 これにより、次が作成されます。

- **dapr-operator**: Dapr 用のコンポーネントの更新と Kubernetes サービス エンドポイント (状態ストア、Pub/Sub など) が管理されます
- **dapr-sidecar-inject**: 注釈付きデプロイ ポッドに Dapr が挿入され、環境変数 `DAPR_HTTP_PORT` と `DAPR_GRPC_PORT` が追加されて、ユーザー定義アプリケーションが有効化されます。これで、Dapr ポート値をハードコーディングせずに Dapr と簡単に通信できます。
- **dapr-placement**: アクターにのみ使用されます。 アクター インスタンスをポッドにマップするマッピング テーブルが作成されます
- **dapr-sentry**: サービス間の mTLS が管理され、証明機関として機能します。 詳細については、[セキュリティの概要][dapr-security]に関するページをお読みください。

AKS クラスターに Dapr がインストールされたので、アプリケーション サービスには一緒に実行される Dapr サイドカーが含まれています。 これにより、Dapr 構成要素 API の使用をすぐに開始できます。 構成要素 API の詳細な概要とそれらを最大限に活用する方法については、[Dapr の構成要素の概要][building-blocks-concepts]に関するページを参照してください。

> [!WARNING]
> AKS 拡張機能を使用して Dapr をインストールする場合は、Dapr の今後の管理のために、Dapr CLI ではなく拡張機能を引き続き使用することをお勧めします。 2 つのツールを組み合わせると、競合が発生し、望ましくない動作が発生する可能性があります。

## <a name="supported-kubernetes-versions"></a>サポートされている Kubernetes のバージョン

Dapr 拡張機能には、AKS と同じサポート ウィンドウが使われています。 詳細については、「[Kubernetes バージョン サポート ポリシー][k8s-version-support-policy]」を参照してください。

## <a name="prerequisites"></a>前提条件 

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- 最新バージョンの [Azure CLI](/cli/azure/install-azure-cli-windows) と *aks-preview* 拡張機能をインストールしてください。
- まだない場合は、[AKS クラスター][deploy-cluster]を作成する必要があります。


### <a name="register-the-aks-extensionmanager-and-aks-dapr-preview-features"></a>プレビュー機能 `AKS-ExtensionManager` と `AKS-Dapr` を登録する

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Dapr 拡張機能を使える AKS クラスターを作成するには、サブスクリプションで `AKS-ExtensionManager` と `AKS-Dapr` の機能フラグを有効にする必要があります。

次の例に示すように [az feature register][az-feature-register] コマンドを使って `AKS-ExtensionManager` と `AKS-Dapr` の機能フラグを登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
az feature register --namespace "Microsoft.ContainerService" --name "AKS-Dapr"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-Dapr')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.KubernetesConfiguration* と *Microsoft.ContainerService* のリソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>クラスター拡張機能用の Azure CLI 拡張機能を設定する

また、`k8s-extension` Azure CLI 拡張機能も必要です。 次のコマンドを実行してこれをインストールします。
  
```azurecli-interactive
az extension add --name k8s-extension
```

`k8s-extension` 拡張機能が既にインストールされている場合は、次のコマンドを使用して最新バージョンに更新できます。

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="create-the-extension-and-install-dapr-on-your-aks-cluster"></a>拡張機能を作成して AKS クラスターに Dapr をインストールする

Kubernetes 拡張機能を使用するようにサブスクリプションを登録したら、Dapr を AKS クラスターにインストールするための Dapr 拡張機能を作成できます。 たとえば、次のように入力します。

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr
```

`--auto-upgrade-minor-version` パラメーターを指定して値を `true` に設定することで、Dapr にマイナー バージョンの自動更新を許可することができます。

```azure-cli-interactive
--auto-upgrade-minor-version true
```

## <a name="configuration-settings"></a>構成設定

この拡張機能を使用すると、`--configuration-settings` パラメーターを使用して Dapr 構成オプションを設定できます。 たとえば、高可用性 (HA) が有効な Dapr をプロビジョニングするには、`global.ha.enabled` パラメーターを `true` に設定します。

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2"
```

> [!NOTE]
> 構成設定の機密性が高く保護する必要がある場合 (証明書関連情報など) は、`--configuration-protected-settings` パラメーターを渡します。これで、値が読み取りから保護されます。

構成設定を渡さない場合、Dapr 構成の既定値は次のようになります。

```yaml
  ha:
    enabled: true
    replicaCount: 3
    disruption:
      minimumAvailable: ""
      maximumUnavailable: "25%"
  prometheus:
    enabled: true
    port: 9090
  mtls:
    enabled: true
    workloadCertTTL: 24h
    allowedClockSkew: 15m
```

使用可能なオプションの一覧については、[Dapr の構成][dapr-configuration-options]に関するページを参照してください。

## <a name="targeting-a-specific-dapr-version"></a>特定の Dapr バージョンを対象とする

> [!NOTE]
> Dapr は、現在および以前のバージョンのみを含むローリング ウィンドウでサポートされています。 これらのサポートされているバージョンを最新の状態に維持する運用上の責任があります。 古いバージョンの Dapr がある場合、サポートされているバージョンを取得するには、中間アップグレードの実行が必要なことがあります。

特定のバージョンの Dapr をインストールしたり、以前のバージョンにロールバックしたりするには、同じコマンド ライン引数を使用します。 `--auto-upgrade-minor-version` を `false` に、`--version` をインストールする Dapr のバージョンに設定します。 `version` パラメーターを省略すると、拡張機能によって最新バージョンの Dapr がインストールされます。 たとえば、Dapr X.X.X を使用するには、次のようにします。 

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version false \
--version X.X.X
```

## <a name="limiting-the-extension-to-certain-nodes-nodeselector"></a>拡張機能を特定のノード (`nodeSelector`) に制限する

一部の構成では、特定のノードでのみ Dapr を実行したい場合があります。 拡張機能の構成で `nodeSelector` を渡せばこれを実現できます。 求められる `nodeSelector` に `.` が含まれる場合は、シェルと拡張機能からエスケープする必要があります。 たとえば、次の構成では、`kubernetes.io/os=linux` を使用するノードにのみ Dapr がインストールされます:

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" \
--configuration-settings "global.nodeSelector.kubernetes\.io/os=linux"
```

## <a name="show-current-configuration-settings"></a>現在の構成設定を表示する

`az k8s-extension show` コマンドを使用して、現在の Dapr 構成設定を表示します。  

```azure-cli-interactive
az k8s-extension show --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension
```

## <a name="update-configuration-settings"></a>構成設定の更新

> [!IMPORTANT]
> 一部の構成オプションは、作成後に変更できません。 これらのオプションを調整するには、拡張機能の削除と変更が必要です。 これは次の設定に適用されます。
> * `global.ha.*`
> * `dapr_placement.*`

> [!NOTE]
> 高可用性 (HA) は、いつでも有効にできます。 ただし、これを有効にした場合、無効にするには、拡張機能を削除して再作成する必要があります。 ユース ケースに高可用性が必要であるか不明な場合は、中断を最小限に抑えるために、最初にこれを無効にすることをお勧めします。

Dapr 構成設定を更新するには、目的の状態で拡張機能を再作成します。 たとえば、以前に次の構成を使用して拡張機能を作成およびインストールしたとします。

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \  
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" 
```

`dapr_operator.replicaCount` を 2 から 3 に更新するには、次を使用します。

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=3"
```

## <a name="troubleshooting-extension-errors"></a>拡張機能のエラーのトラブルシューティング

拡張機能の作成または更新に失敗した場合は、`az k8s-extension list` コマンドを実行して、拡張機能の作成に失敗した場所を確認できます。 たとえば、`global.ha.enabled=false` の代わりに `global.ha=false` など、構成設定で不適切なキーが使用 されている場合は、次のようになります。 

```azure-cli-interactive
az k8s-extension list --cluster-type managedClusters --cluster-name myAKSCluster --resource-group myResourceGroup
```

次の JSON が返され、エラー メッセージが `message` プロパティにキャプチャされます。

```json
"statuses": [
      {
        "code": "InstallationFailed",
        "displayStatus": null,
        "level": null,
        "message": "Error: {failed to install chart from path [] for release [dapr-1]: err [template: dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_poddisruptionbudget.yaml:1:17: executing \"dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_poddisruptionbudget.yaml\" at <.Values.global.ha.enabled>: can't evaluate field enabled in type interface {}]} occurred while doing the operation : {Installing the extension} on the config",
        "time": null
      }
],
```

## <a name="delete-the-extension"></a>拡張機能を削除する

拡張機能を削除し、AKS クラスターから Dapr を削除する必要がある場合は、次のコマンドを使用できます。 

```azure-cli-interactive
az k8s-extension delete --resource-group myResourceGroup --cluster-name myAKSCluster --cluster-type managedClusters --name myDaprExtension
```

## <a name="next-steps"></a>次の手順

- AKS クラスターに Dapr が正常にプロビジョニングされたので、[サンプル アプリケーション][sample-application]のデプロイを試します。

<!-- LINKS INTERNAL -->
[deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[sample-application]: ./quickstart-dapr.md
[k8s-version-support-policy]: ./supported-kubernetes-versions.md?tabs=azure-cli#kubernetes-version-support-policy

<!-- LINKS EXTERNAL -->
[kubernetes-production]: https://docs.dapr.io/operations/hosting/kubernetes/kubernetes-production
[building-blocks-concepts]: https://docs.dapr.io/developing-applications/building-blocks/
[dapr-configuration-options]: https://github.com/dapr/dapr/blob/master/charts/dapr/README.md#configuration
[sample-application]: https://github.com/dapr/quickstarts/tree/master/hello-kubernetes#step-2---create-and-configure-a-state-store
[dapr-security]: https://docs.dapr.io/concepts/security-concept/
