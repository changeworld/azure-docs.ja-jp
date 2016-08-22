<properties 
   pageTitle="リソース マネージャーで Azure ポータルを使用した静的パブリック IP を持つ VM のデプロイ | Microsoft Azure"
   description="リソース マネージャーで Azure ポータルを使用して、静的パブリック IP を持つ VM をデプロイする方法について説明します"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# Azure ポータルを使用して静的パブリック IP を持つ VM をデプロイする

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] クラシック デプロイメント モデル。

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## 静的 IP を持つ VM を作成する 

Azure ポータルで静的パブリック IP アドレスを持つ VM を作成するには、次の手順に従います。

1. ブラウザーから [Azure ポータル](https://portal.azure.com)に移動します。必要であれば Azure アカウントでサインインします。
2. ポータルの左上隅で、**[新規]**、**[コンピューティング]**、**[Windows Server 2012 R2 Datacenter]** の順にクリックします。
3. **[デプロイ モデルの選択]** の一覧から **[リソース マネージャー]** を選択し、**[作成]** をクリックします。
4. **[基本]** ブレードで、次に示す VM 情報を入力し、**[OK]** をクリックします。

	![Azure ポータル - 基本](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. **[サイズの選択]** ブレードで、次に示す **[A1 標準]** をクリックした後、**[選択]** をクリックします。

	![Azure ポータル - サイズの選択](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. **[設定]** ブレードで、**[パブリック IP アドレス]** をクリックします。次に、**[パブリック IP アドレスの作成]** ブレードで、次に示す **[割り当て]** の下の **[静的]** をクリックします。次に、**[OK]** をクリックします

	![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. **[設定]** ブレードで **[OK]** をクリックします。
8. 次に示す **[概要]** ブレードを見直し、**[OK]** をクリックします。

	![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. ダッシュボードの新しいタイルに注意してください。

	![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. VM が作成されると、次に示す **[設定]** ブレードが表示されます。

	![Azure ポータル - パブリック IP アドレスの作成](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

<!---HONumber=AcomDC_0810_2016-->