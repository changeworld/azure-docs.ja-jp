Azure ポータルを使用して、上記のシナリオに基づいた VNet を作成するには、次の手順に従います。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。

2. [**新規**] > [**ネットワーク**] > [**仮想ネットワーク**] の順にクリックしてから、[**デプロイメント モデルの選択**] の一覧にある [**リソース マネージャー**]、[**作成**] の順にクリックします。

3. [**仮想ネットワークの作成**] ブレードで、次の図に示すように、VNet の設定を構成します。

	![仮想ネットワーク ブレードの作成](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. [**リソース グループ**] をクリックし、VNet を追加するリソース グループを選択してから [**新規作成**] をクリックして VNet を新しいリソース グループに追加します。次の図は、**TestRG** という新しいリソース グループのリソース グループ設定を示しています。リソース グループの詳細については、[Azure リソース マネージャーの概要](resource-group-overview.md/#resource-groups)を参照してください。

	![リソース グループ](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. 必要に応じて、VNet の [**サブスクリプション**] と [**場所**] の設定を変更してください。

6. [**作成**] をクリックすると、次の図に示すような [**仮想ネットワークの作成**] という名前のタイルが表示されます。

	![仮想ネットワーク タイルの作成](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

7. VNet が作成されるのを待ってから、[**仮想ネットワーク**] ブレードで、[**すべての設定**] > [**サブネット**] > [**追加**] の順にクリックします。

8. 次に示すように、*BackEnd* サブネットのサブネット設定を指定し、[**OK**] をクリックします。

	![サブネット設定](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

9. サブネットの一覧の表示

	![VNet 内のサブネットの一覧](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

<!---HONumber=AcomDC_0107_2016-->