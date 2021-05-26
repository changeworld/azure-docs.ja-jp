---
title: Azure Arc 対応 Kubernetes 上のカスタムの場所を作成および管理する
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: カスタムの場所を使用して、Azure Arc 対応 Kubernetes クラスターに Azure PaaS サービスをデプロイします
ms.openlocfilehash: 15309599b12b10344b59d46c47c11dfa243726db
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110367188"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes にカスタムの場所を作成および管理する

Azure の場所の拡張としての "*カスタムの場所*" を使用すると、テナント管理者は、Azure サービス インスタンスをデプロイするターゲットの場所として、Azure Arc 対応 Kubernetes クラスターを使用することができます。 Azure のリソースの例としては、Azure Arc 対応 SQL Managed Instance や Azure Arc 対応 PostgreSQL Hyperscale があります。

Azure の場所と同様に、カスタムの場所にアクセスできるテナント内のエンド ユーザーは、会社のプライベート コンピューティングを使用してリソースをそこにデプロイできます。

この記事では、次のことについて説明します。
> [!div class="checklist"]
> * Azure Arc 対応 Kubernetes クラスター上のカスタムの場所を有効にする。
> * Azure サービス インスタンスの Azure サービス クラスター拡張機能をクラスターにデプロイする。
> * Azure Arc 対応 Kubernetes クラスターにカスタムの場所を作成する。

この機能の概念的な概要については、「[カスタムの場所 - Azure Arc 対応 Kubernetes](conceptual-custom-locations.md)」を参照してください。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

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

- `Microsoft.ExtendedLocation` のプロバイダー登録の完了を確認します。
    1. 次のコマンドを入力します。
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. 登録プロセスを監視します。 登録には最大で 10 分かかる場合があります。
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

- [Azure Arc 対応 Kubernetes に接続されたクラスター](quickstart-connect-cluster.md)が既に存在することを確認します。
    - バージョン 1.1.0 以降に[お使いのエージェントをアップグレードします](agent-upgrade.md#manually-upgrade-agents)。

>[!NOTE]
>**カスタムの場所でサポートされているリージョン:**
>* 米国東部
>* 西ヨーロッパ

## <a name="enable-custom-locations-on-cluster"></a>クラスターでカスタムの場所を有効にする

クラスターでこの機能を有効にするには、次のコマンドを実行します。

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

> [!NOTE]
> 1. カスタムの場所機能は、クラスター接続機能に依存します。 そのため、カスタムの場所を使用するには、両方の機能が有効になっている必要があります。
> 2. 機能を有効にするクラスターを指している `kubeconfig` ファイルが存在するマシン上で、`az connectedk8s enable-features` を実行する必要があります。
> 3. サービス プリンシパルを使用して Azure CLI にログインしている場合は、カスタムの場所機能を有効にする前に、[追加のアクセス許可](troubleshooting.md#enable-custom-locations-using-service-principal)をサービス プリンシパルに付与する必要があります。

## <a name="create-custom-location"></a>カスタムの場所を作成する

1. クラスター上で最終的に必要な、Azure サービス インスタンスの Azure サービス クラスター拡張機能をデプロイします。

    * Azure Arc 対応データ サービス

        ```azurecli
        az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
        ```
        > [!NOTE]
        > Arc 対応 Data Services クラスター拡張機能では、認証を使用しない送信プロキシと、基本認証を使用する送信プロキシがサポートされています。 現在、信頼できる証明書が必要な送信プロキシはサポートされていません。


    * [Azure Arc 上の Azure App Service](../../app-service/overview-arc-integration.md)

        ```azurecli
        az k8s-extension create --name <extensionInstanceName> --extension-type 'Microsoft.Web.Appservice' --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace appservice-ns --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" --configuration-settings "appsNamespace=appservice-ns" 
        ```

    * [Kubernetes 上の Event Grid](/azure/event-grid/kubernetes/overview)

        ```azurecli
          az k8s-extension create --name <extensionInstanceName> --extension-type Microsoft.EventGrid --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace eventgrid-ext --configuration-protected-settings-file protected-settings-extension.json --configuration-settings-file settings-extension.json
        ```

1. Azure Arc 対応 Kubernetes クラスターの Azure Resource Manager 識別子を取得します。これは、後の手順で `connectedClusterId` として参照されます。

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Azure Arc 対応 Kubernetes クラスターにデプロイされているクラスター拡張機能の Azure Resource Manager 識別子を取得します。これは、後の手順で `extensionId` として参照されます。

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Azure Arc 対応 Kubernetes クラスターと拡張機能を参照して、カスタムの場所を作成します。

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>次の手順

- [クラスター接続](cluster-connect.md)を使用してクラスターに安全に接続します。
- 拡張機能のインストール、カスタムの場所の作成、App Service Kubernetes 環境の作成に関するエンドツーエンドの手順についての「[Azure Arc 上の Azure App Service](../../app-service/overview-arc-integration.md)」に進みます。 
- [Kubernetes 上の Event Grid](/azure/event-grid/kubernetes/overview) のための Event Grid トピックとイベント サブスクリプションを作成します。
- 現在使用できる [Azure Arc 対応 Kubernetes 拡張機能](extensions.md#currently-available-extensions)の詳細について確認します。

