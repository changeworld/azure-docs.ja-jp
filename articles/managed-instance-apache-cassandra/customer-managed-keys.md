---
title: Azure Managed Instance for Apache Cassandra カスタマー マネージド キー
description: カスタマー マネージド キー
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 8dc09c009f4254aa7f4149f95fe73bb4d7a406f9
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704105"
---
# <a name="customer-managed-keys---overview"></a>カスタマー マネージド キー - 概要

Azure Managed Instance for Apache Cassandra には、独自のキーを使用してディスク上のデータを暗号化する機能が備えられています。 この記事では、Azure Key Vault でカスタマー マネージド キーを実装する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure Key Vault を使用してシークレットを設定していること。 Azure Key Vault の詳細については、[こちら](../key-vault/secrets/about-secrets.md)を参照してください。
- リソース グループに仮想ネットワークをデプロイし、Azure Cosmos DB サービス プリンシパルをメンバーとして、ネットワーク共同作成者ロールを適用していること。 詳細については、「[Azure CLI を使用して Azure Managed Instance for Apache Cassandra クラスターを作成する](create-cluster-cli.md)」を参照してください。 

> [!IMPORTANT]
> この記事では、Azure CLI バージョン 2.30.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-cluster-with-system-assigned-identity"></a><a id="create-cluster"></a>システム割り当て ID を使用してクラスターを作成する

   > [!NOTE]
   > 前提条件で説明したように、デプロイ エラーを回避するには、マネージド インスタンス クラスターをデプロイする前に、仮想ネットワークに適切なロールが適用されていることを確認します。
   > ```azurecli-interactive  
   >     az role assignment create \
   >     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
   >     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
   >     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   > ```

1. ID の種類を SystemAssigned として指定し、`<subscriptionID>`、`<resourceGroupName>`、`<vnetName>`、および `<subnetName>` を適切な値に置き換えて、クラスターを作成します。

    ```azurecli-interactive    
    subnet="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
    cluster="thvankra-cmk-test-wcus"
    group="thvankra-nova-cmk-test"
    region="westcentralus"
    password="PlaceholderPassword"
    
    az managed-cassandra cluster create \
        --identity-type SystemAssigned \
        --resource-group $group \
        --location $region \
        --cluster-name $cluster \
        --delegated-management-subnet-id $subnet \
        --initial-cassandra-admin-password $password
    ```

1. 作成されたクラスターの ID 情報を取得します。

    ```azurecli-interactive
    az managed-cassandra cluster show -c $cluster -g $group
    ```

    出力には、次のような ID セクションが含まれます。 後で使用するために、`principalId` をコピーします。

    ```shell
      "identity": {
        "principalId": "1aa51c7f-196a-4013-a656-1ccabfdc54e0",
        "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "type": "SystemAssigned"
      }
    ```
 
1. Azure Key Vault で、キーに対するアクセス ポリシーを作成します。

   :::image type="content" source="./media/cmk/key-vault-access-policy-1.png" alt-text="Key Vault アクセス ポリシー 1" lightbox="./media/cmk/key-vault-access-policy-1.png" border="true":::

1. 上記で取得したクラスターの `principalId` に、キー コンテナーに対する `get`、`wrap` および `unwrap` キーのアクセス許可を割り当てます。 ポータルで、クラスターのプリンシパル ID をクラスターの名前で検索することもできます。
 

   :::image type="content" source="./media/cmk/key-vault-access-policy-2.png" alt-text="Key Vault アクセス ポリシー 2" lightbox="./media/cmk/key-vault-access-policy-2.png" border="true":::

   > [!WARNING]
   > キー コンテナーで消去保護が有効になっていることを確認してください。 そうしないと、データセンターのデプロイは失敗します。 

1. `add` をクリックしてアクセス ポリシーを追加した後、必ず保存してください。

   :::image type="content" source="./media/cmk/save.png" alt-text="アクセス ポリシーの保存" lightbox="./media/cmk/key-vault-access-policy-2.png" border="true":::

1. キー識別子を取得するには、自分のキーを選択します。

   :::image type="content" source="./media/cmk/select-key.png" alt-text="キーの選択" lightbox="./media/cmk/key-identifier-1.png" border="true":::

1. 現在のバージョンをクリックします。

   :::image type="content" source="./media/cmk/current-version.png" alt-text="現在のバージョンの選択" lightbox="./media/cmk/key-identifier-1.png" border="true":::

1. 後で使用するためにキー識別子を保存します。

   :::image type="content" source="./media/cmk/key-identifier-2.png" alt-text="キー識別子のステップ 2" lightbox="./media/cmk/key-identifier-1.png" border="true":::


1. 次に示すように、マネージド ディスク (managed-disk-customer-key-uri) とバックアップ ストレージ (backup-storage-customer-key-uri) 暗号化の両方について、`<key identifier>` を同じキー (前の手順でコピーした URI) に置き換えて、データセンターを作成します (前に使用した `subnet` と同じ値を使用します)。 

    ```azurecli-interactive
    managedDiskKeyUri = "<key identifier>"
    backupStorageKeyUri = "<key identifier>"
    group="thvankra-nova-cmk-test"
    region="westcentralus"
    cluster="thvankra-cmk-test-2"
    dc="dc1"
    nodecount=3
    subnet="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        
    az managed-cassandra datacenter create \
        --resource-group $group \
        --cluster-name $cluster \
        --data-center-name $dc \
        --managed-disk-customer-key-uri $managedDiskKeyUri \
        --backup-storage-customer-key-uri $backupStorageKeyUri \
        --node-count $nodecount \
        --delegated-subnet-id $subnet \
        --data-center-location $region \
        --sku Standard_DS14_v2
    ```

1. 次に示すように、ID 情報がない既存のクラスターに ID を割り当てることができます。

    ```azurecli-interactive
    az managed-cassandra cluster update --identity-type SystemAssigned -g $group -c $cluster
    ```

## <a name="rotating-the-key"></a><a id="update-cluster"></a>キーのローテーション

1. キーを更新するコマンドを次に示します。

    ```azurecli-interactive
    managedDiskKeyUri = "<key identifier>"
    backupStorageKeyUri = "<key identifier>"
    
    az managed-cassandra datacenter update \
        --resource-group $group \
        --cluster-name $cluster \ 
        --data-center-name $dc \
        --managed-disk-customer-key-uri $managedDiskKeyUri \
        --backup-storage-customer-key-uri $backupStorageKeyUri
    ```