<properties 
	pageTitle="チュートリアル: クラウド専用仮想ネットワークの作成" 
	description="このチュートリアルでは、サンプルのクラウド専用 Azure Virtual Network を作成する方法について説明します。" 
	services="virtual-machines, virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="cherylmc"/>

# チュートリアル: Azure でのクラウド専用仮想ネットワークの作成

このチュートリアルでは、Azure 管理ポータルで 2 つのサブセットを含むクラウド専用 Azure Virtual Network のサンプルを作成する手順について説明します。作成された仮想ネットワークは次のようになります。

![createvnet](./media/create-virtual-network/createVNet_06_VNetExample.png)

たとえば、FrontEndSubnet は Web サーバーに使用でき、BackEndSubnet は SQL Server またはドメイン コントローラーに使用できます。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。目的は、構成の手順を実行して、読者が仮想ネットワークの作成に必要な手順を習得できるようにすることです。特定の構成で機能するクラウド専用の仮想ネットワークを作成するには、「[管理ポータルでのクラウド専用仮想ネットワークの構成](http://msdn.microsoft.com/library/azure/dn631643.aspx)」を参照してください。仮想ネットワークのデザイン シナリオや詳細情報については、[Azure Virtual Network の概要](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)に関するページを参照してください。


> [AZURE.NOTE]このチュートリアルでは、仮想ネットワークを組織のネットワークに接続するクロスプレミス構成を作成する手順については説明しません。クロスプレミス接続とサイト間 VPN 接続 (社内の Active Directory または SharePoint への接続) が可能な仮想ネットワークの作成手順を説明するチュートリアルについては、[サイト間クロスプレミス接続用の仮想ネットワークの作成に関するチュートリアル](../virtual-network/virtual-networks-create-site-to-site-cross-premises-connectivity.md)を参照してください。


##  目標

このチュートリアルでは、2 つのサブネットを含む基本的な Azure クラウド専用仮想ネットワークの作成方法を学習します。

##  前提条件

*  少なくとも 1 つの Azure サブスクリプションが有効でアクティブな Microsoft アカウント。Azure サブスクリプションを持っていない場合は、[Azure の無料試用版のページ](http://azure.microsoft.com/pricing/free-trial/)で無料試用版にサインアップすることもできます。MSDN サブスクリプションを持っている場合は、[Microsoft Azure 特別料金: MSDN、MPN、Bizspark の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)をご覧ください。

##  このチュートリアル用の仮想ネットワークの作成

このサンプルのクラウド専用仮想ネットワークを作成するには、以下の手順を実行します。

1. 管理ポータルにログインします。

2. 画面の左下隅で、**[新規]**、**[Network Services]**、**[仮想ネットワーク]** の順にクリックし、**[カスタム作成]** をクリックして構成ウィザードを開始します。

	![][Image1]

3. **[仮想ネットワークの詳細]** ページで、次の情報を入力します。

- **名前 -** 「**YourVirtualNetwork**」と入力します。

- **リージョン -** 仮想ネットワークは指定したリージョンにあるデータ センターに作成されます。パフォーマンスを最高にするには、ドロップダウン リストから属しているリージョンを選択します。
 

	![][Image2]

4. 右下にある次へ進む矢印をクリックします。このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの設定について](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409)」を参照してください。

5. **[DNS サーバーおよび VPN 接続]** ページで、右下にある次へ進む矢印をクリックします。Azure はインターネットベースの Azure DNS サーバーを新しい仮想マシンに割り当てます。この仮想マシンはインターネット リソースへのアクセスを許可する、この仮想ネットワークに追加されます。このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの設定について](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409)」の「[DNS サーバーおよび VPN 接続] ページ」を参照してください。
	
6.	実際のネットワークと同じように、仮想ネットワークには配置する仮想マシンに割り当てるために、一定範囲の IP アドレス (アドレス空間と呼ばれる) が必要です。仮想ネットワークはサブネットもサポートします。サブネットには仮想ネットワーク アドレス空間から派生する独自のアドレス空間が必要です。このチュートリアルでは、BackEndSubnet と FrontEndSubnet を作成します。**[仮想ネットワーク アドレス空間]** ページで、次の内容を構成します。

	- アドレス空間については、**[CIDR (アドレス数)]** で **[/16 (65535)]** を選択します。

	- サブネットについては、最初の行で既存の名前に「**BackEndSubnet**」と入力し、開始 IP に「**10.0.1.0**」と入力して、**[CIDR (アドレス数)]** で **[/24 (256)]** を選択します。**[サブネットの追加]** をクリックし、名前に「**FrontEndSubnet**」、開始 IP に「**10.0.2.0**」と入力します。
		
	![][Image4]

 仮想ネットワークの図に戻ると、次のアドレス空間が構成されています。
 
	
- FrontEndSubnet: 10.0.2.0/24
- BackEndSubnet: 10.0.1.0/24

 このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの設定について](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409)」の「[仮想ネットワーク アドレス空間]ページ」を参照してください。


7. ページの右下にあるチェックマークをクリックすると、仮想ネットワークの作成が開始されます。仮想ネットワークが作成されると、Azure 管理ポータルの **[ネットワーク]** ページの [状態] に **[作成済み]** と表示されます。  

	![][Image5]

Azure インフラストラクチャ サービスについてさらに学習するには、以下を参照してください。

- [カスタム仮想マシンの作成方法](virtual-machines-create-custom.md) - このトピックでは仮想ネットワークに仮想マシンをインストールします。仮想マシンとインストール オプションの詳細については、「[Azure Virtual Machines](http://azure.microsoft.com/documentation/services/virtual-machines/)」を参照してください。

- [Azure Virtual Network での Active Directory フォレストのインストール](../active-directory-new-forest-virtual-machine.md) - このトピックでは、他のネットワークに接続しない、新しい Windows Server Active Directory (AD) フォレストをインストールします。このチュートリアルでは、新しいフォレストのインストール用に仮想マシン (VM) を作成する具体的な手順について説明します。このチュートリアルに進む場合は、管理ポータルを使用して VM を作成しないでください。詳細については、「[Azure Virtual Machines での Windows Server Active Directory の展開ガイドライン](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)」を参照してください。

この仮想ネットワークを削除するには、選択して **[削除]**、**[はい]** の順にクリックします。

特定の構成で機能するクラウド専用の仮想ネットワークを作成する準備ができたら、「[管理ポータルでのクラウド専用仮想ネットワークの構成](http://msdn.microsoft.com/library/azure/dn631643.aspx)」を参照してください。

仮想ネットワークのデザイン シナリオや詳細情報については、[Azure Virtual Network の概要](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)に関するページを参照してください。

仮想ネットワーク構成のその他の手順と設定については、[Azure Virtual Network の構成タスク](http://go.microsoft.com/fwlink/p/?linkid=296652&clcid=0x409)に関するページを参照してください。


## 関連項目

-  [Azure Virtual Network に関する FAQ](http://go.microsoft.com/fwlink/p/?LinkId=296650)

-  [Azure Virtual Network の構成タスク](http://go.microsoft.com/fwlink/p/?LinkId=296652)

-  [ネットワーク構成ファイルを使用した仮想ネットワークの構成](../virtual-network/virtual-networks-using-network-configuration-file.md)

-  [仮想マシンとロール インスタンスの名前解決](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png

 

<!---HONumber=August15_HO6-->