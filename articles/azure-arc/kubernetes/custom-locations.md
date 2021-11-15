---
title: Azure Arc 対応 Kubernetes にカスタムの場所を作成および管理する
ms.service: azure-arc
ms.date: 10/19/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: カスタムの場所を使用して、Azure Arc 対応 Kubernetes クラスターに Azure PaaS サービスをデプロイします
ms.openlocfilehash: f241ec384fc9ed7ee96d7415074e009cea486811
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257028"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes にカスタムの場所を作成および管理する

 "*カスタムの場所*" を使用すると、テナントまたはクラスター管理者は、次のような Azure サービス インスタンスをデプロイするターゲットの場所として、Azure Arc 対応 Kubernetes クラスターを構成することができます。  Azure Arc 対応 SQL Managed Instance や Azure Arc 対応 PostgreSQL Hyperscale などのリソースです。 Azure Arc 対応 Kubernetes クラスターでのカスタムの場所は、Azure Arc 対応 Kubernetes クラスター内の名前空間の抽象化を表します。 テナントまたはクラスターの管理者は、Azure Arc 対応 SQL Managed Instance、Azure Arc 対応 PostgreSQL Hyperscale インスタンス、Azure Web アプリなどのリソースをカスタムの場所にデプロイするための、ロールベースのアクセス制御 (RBAC) のアクセス許可を、アプリケーション開発者やデータベース管理者に割り当てることができます。 
 
この機能の概念的な概要については、[Azure Arc 対応 Kubernetes のカスタムの場所](conceptual-custom-locations.md)に関する記事をご覧ください。 

この記事では、次の方法について説明します。
> [!div class="checklist"]
> * Azure Arc 対応 Kubernetes クラスター上のカスタムの場所を有効にする。
> * カスタムの場所を作成する。


## <a name="prerequisites"></a>前提条件

- バージョン 2.16.0 以降の [Azure CLI をインストールするか、それにアップグレードします](/cli/azure/install-azure-cli)。

- 次の Azure CLI 拡張機能をインストールします。
    - `connectedk8s` (バージョン 1.1.0 以降)
    - `k8s-extension` (バージョン 0.2.0 以降)
    - `customlocation` (バージョン 0.1.0 以降) 
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    `connectedk8s`、`k8s-extension`、`customlocation` の拡張機能を以前インストールした場合は、次のコマンドを使用して最新バージョンに更新します。

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```
    >[!NOTE]
    >最新バージョンの CLI 拡張機能を使用して最新の機能を入手することをお勧めします。  

- `Microsoft.ExtendedLocation` のプロバイダー登録の完了を確認します。
    1. 次のコマンドを入力します。
    
        ```azurecli
        az provider register --namespace Microsoft.ExtendedLocation
        ```

    2. 登録プロセスを監視します。 登録には最大で 10 分かかる場合があります。
    
        ```azurecli
        az provider show -n Microsoft.ExtendedLocation -o table
        ```

        登録後、`RegistrationState` 状態に `Registered` 値が与えられます。

- [Azure Arc 対応 Kubernetes に接続されたクラスター](quickstart-connect-cluster.md)が既に存在することを確認します。
    - バージョン 1.1.0 以降に[お使いのエージェントをアップグレードします](agent-upgrade.md#manually-upgrade-agents)。

## <a name="enable-custom-locations-on-cluster"></a>クラスターでカスタムの場所を有効にする

Azure AD ユーザーとして Azure CLI にログインしている場合、クラスターでこの機能を有効にするには、次のコマンドを実行します。

```azurecli
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

サービス プリンシパルを使用して Azure CLI にログインしている場合、クラスターでこの機能を有効にするには、次の手順を実行します。

1. Azure Arc サービスで使用される Azure AD アプリケーションのオブジェクト ID をフェッチします。

    ```azurecli
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. 上記の手順の `<objectId>` 値を使用して、クラスターでカスタムの場所機能を有効にします。

    ```azurecli
    az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
    ```

> [!NOTE]
> 1. カスタムの場所機能は、クラスター接続機能に依存します。 そのため、カスタムの場所を使用するには、両方の機能が有効になっている必要があります。
> 2. 機能を有効にするクラスターを指している `kubeconfig` ファイルが存在するマシン上で、`az connectedk8s enable-features` を実行する必要があります。

## <a name="create-custom-location"></a>カスタムの場所を作成する

1. クラスターにインストールする Azure サービス インスタンスの Azure サービス クラスター拡張機能をデプロイします。

    * [Azure Arc 対応 Data Services](../data/create-data-controller-direct-cli.md#create-the-arc-data-services-extension)

        > [!NOTE]
        > Azure Arc 対応 Data Services クラスター拡張機能では、認証を使用しない送信プロキシと、基本認証を使用する送信プロキシがサポートされています。 現在、信頼できる証明書が必要な送信プロキシはサポートされていません。


    * [Azure Arc 上の Azure App Service](../../app-service/manage-create-arc-environment.md#install-the-app-service-extension)

    * [Kubernetes 上の Event Grid](../../event-grid/kubernetes/install-k8s-extension.md)

2. Azure Arc 対応 Kubernetes クラスターの Azure Resource Manager 識別子を取得します。これは、後の手順で `connectedClusterId` として参照されます。

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

3. Azure Arc 対応 Kubernetes クラスターにデプロイされているクラスター拡張機能の Azure Resource Manager 識別子を取得します。これは、後の手順で `extensionId` として参照されます。

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

4. Azure Arc 対応 Kubernetes クラスターと拡張機能を参照して、カスタムの場所を作成します。

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
    ```

**必須のパラメーター**

| パラメーター名 | 説明 |
|----------------|------------|
| `--name, --n` | カスタムの場所の名前 |
| `--resource-group, --g` | カスタムの場所のリソース グループ  | 
| `--namespace` | 作成されるカスタムの場所にバインドされたクラスター内の名前空間 |
| `--host-resource-id` | Azure Arc 対応 Kubernetes クラスター (接続されたクラスター) の Azure Resource Manager 識別子 |
| `--cluster-extension-ids` | 接続されたクラスターにインストールされているクラスター拡張機能インスタンスの Azure Resource Manager 識別子。 クラスター拡張機能の ID をスペースで区切って指定します  |

**省略可能なパラメーター**

| パラメーター名 | 説明 |
|--------------|------------|
| `--assign-identity` | 既定値は `None` です。 パラメーターが "SystemAssigned" に設定されている場合は、[システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を作成します |
| `--location, --l` | Azure 内のカスタムの場所の Azure Resource Manager リソースの場所。 既定では、接続されたクラスターの場所 (または Azure リージョン) に設定されます。 |
| `--tags` | タグのスペース区切りのリスト: key[=value] [key[=value] ...]。既存のタグをクリアするには '' を使用します |
| `--kubeconfig` | クラスターの管理 kubeconfig。 クラスターが AAD 対応クラスターではない場合は、ファイルとして渡す必要があります |


## <a name="show-details-of-a-custom-location"></a>カスタムの場所の詳細を表示する

カスタムの場所の詳細を表示する

```azurecli
    az customlocation show -n <customLocationName> -g <resourceGroupName> 
```

**必須のパラメーター**

| パラメーター名 | 説明 |
|----------------|------------|
| `--name, --n` | カスタムの場所の名前 |
| `--resource-group, --g` | カスタムの場所のリソース グループ  | 

## <a name="list-custom-locations"></a>カスタムの場所を一覧表示する

リソース グループ内のすべてのカスタムの場所の一覧を表示します

```azurecli
    az customlocation show -g <resourceGroupName> 
```

**必須のパラメーター**

| パラメーター名 | 説明 |
|----------------|------------|
| `--resource-group, --g` | カスタムの場所のリソース グループ  | 


## <a name="update-a-custom-location"></a>カスタムの場所を更新する

新しいタグを追加し、既存のタグと関連付けられたクラスター拡張機能を維持したまま、新しいクラスター拡張機能 ID をカスタムの場所に関連付けるときは、`update` コマンドを使用します。 `--cluster-extension-ids`、`--tags`、`assign-identity` を更新できます。 

```azurecli
    az customlocation update -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
```
**必須のパラメーター**

| パラメーター名 | 説明 |
|----------------|------------|
| `--name, --n` | カスタムの場所の名前 |
| `--resource-group, --g` | カスタムの場所のリソース グループ  | 
| `--namespace` | 作成されるカスタムの場所にバインドされたクラスター内の名前空間 |
| `--host-resource-id` | Azure Arc 対応 Kubernetes クラスター (接続されたクラスター) の Azure Resource Manager 識別子 |

**省略可能なパラメーター**

| パラメーター名 | 説明 |
|--------------|------------|
| `--assign-identity` | [システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) をカスタムの場所に割り当てる場合は、`None` または `"SystemAssigned` に更新できます |
| `--cluster-extension-ids` | 接続されたクラスターにインストールされているクラスター拡張機能インスタンスの Azure Resource Manager 識別子を指定して、新しいクラスター拡張機能をこのカスタムの場所に関連付けます。 クラスター拡張機能の ID をスペースで区切って指定します |
| `--tags` | 既存のタグに加えて、新しいタグを追加します。タグのスペース区切りのリスト: key[=value] [key[=value] ...]。 |

## <a name="patch-a-custom-location"></a>カスタムの場所にパッチを適用する

既存のタグとクラスター拡張機能 ID を新しいタグとクラスター拡張 ID に置き換える場合は、`patch` コマンドを使用します。 `--cluster-extension-ids`、`assign-identity`、`--tags` にパッチを適用できます。 

```azurecli
    az customlocation patch -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
```

**必須のパラメーター**

| パラメーター名 | 説明 |
|----------------|------------|
| `--name, --n` | カスタムの場所の名前 |
| `--resource-group, --g` | カスタムの場所のリソース グループ  | 

**省略可能なパラメーター**

| パラメーター名 | 説明 |
|--------------|------------|
| `--assign-identity` | [システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) をカスタムの場所に割り当てる場合は、`None` または `"SystemAssigned` に更新できます |
| `--cluster-extension-ids` | 接続されたクラスターにインストールされているクラスター拡張機能インスタンスの Azure Resource Manager 識別子を指定して、新しいクラスター拡張機能をこのカスタムの場所に関連付けます。 クラスター拡張機能の ID をスペースで区切って指定します |
| `--tags` | 既存のタグに加えて、新しいタグを追加します。タグのスペース区切りのリスト: key[=value] [key[=value] ...]。 |

## <a name="delete-a-custom-location"></a>カスタムの場所を削除する

 ```azurecli
    az customlocation delete -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
   ```

## <a name="next-steps"></a>次の手順

- [クラスター接続](cluster-connect.md)を使用してクラスターに安全に接続します。
- 拡張機能のインストール、カスタムの場所の作成、App Service Kubernetes 環境の作成に関するエンドツーエンドの手順についての「[Azure Arc 上の Azure App Service](../../app-service/overview-arc-integration.md)」に進みます。 
- [Kubernetes 上の Event Grid](../../event-grid/kubernetes/overview.md) のための Event Grid トピックとイベント サブスクリプションを作成します。
- 現在使用できる [Azure Arc 対応 Kubernetes 拡張機能](extensions.md#currently-available-extensions)の詳細について確認します。
