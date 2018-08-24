---
title: 仮想ネットワークで Azure Batch プールをプロビジョニングする | Microsoft Docs
description: ファイル サーバーなど、ネットワーク内の他の VM と計算ノードが安全に通信できるように、仮想ネットワークで Batch プールを作成できます。
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 9e8bd6819601cc4436e3432ee390f2ae82a0d94a
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42144946"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>仮想ネットワーク内に Azure Batch プールを作成する


Azure Batch プールを作成すると、指定した [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md) (VNet) のサブネットでプールをプロビジョニングできます。 この記事では、VNet で Batch プールを設定する方法について説明します。 



## <a name="why-use-a-vnet"></a>VNet を使用する理由


Azure Batch プールには、計算ノードが互いに通信する (たとえば、マルチインスタンス タスクを実行する) ことを可能にする設定があります。 これらの設定に別個の VNet は必要ありません。 ただし、既定では、ライセンス サーバーやファイル サーバーなど、Batch プールに含まれない仮想マシンとはノードは通信できません。 プールの計算ノードが他の仮想マシンと、あるいはオンプレミス ネットワークと安全に通信するために、Azure VNet のサブネットでプールをプロビジョニングできます。 



## <a name="prerequisites"></a>前提条件

* **[認証]**:  Azure VNet を使用するには、Batch クライアント API で Azure Active Directory (AD) 認証を使用する必要があります。 Azure AD の Azure Batch のサポートについては、「[Batch サービスの認証に Active Directory を使用する](batch-aad-auth.md)」に記載されています。 

* **Azure VNet**。 1 つまたは複数のサブネットを持つ VNet を前もって用意するために、Azure Portal、Azure PowerShell、Azure コマンドライン インターフェイス (CLI)、その他の方法を利用できます。 Azure Resource Manager ベースの VNet を作成する方法については、[仮想ネットワークの作成](../virtual-network/manage-virtual-network.md#create-a-virtual-network)に関するページを参照してください。 クラシック VNet を作成する方法については、「[複数のサブネットを含んだ仮想ネットワーク (クラシック) を作成する](../virtual-network/create-virtual-network-classic.md)」を参照してください。

### <a name="vnet-requirements"></a>VNet に関する要件
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]
    
## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>ポータルで VNet を含むプールを作成する

VNet を作成し、それにサブネットを割り当てたら、その VNet で Batch プールを作成できます。 次の手順に従って、Azure Portal でプールを作成します。 



1. Azure Portal の Batch アカウントに移動します。 このアカウントは、使用する予定の VNet を含むリソース グループと同じサブスクリプションおよびリージョン内にある必要があります。 
2. 左側の **[設定]** ウィンドウで、**[プール]** メニュー項目を選択します。
3. **[プール]** ウィンドウで、**[追加]** コマンドを選択します。
4. **[プールの追加]** ウィンドウで、**[イメージの種類]** ドロップダウンから、使用する予定のオプションを選択します。 
5. **[Publisher]\(パブリッシャー\)、[Offer]\(プラン\)、[SKU]** で、カスタム イメージに対して適切な値を選択します。
6. **[ノード サイズ]**、**[ターゲットの専用ノード数]**、**[低優先度ノード]** など、残りの必須の設定ほか、オプションの設定も必要に応じて指定します。
7. **[仮想ネットワーク]** で、使用する予定の仮想ネットワークとサブネットを選択します。
  
  ![仮想ネットワークを含むプールを追加する](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>強制トンネリングのユーザー定義ルート

組織には、検査やログ記録目的で、サブネットからオンプレミスの場所にインターネット接続のトラフィックをリダイレクトする (強制的に送る) 要件がある場合があります。 VNet でサブネットの強制トンネリングを既に有効にしている場合もあります。 

強制トンネリングが有効になっている VNet で Azure Batch プールの計算ノードが機能することを確認するには、そのサブネットに次の[ユーザー定義ルート](../virtual-network/virtual-networks-udr-overview.md)を追加する必要があります。

* Batch サービスは、タスクをスケジュールする目的でプールの計算ノードと通信する必要があります。 この通信を有効にするには、Batch アカウントが存在するリージョンで Batch サービスによって利用される IP アドレスごとにユーザー定義ルートを追加します。 Batch サービスの IP アドレスの一覧を取得するには、Azure サポートにお問い合わせください。

* オンプレミス ネットワーク アプライアンス経由の Azure Storage への送信トラフィック (具体的には、フォーム `<account>.table.core.windows.net`、`<account>.queue.core.windows.net`、`<account>.blob.core.windows.net` の URL) がブロックされないようにしてください。

ユーザー定義ルートを追加するとき、関連する各 Batch の IP アドレス プレフィックスのルートを定義し、**[次ホップの種類]** を **[インターネット]** に設定します。 次の例を参照してください。

![ユーザー定義のルート](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>次の手順

- Batch の詳細な概要については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」を参照してください。
- ユーザー定義ルートの作成の詳細については、「[Azure Portal を使用してユーザー定義ルートを作成する](../virtual-network/tutorial-create-route-table-portal.md)」を参照してください。
