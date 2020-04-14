---
title: 仮想ネットワークに Azure Data Explorer クラスターと DB を作成する
description: この記事では、仮想ネットワークに Azure Data Explorer クラスターを作成する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548895"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>仮想ネットワークに Azure Data Explorer クラスターを作成する

Azure Data Explorer では、仮想ネットワーク (VNet) のサブネットへのクラスターのデプロイがサポートされています。 この機能を使うと、Azure 仮想ネットワークまたはオンプレミスからクラスターにプライベートにアクセスしたり、仮想ネットワーク内のイベント ハブやストレージなどのリソースにアクセスしたり、受信トラフィックと送信トラフィックを制限したりすることができます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-network-security-group-nsg"></a>ネットワーク セキュリティ グループ (NSG) を作成する

[ネットワーク セキュリティ グループ (NSG)](/azure/virtual-network/security-overview) は、VNet 内でネットワーク アクセスを制御する機能を提供します。 Azure Data Explorer には、2 つのエンドポイントHTTPS (443) と TDS (1433) を使用してアクセスできます。 クラスターの管理、監視、および適切な操作のために、これらのエンドポイントへのアクセスを許可するように次の NSG ルールを構成する必要があります。

ネットワーク セキュリティ グループを作成するには:

1. ポータルの左上隅にある **[+ リソースの作成]** ボタンを選択します。
1. *Network Security Group* を検索します。
1. **[Network Security Group]** で、画面の下部にある **[作成]** を選択します。
1. **[ネットワーク セキュリティ グループの作成]** ウィンドウで、次の情報を入力します。

   ![NSG の作成フォーム](media/vnet-create-cluster-portal/network-security-group.png)

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | クラスターに使用する Azure サブスクリプションを選択します。|
    | Resource group | 該当するリソース グループ | 既存のリソース グループを使用するか、新しいリソース グループを作成します。 |
    | 名前 | AzureDataExplorerNsg | リソース グループ内でネットワーク セキュリティ グループ (NSG) を識別する名前を選択します。  |
    | リージョン | *米国西部* | ニーズに最も適したリージョンを選択します。
    | | | |

1. **[確認と作成]** を選択してクラスターの詳細を確認し、 **[作成]** を選択してクラスターをプロビジョニングします。

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

    ![リソースに移動](media/vnet-create-cluster-portal/notification-nsg.png)

1. **[受信セキュリティ規則]** タブで、 **[+ 追加]** を選択します。
1. **[受信セキュリティ規則の追加]** ウィンドウで、次の情報を入力します。

    ![NSG 受信規則の作成フォーム](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **設定** | **推奨値** 
    |---|---|
    | source | ServiceTag
    | 発信元サービス タグ | AzureDataExplorerManagement
    | Source port ranges | *
    | 宛先 | VirtualNetwork
    | 宛先ポート範囲 | *
    | Protocol | TCP
    | アクション | Allow
    | Priority | 100
    | 名前 | AllowAzureDataExplorerManagement
    | | |
    
1. 「[VNet デプロイの依存関係](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)」に従って、受信と送信のすべての依存関係に対し、前の 2 つの手順を繰り返します。 または、送信規則は、ポート 443 と80 で "*インターネット*" を許可する 1 つの規則に置き換えることもできます。
    
    受信と送信の依存関係に対する NSG 規則は次のようになります。

    ![NSG ルール](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>パブリック IP アドレスを作成する

クエリ (エンジン) のパブリック IP アドレスを作成するには:

1. ポータルの左上隅にある **[+ リソースの作成]** ボタンを選択します。
1. *Network Security Group* を検索します。
1. **[パブリック IP アドレス]** で、画面の下部にある **[作成]** を選択します。
1. **[パブリック IP アドレスの作成]** ペインで、次の情報を入力します。
   
   ![パブリック IP の作成フォーム](media/vnet-create-cluster-portal/public-ip-blade.png)

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | IP バージョン | IPv4 | IP のバージョンを選択します。 サポートされているのは IPv4 だけです。|
    | Sku | Standard | クエリ (エンジン) URI エンドポイントには **Standard** が必要です。 |
    | 名前 | engine-pip | リソース グループ内でパブリック IP アドレスを識別する名前を選択します。
    | サブスクリプション | 該当するサブスクリプション | パブリック IPに使用する Azure サブスクリプションを選択します。|
    | Resource group | 該当するリソース グループ | 既存のリソース グループを使用するか、新しいリソース グループを作成します。 |
    | 場所 | *米国西部* | ニーズに最も適したリージョンを選択します。
    | | | |

1. **[作成]** を選択して、パブリック IP アドレスを作成します。

1. インジェスト (データ管理) パブリック IP アドレスを作成するには、同じ手順で次のように選択します 
    * **[SKU]** : Basic
    * **[IP アドレスの割り当て]** :静的

## <a name="create-virtual-network-and-subnet"></a>仮想ネットワークとサブネットを作成する

仮想ネットワークとサブネットを作成するには:

1. ポータルの左上隅にある **[+ リソースの作成]** ボタンを選択します。
1. 「*Virtual Network*」を検索します。
1. **[Virtual Network]** で、画面の下部にある **[作成]** を選択します。
1. **[仮想ネットワークの作成]** ウィンドウで、次の情報を入力します。

   ![仮想ネットワークの作成フォーム](media/vnet-create-cluster-portal/vnet-blade.png)

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | クラスターに使用する Azure サブスクリプションを選択します。|
    | Resource group | 該当するリソース グループ | 既存のリソース グループを使用するか、新しいリソース グループを作成します。 |
    | 名前 | AzureDataExplorerVnet | リソース グループ内で仮想ネットワークを識別する名前を選択します。
    | リージョン | *米国西部* | ニーズに最も適したリージョンを選択します。
    | | | |

    > [!NOTE]
    > 運用ワークロードの場合は、「[VNet でサブネットのサイズを計画する](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet)」に従って、サブネットのサイズを計画します

1. **[確認と作成]** を選択してクラスターの詳細を確認し、 **[作成]** を選択してクラスターをプロビジョニングします。

1. デプロイが完了したら、 **[リソースに移動]** を選択します。
1. **[サブネット]** ブレードに移動し、**default** サブネットを選択します。
    
    ![[サブネット] ブレード](media/vnet-create-cluster-portal/subnets.png)

1. **default** サブネット ウィンドウで次のようにします。
    1. ドロップダウン メニューから **[ネットワーク セキュリティ グループ]** を選択します。 
    1. ネットワーク セキュリティ グループ名を選択します (この例では **AzureDataExplorerNsg**)。 
    1. **および**

    ![サブネットを構成する](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>クラスターの作成

「[クラスターの作成](create-cluster-database-portal.md#create-a-cluster)」で説明されているように、コンピューティング リソースとストレージ リソースの定義済みセットを使用して、Azure リソース グループに Azure Data Explorer クラスターを作成します。

1. クラスターの作成を完了する前に、 **[Azure Data Explorer クラスターの作成]** ウィンドウで **[ネットワーク]** タブを選択し、前のタブで作成したリソースを使用して仮想ネットワークの詳細を指定します。

   ![クラスター VNET 作成フォーム](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | ネットワーク リソースに使用する Azure サブスクリプションを選択します。|
    | Virtual Network | AzureDataExplorerVnet | 前の手順で作成した仮想ネットワークを選択します。
    | Subnet | default | 前の手順で作成したサブネットを選択します。
    | Query Public IP (クエリ パブリック IP) | engine-pip | 前の手順で作成したクエリ パブリック IP を選択します。
    | Data ingestion Public IP (データ インジェスト パブリック IP) | dm-pip | 前の手順で作成したインジェスト パブリック IP を選択します。
    | | | |

1. **[確認と作成]** を選択してクラスターを作成します。
1. デプロイが完了したら、 **[リソースに移動]** を選択します。

Azure Data Explorer クラスターを仮想ネットワークにデプロイするには、「[Azure Data Explorer クラスターを VNet にデプロイする](https://azure.microsoft.com/resources/templates/101-kusto-vnet/)」の Azure Resource Manager テンプレートを使用します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Data Explorer を仮想ネットワークにデプロイする](vnet-create-cluster-portal.md)