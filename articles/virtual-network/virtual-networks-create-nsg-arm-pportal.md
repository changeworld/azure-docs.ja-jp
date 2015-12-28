<properties 
   pageTitle="プレビュー ポータルを使用して ARM モードで NSG を作成する方法 | Microsoft Azure"
   description="プレビュー ポータルを使用して ARM で NSG を作成してデプロイする方法について"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# プレビュー ポータルを使用して NSG を管理する方法

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]この記事では、リソース マネージャーのデプロイ モデルについて説明します。[クラシック デプロイ モデルで NSG を作成](virtual-networks-create-nsg-classic-ps.md)することもできます。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## [デプロイ] をクリックして ARM テンプレートをデプロイする

この時点では、プレビューから、NSG を作成できません。ただし、既存の NSG は管理します。NSG を管理するためには、パブリック リポジトリで使用可能なサンプル テンプレートを使用して、上記のシナリオで説明したリソースを作成します。[このテンプレート](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG)をデプロイして、**[Azure へのデプロイ]** をクリックし、必要に応じて既定のパラメーター値を置き換えて、ポータルの指示に従います。

## 既存の NSG に規則を作成する

プレビュー ポータルから既存の NSG に規則を作成するには、次の手順に従います。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. **[参照]** > **[ネットワーク セキュリティ グループ]** をクリックします。

![プレビュー ポータル - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure1.png)

3. NSG の一覧で、**[NSG-FrontEnd]** > **[受信セキュリティ規則]** をクリックします。

![プレビュー ポータル - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. **[受信セキュリティ規則]** の一覧で、**[追加]** をクリックします。

![プレビュー ポータル - 規則の追加](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. **[受信セキュリティ規則の追加]** ブレードで、ポート *80* の *TCP* 経由で任意のソースから任意の VM にアクセスできるようにする *web-rule* という名前の規則を *200* の優先度で作成して、**[OK]** をクリックします。これらの設定のほとんどは、既定値が設定されています。

![プレビュー ポータル - 規則の設定](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. 数秒後に、NSG の新しい規則が表示されます。

![プレビュー ポータル - 新しい規則](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

<!---HONumber=AcomDC_1217_2015-->