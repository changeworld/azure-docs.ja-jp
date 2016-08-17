## Azure ポータルで VNet ピアリングを作成する方法

上記のシナリオに基づいた VNet ピアリングを Azure ポータルから作成するには、次の手順に従います。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. VNET ピアリングを確立するには、2 つの VNet 間で 2 つのリンク (各方向につき 1 つ) を作成する必要があります。まず、VNET1 から VNET2 への VNET ピアリング リンクを作成しましょう。ポータルで **[参照]** をクリックし、**[仮想ネットワーク]** を選択します。

	![Azure ポータルで VNet ピアリングを作成する](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure01.png)

3. [仮想ネットワーク] ブレードで [VNET1] を選択し、[ピアリング]、[追加] の順にクリックします。

	![ピアリングの選択](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure02.png)

4. [ピアリングの追加] ブレードで、ピアリング リンクに「LinkToVnet2」という名前を付け、サブスクリプションとピア仮想ネットワーク (VNET2) を選択して [OK] をクリックします。

	![VNet へのリンク](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure03.png)

5. この VNET ピアリング リンクが作成されると、リンクの状態が次のように表示されます。

	![リンクの状態](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure04.png)

6. 次に VNET2 から VNET1 への VNWT ピアリング リンクを作成します。[仮想ネットワーク] ブレードで [VNET2] を選択し、[ピアリング]、[追加] の順にクリックします。

	![他の VNet からのピア](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure05.png)

7. [ピアリングの追加] ブレードで、ピアリング リンクに「LinkToVnet1」という名前を付け、サブスクリプションとピア仮想ネットワークを選択して [OK] をクリックします。

	![仮想ネットワーク タイルの作成](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure06.png)

8. この VNET ピアリング リンクが作成されると、リンクの状態が次のように表示されます。

	![最終的なリンクの状態](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure07.png)

9. LinkToVnet2 の状態を確認すると、こちらも "接続済み" に変わっています。

	![最終的なリンクの状態 2](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure08.png)

10. 注: VNET ピアリングは両方のリンクが接続されている場合にのみ確立されます。

<!---HONumber=AcomDC_0803_2016-->