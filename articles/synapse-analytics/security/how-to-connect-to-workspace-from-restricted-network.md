---
title: 制限付きネットワークから Synapse Studio ワークスペース リソースに接続する
description: この記事では、制限付きネットワークから Azure Synapse Studio ワークスペース リソースに接続する方法について説明します。
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 5d28b8f2ff3045c9fdf5e8a866419a22bfbc6504
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328628"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>制限付きネットワークから Synapse Studio ワークスペース リソースに接続する

この記事の対象読者は、会社の制限付きネットワークを管理している会社の IT 管理者です。 IT 管理者は、この制限付きネットワーク内の Azure Synapse Studio とワークステーションの間のネットワーク接続を有効にしようとしています。

この記事では、制限付きのネットワーク環境から Azure Synapse ワークスペースに接続する方法について説明します。 

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション** :Azure サブスクリプションをお持ちでない場合は、開始する前に [無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure Synapse ワークスペース** :Synapse Studio をお持ちでない場合は、Azure Synapse Analytics から Synapse ワークスペースを作成します。 ワークスペース名は、次の手順 4 で必要になります。
* **制限付きネットワーク** :制限付きネットワークは、会社の IT 管理者によって管理されています。IT 管理者は、ネットワーク ポリシーを構成するアクセス許可を持っています。 仮想ネットワーク名とそのサブネットは、次の手順 3 で必要になります。



## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>手順 1:制限付きネットワークにネットワーク アウトバウンド セキュリティ規則を追加する

4 つのサービス タグを持つ 4 つのネットワーク アウトバウンド セキュリティ規則を追加する必要があります。 [サービス タグの概要](/azure/virtual-network/service-tags-overview.md)について確認してください。 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (省略可能。 この種類の規則は、データを Microsoft に共有する場合にのみ追加してください。)

以下のような **Azure Resource Manager** アウトバウンド規則。 他の 3 つのルールを作成するときは、 **[宛先サービス タグ]** の値を、ドロップダウンの選択リストからサービス タグ名 **[AzureFrontDoor.Frontend]** 、 **[AzureActiveDirectory]** 、 **[AzureMonitor]** に置き換えます。

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>手順 2:Azure Synapse Analytics (プライベート リンク ハブ) を作成する

Azure portal から Azure Synapse Analytics (プライベート リンク ハブ) を作成する必要があります。 Azure portal を通じて「 **Azure Synapse Analytics (プライベート リンク ハブ)** 」を検索し、必要なフィールドに入力して作成します。 

> [!Note]
> リージョンは、Synapse ワークスペースと同じ場所である必要があります。

![Synapse Analytics プライベート リンク ハブの作成](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-link-endpoint-for-synapse-studio-gateway"></a>手順 3:Synapse Studio ゲートウェイ用のプライベート リンク エンドポイントを作成する

Synapse Studio ゲートウェイにアクセスするには、Azure portal からプライベート リンク エンドポイントを作成する必要があります。 Azure portal を通じて「 **Private Link** 」を検索します。 **[Private Link センター]** で **[プライベート エンドポイントの作成]** を選択し、必要なフィールドに入力して作成します。 

> [!Note]
> リージョンは、Synapse ワークスペースと同じ場所である必要があります。

![Synapse Studio 用のプライベート エンドポイントの作成 1](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

次の **[リソース]** タブで、上記の手順 2 で作成したプライベート リンク ハブを選択します。

![Synapse Studio 用のプライベート エンドポイントの作成 2](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

次の **[構成]** タブで、 
* **[仮想ネットワーク]** に使用する制限付き仮想ネットワーク名を選択します。
* **[サブネット]** に制限付き仮想ネットワークのサブネットを選択します。 
* **[プライベート DNS ゾーンと統合する]** で、 **[はい]** を選択します。

![Synapse Studio 用のプライベート エンドポイントの作成 3](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

プライベート リンク エンドポイントが作成されたら、Synapse Studio Web ツールのサインイン ページにアクセスできます。 ただし、次の手順を完了するまでは、Synapse ワークスペース内のリソースにはアクセスできません。

## <a name="step-4-create-private-link-endpoints-for-synapse-studio-workspace-resource"></a>手順 4:Synapse Studio ワークスペース リソース用のプライベート リンク エンドポイントを作成する

Synapse Studio ワークスペース リソース内のリソースにアクセスするには、 **[対象サブリソース]** に種類が **[Dev]** のプライベート リンク エンドポイントを少なくとも 1 つ、アクセスする Synapse Studio ワークスペース内のリソースに応じて、種類が **[Sql]** または **[SqlOnDemand]** の 2 つのオプションのプライベート リンク エンドポイントを作成する必要があります。 この Synapse Studio ワークスペース用のプライベート リンク エンドポイントの作成は、上記のエンドポイントの作成と似ています。  

**[リソース]** タブにある以下の領域に注目してください。
* **[リソースの種類]** には、 **[Microsoft.Synapse/workspaces]\(Microsoft.Synapse/ワークスペース\)** を選択します。
* **[リソース]** には、前に自身で作成した **ワークスペース名** を選択します。
* **[対象サブリソース]** で、エンドポイントの種類を選択します。
  * **[Sql]** は、SQL プールで SQL クエリを実行するためのものです。
  * **[SqlOnDemand]** は、SQL に組み込まれているクエリを実行するためのものです。
  * **[Dev]** は、Synapse Studio ワークスペース内のその他すべてにアクセスするためのものです。 少なくともこの種類のプライベート リンク エンドポイントを作成する必要があります。

![Synapse Studio ワークスペース用のプライベート エンドポイントの作成](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)

これで、すべてが設定されました。 Synapse Studio ワークスペースのリソースにアクセスできます。

## <a name="next-steps"></a>次のステップ

[マネージド ワークスペースの Virtual Network](./synapse-workspace-managed-vnet.md) の詳細を学習する

[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)の詳細を学習する
