---
title: Azure Managed Instance for Apache Cassandra 用に Cosmos DB サービス プリンシパルを追加する方法
description: Azure Managed Instance for Apache Cassandra 用の既存の仮想ネットワークに Cosmos DB サービス プリンシパルを追加する方法について説明します
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06fd7f23db4b4df9b1153ac8b00afb35c265d0be
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858438"
---
# <a name="use-azure-portal-to-add-cosmos-db-service-principal"></a>Azure portal を使用して Cosmos DB サービス プリンシパルを追加する

既存の仮想ネットワークへのデプロイを成功させるために、Azure Managed Instance for Apache Cassandra では、`Microsoft.Network/virtualNetworks/subnets/join/action` アクションを許可するロール (ネットワーク共同作成者など) を持つ Azure Cosmos DB サービス プリンシパルが必要です。 環境によっては、これらのアクセス許可を手動で追加する必要があります。 この記事では、Azure portal を使用してこれを行う方法について説明します。 

## <a name="add-cosmos-db-service-principal"></a>Cosmos DB サービス プリンシパルを追加する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. サブスクリプション内のターゲット仮想ネットワークに移動し、[アクセス制御] タブを選択し、[`add role assignment`] をクリックします。

   :::image type="content" source="./media/add-service-principal/service-principal-1.png" alt-text="ロールの割り当てを追加する" lightbox="./media/add-service-principal/service-principal-1.png" border="true"::: 

1. `Network Contributor` ロールを検索し、これを強調表示し、[`members`] タブを選択します。

   :::image type="content" source="./media/add-service-principal/service-principal-2.png" alt-text="ネットワーク共同作成者を追加する" lightbox="./media/add-service-principal/service-principal-2.png" border="true"::: 

   > [!NOTE]
   > ネットワーク共同作成者のような広範なアクセス許可を持つロールを使用する必要はありません。これは、簡単な例として使用しています。 `Microsoft.Network/virtualNetworks/subnets/join/action` アクションが許可されているのであれば、もっと範囲が狭いアクセス許可を持つカスタマー ロールも作成できます

1. [`Assign access to`] に対して [`User, group, or service principal`] が選択されていることを確認し、[`Select members`] をクリックして `Azure Cosmos DB` サービス プリンシパルを検索します。 右側のウィンドウで、これを選択します。

   :::image type="content" source="./media/add-service-principal/service-principal-3.png" alt-text="Cosmos DB サービス プリンシパルを選択する" lightbox="./media/add-service-principal/service-principal-3.png" border="true"::: 

1. 上部にある [`Review + assign`] タブをクリックし、次に下部にある [`Review + assign`] ボタンをクリックします。 これで、Cosmos DB サービス プリンシパルが割り当てられます。 

   :::image type="content" source="./media/add-service-principal/service-principal-4.png" alt-text="確認して割り当てる" lightbox="./media/add-service-principal/service-principal-4.png" border="true"::: 

## <a name="next-steps"></a>次の手順

この記事では、Managed Cassandra のデプロイを許可するために、適切なロールを持つAzure Cosmos DB サービス プリンシパルを仮想ネットワークに割り当てる方法について説明しました。 Azure Managed Instance for Apache Cassandra の詳細については、次の記事を参照してください。

* [Azure Managed Instance for Apache Cassandra の概要](introduction.md)
* [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)
