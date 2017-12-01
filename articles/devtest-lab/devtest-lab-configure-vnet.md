---
title: "Azure DevTest Labs で仮想ネットワークを構成する | Microsoft Docs"
description: "既存の仮想ネットワークとサブネットを構成し、それらを VM で Azure DevTest Labs と共に使用する方法について説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: tarcher
ms.openlocfilehash: 7048d5d1054906d712ac0422dacfe1ccca97fcc0
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2017
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Azure DevTest Labs で仮想ネットワークを構成する
[ラボへの VM の追加](devtest-lab-add-vm.md)に関する記事で説明しているように、ラボで VM を作成する際に、構成されている仮想ネットワークを指定できます。 たとえば、ExpressRoute またはサイト間 VPN で構成されている仮想ネットワークを使用して、VM から企業ネットワーク リソースにアクセスしなければならない場合があります。

この記事では、VM の作成時に選択できるように、ラボの Virtual Network の設定に既存の仮想ネットワークを追加する方法を説明します。

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Azure ポータルを使用してラボ用の仮想ネットワークを構成する
次に、ラボに既存の仮想ネットワーク (およびサブネット) を追加し、同じラボで VM を作成するときにそれを使用できるようにするための手順を説明します。 

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で目的のラボを選択します。 
1. ラボのメイン ウィンドウで、**[構成とポリシー]** を選択します。

    ![ラボの構成とポリシーにアクセスする](./media/devtest-lab-configure-vnet/policies-menu.png)
1. **[外部リソース]** セクションで **[仮想ネットワーク]** を選択します。 現在のラボに構成した仮想ネットワークのほか、ラボ用に作成される既定の仮想ネットワークの一覧が表示されます。 
1. **[+ 追加]**を選択します。
   
    ![既存の仮想ネットワークをラボに追加する](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. **[仮想ネットワーク]** ウィンドウで、**[仮想ネットワークの選択]** を選択します。
   
    ![既存の仮想ネットワークを選択する](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. **[仮想ネットワークの選択]** ウィンドウで、目的の仮想ネットワークを選択します。 サブスクリプションでラボと同じリージョンの下にある仮想ネットワークの一覧が表示されます。
1. 仮想ネットワークを選択すると、**[仮想ネットワーク]** ウィンドウに戻ります。 下部の一覧からサブネットを選択します。

    ![サブネットの一覧](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    [ラボ サブネット] ウィンドウが表示されます。

    ![[ラボ サブネット] ウィンドウ](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - **ラボ サブネット名**を指定します。
   - ラボ VM の作成にサブネットを使用できるようにするには、**[仮想マシンの作成に使用]** を選択します。
   - [共有パブリック IP アドレス](devtest-lab-shared-ip.md)を有効にするには、**[Enable shared public IP (共有パブリック IP を有効にする)]** を選択します。
   - サブネットでパブリック IP アドレスを許可するには、**[Allow public IP creation (パブリック IP の作成を許可する)]** を選択します。
   - **[ユーザーあたりの最大仮想マシン数]** フィールドに、サブネットごとのユーザーあたりの最大 VM 数を指定します。 無制限の VM 数が必要な場合は、このフィールドを空白のままにします。
1. **[OK]** を選択して、[ラボ サブネット] ウィンドウを閉じます。
1. **[保存]** を選択して、[仮想ネットワーク] ウィンドウを閉じます。

仮想ネットワークが構成されたので、VM の作成時にそれを選択できます。 VM の作成方法および仮想ネットワークの指定方法については、「 [アーティファクトを含む VM をラボに追加する](devtest-lab-add-vm-with-artifacts.md)」を参照してください。 

Azure の [Virtual Network ドキュメント](https://docs.microsoft.com/azure/virtual-network)では、VNet を設定および管理して、オンプレミスの ネットワークに接続する方法など、VNet を使用する方法について詳しく説明しています。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
ラボに目的の仮想ネットワークを追加したら、次は [ラボに VM を追加](devtest-lab-add-vm-with-artifacts.md)します。

