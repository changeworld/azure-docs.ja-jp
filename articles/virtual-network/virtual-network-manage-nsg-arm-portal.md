<properties 
   pageTitle="リソース マネージャーでプレビュー ポータルを使用して NSG を管理する | Microsoft Azure"
   description="リソース マネージャーでプレビュー ポータルを使用して既存の NSG を管理する方法について説明します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
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
   ms.date="03/14/2016"
   ms.author="telmos" />

# プレビュー ポータルを使用して NSG を管理する

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## 情報を取得する

既存の NSG を表示し、既存の NSG の規則を取得し、NSG が関連付けられているリソースを検索することができます。

### 既存の NSG を表示する
サブスクリプションの既存の NSG をすべて表示するには、次の手順を実行します。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. **[参照]** > **[ネットワーク セキュリティ グループ]** をクリックします。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. **[ネットワーク セキュリティ グループ]** ブレードで NSG の一覧を確認します。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

**RG-NSG** リソース グループの NSG の一覧を表示するには、次の手順を実行します。

1. **[リソース グループ] ** > **[RG-NSG]** > **[...]** をクリックします。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. リソースの一覧で、以下の **[リソース]** ブレードのように NSG アイコンが表示されている項目を探します。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
		 
### NSG のすべての規則を一覧表示する

**NSG-FrontEnd** という NSG の規則を表示するには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。
2. **[設定]** タブの **[受信セキュリティ規則]** をクリックします。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. 次のように **[受信セキュリティ規則]** ブレードが表示されます。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. **[設定]** タブの **[送信セキュリティ規則]** をクリックして、送信規則を表示します。

>[AZURE.NOTE] 既定の規則を表示するには、規則が表示されたブレードの上部にある **[既定の規則]** アイコンをクリックします。

### NSG の関連付けを表示する

**NSG-FrontEnd** NSG が関連付けられているリソースを表示するには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。
2. **[設定]** タブの **[サブネット]** をクリックして、NSG に関連付けられているサブネットを表示します。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. **[設定]** タブの **[ネットワーク インターフェイス]** をクリックして、NSG に関連付けられている NIC を表示します。

## 規則を管理する

既存の NSG に規則を追加し、既存の規則を編集し、規則を削除することができます。

### 規則を追加する

任意のコンピューターから **NSG-FrontEnd** NSG へのポート **443** に対する**受信**トラフィックを許可する規則を追加するには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。
2. **[設定]** タブの **[受信セキュリティ規則]** をクリックします。
3. **[受信セキュリティ規則]** ブレードで **[追加]** をクリックします。**[受信セキュリティ規則の追加]** に次のように値を入力し、**[OK]** をクリックします。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. 数秒後に、**[受信セキュリティ規則]** ブレードに新しい規則が表示されます。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### 規則を変更する

上の手順で作成した規則を、**インターネット**からの受信トラフィックのみを許可する規則に変更するには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。
2. **[設定]** タブで、上で作成した規則をクリックします。
3. **[allow-https]** ブレードで、**[ソース]** プロパティを次のように変更し、**[保存]** をクリックします。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### 規則を削除する

上の手順で作成した規則を削除するには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。
2. **[設定]** タブで、上で作成した規則をクリックします。
3. **[allow-https]** ブレードで、**[削除]** をクリックし、**[はい]** をクリックします。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## 関連付けを管理する

NSG をサブネットと NIC に関連付けることができます。また、NSG とリソースの関連付けを解除することもできます。

### NSG を NIC に関連付ける

**NSG-FrontEnd** NSG を **TestNICWeb1** NIC に関連付けるには、次の手順を実行します。

1. **[ネットワーク セキュリティ グループ]** ブレードまたは上の **[リソース]** ブレードの **[NSG-FrontEnd]** をクリックします。
2. **[設定]** タブの **[ネットワーク インターフェイス]** > **[関連付け]** > **[TestNICWeb1]** をクリックします。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### NSG と NIC の関連付けを解除する

**NSG-FrontEnd** NSG と **TestNICWeb1** NIC の関連付けを解除するには、次の手順を実行します。

1. Azure ポータルで **[リソース グループ]** > **[RG-NSG]** > **[...]** > **[TestNICWeb1]** をクリックします。
2. **[TestNICWeb1]** ブレードで **[セキュリティの変更]** > **[なし]** をクリックします。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] このブレードを使用して、NIC を既存の任意の NSG に関連付けることもできます。

### NSG とサブネットの関連付けを解除する

**NSG-FrontEnd** NSG と **FrontEnd** サブネットの関連付けを解除するには、次の手順を実行します。

1. Azure ポータルで **[リソース グループ]** > **[RG-NSG]** > **[...]** > **[TestVNet]** をクリックします。
2. **[設定]** ブレードで、**[サブネット]** > **[FrontEnd]** > **[ネットワーク セキュリティ グループ]** > **[なし]** をクリックします。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. **[FrontEnd]** ブレードで、**[保存]** をクリックします。

![Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### NSG サブネットへの関連付け

**NSG-FrontEnd** NSG を **FronEnd** サブネットにもう一度関連付けるには、次の手順を実行します。

1. Azure ポータルで **[リソース グループ]** > **[RG-NSG]** > **[...]** > **[TestVNet]** をクリックします。
2. **[設定]** ブレードで、**[サブネット]** > **[FrontEnd]** > **[ネットワーク セキュリティ グループ]** > **[NSG-FrontEnd]** をクリックします。
3. **[FrontEnd]** ブレードで、**[保存]** をクリックします。

>[AZURE.NOTE] NSG の **[設定]** ブレードから、NSG をサブネットに関連付けることもできます。

## NSG の削除

NSG に関連付けられているリソースがない場合にのみ、NSG を削除できます。NSG を削除するには、次の手順を実行します。

1. Azure ポータルで **[リソース グループ]** > **[RG-NSG]** > **[...]** > **[NSG-FrontEnd]** をクリックします。
2. **[設定]** ブレードで、**[ネットワーク インターフェイス]** をクリックします。
3. NIC が表示される場合は、NIC をクリックし、「[NSG と NIC の関連付けを解除する](#Dissociate-an-NSG-from-a-NIC)」の手順 2 を実行します。
4. 各 NIC について手順 3 を繰り返します。
5. **[設定]** ブレードで **[サブネット]** をクリックします。
6. サブネットが表示される場合は、サブネットをクリックし、「[NSG とサブネットの関連付けを解除する](#Dissociate-an-NSG-from-a-subnet)」の手順 2 と 3 を実行します。
7. 左側で **[NSG-FrontEnd]** ブレードまでスクロールし、**[削除]** > **[はい]** をクリックします。

[Azure ポータル - NSG](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## 次のステップ

- NSG の[ログを有効にします](virtual-network-nsg-manage-log.md)。

<!---HONumber=AcomDC_0323_2016-->