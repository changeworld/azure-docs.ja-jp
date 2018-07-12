---
title: 静的パブリック IP アドレスを持つ VM を作成する - Azure Portal | Microsoft Docs
description: Azure Portal を使用して、静的パブリック IP アドレスを持つ VM を作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 524293f9a1ded73ee7cb6ba4f53208a9f9c54ffa
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38670986"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Azure Portal を使用して静的パブリック IP アドレスを持つ VM を作成する

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (クラシック)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイ モデルの使用方法について取り上げていますが、最新のデプロイでは、クラシック デプロイ モデルではなくこのモデルをお勧めします。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>静的 IP を持つ VM を作成する

Azure Portal で静的パブリック IP アドレスを持つ VM を作成するには、次の手順に従います。

1. ブラウザーから [Azure Portal](https://portal.azure.com) に移動します。必要であれば Azure アカウントでサインインします。
2. ポータルの左上で、**[リソースの作成]**>>**[Compute]**>**[Windows Server 2012 R2 Datacenter]** の順にクリックします。
3. **[デプロイ モデルの選択]** の一覧から **[リソース マネージャー]** を選択し、**[作成]** をクリックします。
4. **[基本]** ウィンドウで、次に示す VM 情報を入力し、**[OK]** をクリックします。
   
    ![Azure ポータル - 基本](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. **[サイズの選択]** ウィンドウで、次に示す **[A1 標準]** をクリックしてから **[選択]** をクリックします。
   
    ![Azure ポータル - サイズの選択](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. **[設定]** ウィンドウで、**[パブリック IP アドレス]** をクリックします。次に、**[パブリック IP アドレスの作成]** ウィンドウで、次に示す **[割り当て]** の下の **[静的]** をクリックします。 **[OK]** をクリックします。
   
    ![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. **[設定]** ウィンドウで **[OK]** をクリックします。
8. 次に示す **[概要]** ウィンドウを確認し、**[OK]** をクリックします。
   
    ![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. ダッシュボードの新しいタイルに注意してください。
   
    ![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. VM が作成されると、**[設定]** ペインが次のように表示されます。
    
    ![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>オペレーティング システム内で IP アドレスを設定する

仮想マシンのオペレーティング システム内で Azure の仮想マシンに割り当てられるパブリック IP アドレスは、手動で割り当てないでください。 VM のオペレーティング システム内で Azure 仮想マシンに割り当てられるプライベート IP は、[Windows VM に複数の IP アドレスを割り当てる](virtual-network-multiple-ip-addresses-portal.md)場合など、必要でない限り静的に割り当てないことをお勧めします。 実際にオペレーティング システム内でプライベート IP アドレスを手動で設定する場合は、それが Azure [ネットワーク インターフェイス](virtual-network-network-interface-addresses.md#change-ip-address-settings)に割り当てられているプライベート IP アドレスと同じアドレスであるようにしてください。そうしないと、仮想マシンへの接続が失われる可能性があります。 詳細については、[プライベート IP アドレス](virtual-network-network-interface-addresses.md#private)設定に関するページを参照してください。

## <a name="next-steps"></a>次の手順

この記事で作成した VM では、すべてのネットワーク トラフィックを送受信できます。 ネットワーク セキュリティ グループ内に受信と送信のセキュリティ規則を定義して、ネットワーク インターフェイスかサブネット、またはその両方で送受信可能なトラフィックを制限できます。 ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループの概要](security-overview.md)に関するページを参照してください。