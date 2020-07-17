---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 8d13d5d93dba66fa0dce08ffbf569164fb03398d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466830"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Azure ポータルで従来の VNet を作成する方法
前述のシナリオに基づいて従来の VNet を作成するには、次の手順に従います。

1. ブラウザーで https://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. **[リソースの作成]**  >  **[ネットワーク]**  >  **[仮想ネットワーク]** の順にクリックします。 **[デプロイ モデルの選択]** の一覧に **[クラシック]** が既に表示されています。 3. 次の図のように **[作成]** をクリックします。
   
    ![Azure ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. **[仮想ネットワーク]** ウィンドウで、VNet の **[名前]** を入力してから **[アドレス空間]** をクリックします。 VNet と最初のサブネットのアドレス空間の設定を構成してから **[OK]** をクリックします。 次の図では、このシナリオでの CIDR ブロック設定を示しています。
   
    ![[アドレス空間] ウィンドウ](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. **[リソース グループ]** をクリックし、VNet を追加するリソース グループを選択してから **[新しいリソース グループを作成する]** をクリックして VNet を新しいリソース グループに追加します。 次の図は、**TestRG**という新しいリソース グループのリソース グループ設定を示しています。 リソース グループの詳細については、「 [Azure リソース マネージャーの概要](../articles/azure-resource-manager/management/overview.md#resource-groups)」を参照してください。
   
    ![[リソース グループの作成] ウィンドウ](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. 必要に応じて、VNet の **[サブスクリプション]** と **[場所]** の設定を変更します。 
7. VNet を**スタート画面**にタイルとして表示しない場合は、 **[スタート画面にピン留めする]** を無効にします。 
8. **[作成]** をクリックすると、次の図に示す **[仮想ネットワークの作成]** という名前のタイルが表示されます。
   
    ![ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. VNet が作成されるのを待ち、タイルが表示されたら、クリックしてサブネットを追加します。
   
    ![ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. 次に示すように、VNet の **[構成]** が表示されます。 
   
    ![ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. **[サブネット]**  >  **[追加]** の順にクリックしてからサブネットの **[名前]** を入力し、 **[アドレス範囲 (CIDR ブロック)]** を指定して **[OK]** をクリックします。 次の図は、このシナリオの設定を示しています。
    
    ![Azure ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

