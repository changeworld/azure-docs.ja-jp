## Azure ポータルのネットワーク構成ファイルを使用して VNet を作成する方法

Azure では、xml ファイルを使用して、サブスクリプションに使用可能なすべての VNet を定義します。このファイルをダウンロードし、既存の Vnet が変更または削除されるようにファイルを編集することで、新しい VNet を作成します。このドキュメントでは、ネットワーク構成 (または netcgf) ファイルをダウンロードする方法と、そのファイルを編集して新しい VNet を作成する方法を説明します。ネットワーク構成ファイルの詳細については、[Azure 仮想ネットワークの構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)を参照してください。

Azure ポータルで取得した netcfg ファイルを使用して VNet を作成するには、次の手順に従います。

1. ブラウザーから http://manage.windowsazure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. 以下に示すように、サービスの一覧を下へスクロールし、[**ネットワーク**] をクリックします。

	![Azure 仮想ネットワーク](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. ページ下部にある次のような [**エクスポート**] ボタンをクリックします。

	![[エクスポート] ボタン](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. [**ネットワーク構成のエクスポート**] ページで、仮想ネットワーク構成のエクスポート元となるサブスクリプションを選択してから、ページ左下隅のチェック マーク ボタンをクリックします。
5. ブラウザーの使用手順に従って、**NetworkConfig.xml** ファイルを保存します。ファイルの保存場所を覚えておいてください。
6. 任意の XML またはテキスト エディター アプリケーションを使用して、上記の手順 5. で保存したファイルを開き、**<VirtualNetworkSites>**要素を探します。既に作成したネットワークがある場合、各ネットワークは**<VirtualNetworkSite>**要素として表示されます。
7. このシナリオで説明した仮想ネットワークを作成するには、次の XML を**<VirtualNetworkSites>**要素のすぐ下に追加します。

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

8.  ネットワーク構成ファイルを保存します。
9.  次の図に示すように、Azure ポータルのページ左下隅で、[**新規**]、[**ネットワーク サービス**]、[**仮想ネットワーク**] 、[**構成のインポート**] の順にクリックします。

	![構成のインポート](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  [**ネットワーク構成ファイルをインポートする**] ページで、[**ファイルの参照...**] をクリックしてから、上記の手順 8. でファイルを保存したフォルダーに移動し、そのファイルを選択して [**開く**] をクリックします。この Web ページは、次の図のように表示されます。ページの右下隅にある矢印ボタンをクリックすると、次の手順に移動できます。

	![[ネットワーク構成ファイルのインポート] ページ](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   次の図のように、[**ネットワークを構築しています**] ページに新しい VNet のエントリが表示されます。

	![[ネットワークを構築しています] ページ](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   ページの右下隅にあるチェック マーク ボタンをクリックすると、VNet が作成されます。数秒後、次の図に示すように、作成した VNet が利用可能な VNet の一覧に表示されます。

	![新しい仮想ネットワーク](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)

<!---HONumber=Oct15_HO3-->