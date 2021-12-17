---
title: Azure Kubernetes Service (AKS) 用クラスター拡張機能 (プレビュー)
description: Azure Kubernetes Service (AKS) で拡張機能のライフサイクルをデプロイおよび管理する方法について説明します。
ms.service: container-service
ms.date: 10/13/2021
ms.topic: article
author: nickomang
ms.author: nickoman
ms.openlocfilehash: 80d6eb34e1b1e0bbce6a8a1f1d2de58dbec51b4c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447430"
---
# <a name="deploy-and-manage-cluster-extensions-for-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service (AKS) のクラスター拡張機能のデプロイと管理 (プレビュー)

クラスター拡張機能を使用すると Azure Resource Manager AKS クラスターに Azure Machine Learning (ML) のようなサービスをインストールおよびライフサイクル管理できます。 この機能により以下が可能になります。

* Azure Resource Manager AKS クラスター間での大規模なデプロイを含む、拡張機能の大規模なデプロイ。
* Azure Resource Manager からの拡張機能のライフサイクル管理 (更新、削除)。

この記事では、以下について説明します。
> [!div class="checklist"]

> * 拡張機能インスタンスを作成する方法。
> * AKS で使用可能なクラスター拡張機能。
> * 拡張機能インスタンスを表示、一覧表示、更新、削除する方法。

この機能の概要については、「[クラスター拡張機能 - Azure Arc 対応 Kubernetes][arc-k8s-extensions]」の記事を参照してください。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI](/cli/azure/install-azure-cli) バージョン >= 2.16.0 がインストールされています。

### <a name="register-provider-for-cluster-extensions"></a>クラスター拡張機能のプロバイダーを登録する

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 次のコマンドを入力します。

    ```azurecli-interactive
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ContainerService
    ```

2. 登録プロセスを監視します。 登録には最大で 10 分かかる場合があります。

    ```azurecli-interactive
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ContainerService -o table
    ```

    登録すると、これらの名前空間の `RegistrationState` 状態が `Registered` に変わります。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 次のコマンドを入力します。

    ```azurepowershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerService
    ```

1. 登録プロセスを監視します。 登録には最大で 10 分かかる場合があります。

    ```azurepowershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerService
    ```

    登録すると、これらの名前空間の `RegistrationState` 状態が `Registered` に変わります。

---

### <a name="register-the-aks-extensionmanager-preview-features"></a>プレビュー機能 `AKS-ExtensionManager` を登録する

クラスター拡張機能を使用できる AKS クラスターを作成するには、サブスクリプションで機能フラグ `AKS-ExtensionManager` を有効にする必要があります。

`AKS-ExtensionManager` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.KubernetesConfiguration* と *Microsoft.ContainerService* のリソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>クラスター拡張機能用の Azure CLI 拡張機能を設定する

> [!NOTE]
> 拡張機能でサポートされている最小 `k8s-extension` バージョンは Azure CLI 拡張機能 `1.0.0` です。 インストールしたバージョンが不明な場合は、`az extension show --name k8s-extension`を実行して 、`version` フィールドを探します。

また、`k8s-extension` Azure CLI 拡張機能も必要です。 次のコマンドを実行してこれをインストールします。
  
```azurecli-interactive
az extension add --name k8s-extension
```

`k8s-extension` 拡張機能が既にインストールされている場合は、次のコマンドを使用して最新バージョンに更新できます。

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="currently-available-extensions"></a>現在使用可能な拡張機能

>[!NOTE]
> クラスター拡張機能は、さまざまな拡張機能を AKS クラスターにインストールして管理するプラットフォームを提供します。 これらの拡張機能の使用中に問題が発生した場合は、それぞれのサービスでサポート チケットを開きます。

| 拡張機能 | 説明 |
| --------- | ----------- |
| [Dapr][dapr-overview] | Dapr は移植可能なイベント駆動型ランタイムであり、開発者はクラウドとエッジで実行される回復力のあるステートレスでステートフルなアプリケーションを簡単に構築できます。 |
| [Azure ML][azure-ml-overview] | Azure Kubernetes Service クラスターを使用して、機械学習モデルを Azure Machine Learning でトレーニング、推論、管理します。 |

## <a name="supported-regions-and-kubernetes-versions"></a>サポートされているリージョンと Kubernetes のバージョン

クラスター拡張機能は、Kubernetes リージョンのサポートが有効になっているリージョンに記載されているリージョン [Azure Arc AKS クラスターで使用できます][arc-k8s-regions]。

サポートされている Kubernetes のバージョンについては、各拡張機能の対応するドキュメントを参照してください。

## <a name="usage-of-cluster-extensions"></a>クラスター拡張機能の使用

> [!NOTE]
> この記事で提供されているサンプルは完全ではなく、機能を紹介するだけのサンプルです。 コマンドとそのパラメーターの包括的な一覧については [、az k8s-extension CLI リファレンス][k8s-extension-reference]を参照してください。

### <a name="create-extensions-instance"></a>拡張機能インスタンスを作成する

`k8s-extension create` を使用して、必須パラメーターの値を渡して、新しい拡張機能インスタンスを作成します。 次のコマンドは、AKS Azure Machine Learning 拡張機能インスタンスを作成します。

```azurecli
az k8s-extension create --name aml-compute --extension-type Microsoft.AzureML.Kubernetes --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters --configuration-settings enableInference=True allowInsecureConnections=True
```

> [!NOTE]
> クラスター拡張機能サービスは、48 時間を超える間、機密情報を保持できません。 クラスター拡張機能エージェントが 48 時間を超えるネットワーク接続を持たず、クラスターに拡張機能を作成するかどうかを判断できない場合、拡張機能が `Failed` 状態に遷移します。 状態が `Failed` に戻った後、新しい拡張機能インスタンスを作成するには、もう一度 `k8s-extension create` を実行する必要があります。

#### <a name="required-parameters"></a>必須のパラメーター

| パラメーター名 | 説明 |
|----------------|------------|
| `--name` | 拡張機能インスタンスの名前 |
| `--extension-type` | クラスターにインストールする拡張機能の種類。 例: Microsoft.AzureML.Kubernetes | 
| `--cluster-name` | 拡張機能インスタンスを作成する必要がある AKS クラスターの名前 |
| `--resource-group` | AKS クラスターを含むリソース グループ |
| `--cluster-type` | 拡張機能インスタンスを作成する必要があるクラスターの種類。 AKS クラスターにマッピングされる `managedClusters` を指定する|

#### <a name="optional-parameters"></a>省略可能なパラメーター

| パラメーター名 | 説明 |
|--------------|------------|
| `--auto-upgrade-minor-version` | 拡張機能のマイナー バージョンを自動的にアップグレードするかどうかを指定するブール型プロパティ。 既定値:`true`。  このパラメーターが true に設定されている場合、バージョンは動的に更新されるため、`version` パラメーターは設定できません。 `false` に設定されている場合、パッチ バージョンでも、拡張機能は自動アップグレードされません。 |
| `--version` | インストールする拡張機能のバージョン (拡張機能インスタンスの固定先の特定バージョン)。 auto-upgrade-minor-version が `true` に設定されている場合、指定しないでください。 |
| `--configuration-settings` | 機能を制御するために拡張機能に渡すことができる設定。 これらは、パラメーター名の後で、`key=value` のペアをスペースで区切って渡します。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-settings-file` は使用できません。 |
| `--configuration-settings-file` | 拡張機能に構成設定を渡すために使用されるキーと値のペアを含む JSON ファイルへのパス。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-settings` は使用できません。 |
| `--configuration-protected-settings` | これらの設定は、`GET` API 呼び出しまたは `az k8s-extension show` コマンドを使用して取得できないため、機密設定を渡すために使用されます。 これらは、パラメーター名の後で、`key=value` のペアをスペースで区切って渡します。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-protected-settings-file` は使用できません。 |
| `--configuration-protected-settings-file` | 拡張機能に機密設定を渡すために使用されるキーと値のペアを含む JSON ファイルへのパス。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-protected-settings` は使用できません。 |
| `--scope` | 拡張機能のインストールのスコープ - `cluster` または `namespace` |
| `--release-namespace` | このパラメーターは、リリースを作成する名前空間を示します。 このパラメーターは、`scope` パラメーターが `cluster` に設定されている場合にのみ関係します。 |
| `--release-train` |  拡張機能の作成者は、`Stable`、`Preview` など、さまざまなリリース トレインでバージョンを公開できます。このパラメーターが明示的に設定されていない場合、既定値として `Stable` が使用されます。 `autoUpgradeMinorVersion` パラメーターが `false` に設定されている場合、このパラメーターは使用できません。 |
| `--target-namespace` | このパラメーターは、リリースを作成する名前空間を示します。 この拡張機能インスタンスに対して作成されたシステム アカウントのアクセス許可は、この名前空間に制限されます。 このパラメーターは、`scope` パラメーターが `namespace` に設定されている場合にのみ関係します。 |

### <a name="show-details-of-an-extension-instance"></a>拡張機能インスタンスの詳細を表示する

`k8s-extension show` を使用し、必須パラメーターの値を渡すことで、現在インストールされている拡張機能インスタンスの詳細を表示します。

```azurecli
az k8s-extension show --name azureml --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>クラスターにインストールされているすべての拡張機能を一覧表示する

`k8s-extension list` を使用し、必須パラメーターの値を渡すことで、クラスターにインストールされているすべての拡張機能を一覧表示します。

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

### <a name="update-extension-instance"></a>拡張機能インスタンスを更新する

> [!NOTE]
> 更新が許可されている ConfigurationSetting と ConfigurationProtectedSettings の特定の設定については、拡張機能の種類 (例: Azure ML) のドキュメントを参照してください。 ConfigurationProtectedSettings の場合、1 つの設定の更新中に、すべての設定が提供される必要があります。 一部の設定を省略した場合、これらの設定は古いと見なされ、削除されます。

`k8s-extension update`で既存の拡張インスタンスを更新し、必須パラメーターの値を渡します。 次のコマンドは、拡張機能インスタンスの自動アップグレード Azure Machine Learning を更新します。

```azurecli
az k8s-extension update --name azureml --extension-type Microsoft.AzureML.Kubernetes --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

**必須のパラメーター**

| パラメーター名 | 説明 |
|----------------|------------|
| `--name` | 拡張機能インスタンスの名前 |
| `--extension-type` | クラスターにインストールする拡張機能の種類。 例: Microsoft.AzureML.Kubernetes | 
| `--cluster-name` | 拡張機能インスタンスを作成する必要がある AKS クラスターの名前 |
| `--resource-group` | AKS クラスターを含むリソース グループ |
| `--cluster-type` | 拡張機能インスタンスを作成する必要があるクラスターの種類。 AKS クラスターにマッピングされる `managedClusters` を指定する|

**省略可能なパラメーター**

| パラメーター名 | 説明 |
|--------------|------------|
| `--auto-upgrade-minor-version` | 拡張機能のマイナー バージョンを自動的にアップグレードするかどうかを指定するブール型プロパティ。 既定値:`true`。  このパラメーターが true に設定されている場合、バージョンは動的に更新されるため、`version` パラメーターは設定できません。 `false` に設定されている場合、パッチ バージョンでも、拡張機能は自動アップグレードされません。 |
| `--version` | インストールする拡張機能のバージョン (拡張機能インスタンスの固定先の特定バージョン)。 auto-upgrade-minor-version が `true` に設定されている場合、指定しないでください。 |
| `--configuration-settings` | 機能を制御するために拡張機能に渡すことができる設定。 更新を必要とする設定のみを指定する必要があります。 指定された設定は、指定された値に置き換わります。 これらは、パラメーター名の後で、`key=value` のペアをスペースで区切って渡します。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-settings-file` は使用できません。 |
| `--configuration-settings-file` | 拡張機能に構成設定を渡すために使用されるキーと値のペアを含む JSON ファイルへのパス。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-settings` は使用できません。 |
| `--configuration-protected-settings` | これらの設定は、`GET` API 呼び出しまたは `az k8s-extension show` コマンドを使用して取得できないため、機密設定を渡すために使用されます。 設定を更新する場合は、すべての設定が提供される必要があります。 一部の設定を省略した場合、これらの設定は古いと見なされ、削除されます。 これらは、パラメーター名の後で、`key=value` のペアをスペースで区切って渡します。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-protected-settings-file` は使用できません。 |
| `--configuration-protected-settings-file` | 拡張機能に機密設定を渡すために使用されるキーと値のペアを含む JSON ファイルへのパス。 このパラメーターをコマンドで使用する場合、同じコマンドで `--configuration-protected-settings` は使用できません。 |
| `--scope` | 拡張機能のインストールのスコープ - `cluster` または `namespace` |
| `--release-train` |  拡張機能の作成者は、`Stable`、`Preview` など、さまざまなリリース トレインでバージョンを公開できます。このパラメーターが明示的に設定されていない場合、既定値として `Stable` が使用されます。 `autoUpgradeMinorVersion` パラメーターが `false` に設定されている場合、このパラメーターは使用できません。 |

### <a name="delete-extension-instance"></a>拡張機能インスタンスを削除する

>[!NOTE]
> この拡張機能を表す Azure リソースはただちに削除されます。 Kubernetes クラスターで実行されているエージェントがネットワークに接続していて、目的の状態を取得するために Azure サービスに再びアクセスできる場合にのみ、この拡張機能に関連するクラスターの Helm リリースが削除されます。

`k8s-extension delete` を使用し、必須パラメーターの値を渡すことで、クラスター上の拡張機能インスタンスを削除します。

```azurecli
az k8s-extension delete --name azureml --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

<!-- LINKS -->
<!-- INTERNAL -->
[arc-k8s-extensions]: ../azure-arc/kubernetes/conceptual-extensions.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-ml-overview]: ../machine-learning/how-to-attach-arc-kubernetes.md
[dapr-overview]: ./dapr.md
[k8s-extension-reference]: /cli/azure/k8s-extension

<!-- EXTERNAL -->
[arc-k8s-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc&regions=all