---
title: Azure Arc 対応 Kubernetes 上のカスタムの場所
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions
description: カスタムの場所を使用して、Azure Arc 対応 Kubernetes クラスターに Azure PaaS サービスをデプロイします
ms.openlocfilehash: ddda6420acd7126cb46b043f5c1bce67758342bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450868"
---
# <a name="custom-locations-on-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes 上のカスタムの場所

Azure の場所の拡張としての "*カスタムの場所*" を使用すると、テナント管理者は、Azure サービス インスタンスをデプロイするターゲットの場所として、Azure Arc 対応 Kubernetes クラスターを使用することができます。 Azure のリソースの例としては、Azure Arc 対応 SQL Managed Instance や Azure Arc 対応 PostgreSQL Hyperscale があります。

Azure の場所と同様に、カスタムの場所にアクセスできるテナント内のエンド ユーザーは、会社のプライベート コンピューティングを使用してリソースをそこにデプロイできます。

この機能の概念的な概要については、「[カスタムの場所 - Azure Arc 対応 Kubernetes](conceptual-custom-locations.md)」を参照してください。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件

- バージョン 2.16.0 以降の [Azure CLI をインストールするか、それにアップグレードします](https://docs.microsoft.com/cli/azure/install-azure-cli)。

- `connectedk8s` (バージョン 1.1.0 以降)、`k8s-extension` (バージョン 0.2.0 以降)、`customlocation` (バージョン 0.1.0 以降) Azure CLI 拡張機能。 次のコマンドを実行して、これらの Azure CLI 拡張機能をインストールします。
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    `connectedk8s`、`k8s-extension`、`customlocation` 拡張機能が既にインストールされている場合は、次のコマンドを使用して最新バージョンに更新できます。

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- `Microsoft.ExtendedLocation` のプロバイダーの登録が完了しています。
    1. 次のコマンドを入力します。
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. 登録プロセスを監視します。 登録には最大で 10 分かかる場合があります。
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

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

## <a name="create-custom-location"></a>カスタムの場所を作成する

1. Azure Arc 対応 Kubernetes クラスターを作成する。
    - クラスターをまだ接続していない場合は[クイックスタート](quickstart-connect-cluster.md)を使用します。
    - バージョン 1.1.0 以降に [エージェントをアップグレードします](agent-upgrade.md#manually-upgrade-agents)。

1. 最終的にインスタンスが必要になる Azure サービスのクラスター拡張機能を、カスタムの場所にデプロイします。

    ```azurecli
    az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
    ```

    > [!NOTE]
    > Arc 対応 Data Services クラスター拡張機能では、認証を使用しない送信プロキシと、基本認証を使用する送信プロキシがサポートされています。 現在、信頼できる証明書が必要な送信プロキシはサポートされていません。

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

> [!div class="nextstepaction"]
> [クラスター接続](cluster-connect.md)を使用してクラスターに安全に接続する