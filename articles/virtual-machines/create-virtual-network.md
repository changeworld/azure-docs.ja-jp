<properties 
	pageTitle="チュートリアル:クラウド専用の仮想ネットワークを作成する" 
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
	ms.date="09/29/2014" 
	ms.author="cherylmc"/>

<h1 id="vnettut1">チュートリアル:Azure でのクラウド専用仮想ネットワークの作成</h1>

このチュートリアルでは、Azure 管理ポータルで 2 つのサブセットを含むクラウド専用 Azure 仮想ネットワークのサンプルを作成する手順について説明します。作成された仮想ネットワークは次のようになります。

![createvnet](./media/create-virtual-network/createVNet_06_VNetExample.png)

たとえば、FrontEndSubnet は Web サーバーに使用でき、BackEndSubnet は SQL Server またはドメイン コントローラーに使用できます。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。目的は、構成の手順を実行して、読者が仮想ネットワークの作成に必要な手順を習得できるようにすることです。特定の構成で機能するクラウド専用の仮想ネットワークを作成するには、「[管理ポータルでのクラウド専用仮想ネットワークの構成](http://msdn.microsoft.com/library/azure/dn631643.aspx)」をご覧ください。仮想ネットワークのデザイン シナリオや詳細情報については、「[仮想ネットワークの概要](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)」をご覧ください。


> [AZURE.NOTE] このチュートリアルでは、仮想ネットワークを組織のネットワークに接続するクロスプレミス構成を作成する手順については説明しません。クロスプレミス接続とサイト間 VPN 接続 (社内の Active Directory または SharePoint への接続) が可能な仮想ネットワークの作成手順を説明するチュートリアルについては、「[チュートリアル:サイト間接続用のクロスプレミス仮想ネットワークの作成](/manage/services/networking/cross-premises-connectivity/)」をご覧ください。


## 目標

このチュートリアルでは、2 つのサブネットを含む基本的な Azure クラウド専用仮想ネットワークの作成方法を学習します。

## 前提条件

*  少なくとも 1 つの Azure サブスクリプションが有効でアクティブな Microsoft アカウント。Azure サブスクリプションをまだ取得していない場合は、[Azure の 1 か月間無料評価版のページ](http://www.windowsazure.com/pricing/free-trial/)で無料評価版にサインアップできます。MSDN サブスクリプションがある場合は、[MSDN、MPN、Bizspark の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) をご覧ください。

## このチュートリアル用の仮想ネットワークの作成

このサンプルのクラウド専用仮想ネットワークを作成するには、以下の手順を実行します。

1. [Azure 管理ポータル](http://manage.windowsazure.com/)にログインします。

2. 画面の左下隅で、**[新規]**、**[ネットワーク サービス]**、**[仮想ネットワーク]** の順にクリックし、**[カスタム作成]** をクリックして構成ウィザードを開始します。

	![][Image1]

3. **[仮想ネットワークの詳細]** ページで、次の情報を入力します。

- **名前 -** 「**YourVirtualNetwork**」と入力します。

- **リージョン -** 仮想ネットワークは指定したリージョンにあるデータ センターに作成されます。パフォーマンスを最高にするには、ドロップダウン リストから属しているリージョンを選択します。
 

	![][Image2]

4. 右下にある次へ進む矢印をクリックします。このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの設定について](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409)」をご覧ください。

5. **[DNS サーバーと VPN 接続]** ページで、右下にある次へ進む矢印をクリックします。Azure はインターネットベースの Azure DNS サーバーを新しい仮想マシンに割り当てます。この仮想マシンはインターネット リソースへのアクセスを許可する、この仮想ネットワークに追加されます。このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの設定について](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409)」の「[DNS サーバーと VPN 接続] ページ」をご覧ください。
	
6.	実際のネットワークと同じように、仮想ネットワークには配置する仮想マシンに割り当てるために、一定範囲の IP アドレス (アドレス空間と呼ばれる) が必要です。仮想ネットワークはサブネットもサポートします。サブネットには仮想ネットワーク アドレス空間から派生する独自のアドレス空間が必要です。このチュートリアルでは、BackEndSubnet と FrontEndSubnet を作成します。**[仮想ネットワーク アドレス空間]** ページで、次の内容を構成します。

	- アドレス空間については、**[CIDR (アドレス数)]** で **[/16 (65535)]** を選択します。

	- サブネットについては、最初の行で既存の名前に「**BackEndSubnet**」と入力し、開始 IP に「**10.0.1.0**」と入力して、**[CIDR (アドレス数)]** で **[/24 (256)]** を選択します。**[サブネットの追加]** をクリックし、名前に「**FrontEndSubnet**」、開始 IP に「**10.0.2.0**」と入力します。
		
	![][Image4]

 仮想ネットワークの図に戻ると、次のアドレス空間が構成されています。
 
	![][Image7]

 仮想ネットワークのアドレス空間は、10.0.0.0/8、172.16.0.0/12 または 192.168.0.0/16 のプライベート アドレス空間内にあり、クラスレス ドメイン間ルーティング (CIDR) 表記 (ネットワークのプレフィックス表記とも呼ばれる) で指定されている必要があります。このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの設定について](http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409)」の「[仮想ネットワーク アドレス空間]ページ」をご覧ください。


7. ページの右下にあるチェックマークをクリックすると、仮想ネットワークの作成が開始されます。仮想ネットワークが作成されると、Azure 管理ポータルの **[ネットワーク]** ページの [状態] に **[作成済み]** と表示されます。  

	![][Image5]

Azure インフラストラクチャ サービスについてさらに学習するには、以下をご覧ください。

- [カスタム仮想マシンの作成方法](http://www.windowsazure.com/manage/windows/how-to-guides/custom-create-a-vm/) - このトピックでは仮想ネットワークに仮想マシンをインストールします。仮想マシンとインストール オプションの詳細については、「[Azure Virtual Machines](http://www.windowsazure.com/manage/windows/)」をご覧ください。

- [Azure の仮想ネットワークでの Active Directory フォレストのインストール](http://www.windowsazure.com/manage/services/networking/active-directory-forest/) - このトピックでは、他のネットワークに接続しない、新しい Windows Server Active Directory (AD) フォレストをインストールします。このチュートリアルでは、新しいフォレストのインストール用に仮想マシン (VM) を作成する具体的な手順について説明します。このチュートリアルに進む場合は、管理ポータルを使用して VM を作成しないでください。詳細については、「[Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)」をご覧ください。

この仮想ネットワークを削除するには、選択して **[削除]**、**[はい]** の順にクリックします。

特定の構成で機能するクラウド専用の仮想ネットワークを作成する準備ができたら、「[管理ポータルでのクラウド専用仮想ネットワークの構成](http://msdn.microsoft.com/library/azure/dn631643.aspx)」をご覧ください。 

仮想ネットワークのデザイン シナリオや詳細情報については、「[仮想ネットワークの概要](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)」をご覧ください。 

仮想ネットワーク構成のその他の手順と設定については、「[仮想ネットワークの構成タスク](http://go.microsoft.com/fwlink/?linkid=296652&clcid=0x409)」をご覧ください。


## 関連項目

-  [仮想ネットワーク FAQ](http://go.microsoft.com/fwlink/?LinkId=296650)

-  [仮想ネットワークの構成タスク](http://go.microsoft.com/fwlink/?LinkId=296652)

-  [ネットワーク構成ファイルを使用した仮想ネットワークの構成](http://msdn.microsoft.com/library/windowsazure/jj156097.aspx)

-  [名前解決 (DNS)](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png


<!--HONumber=47-->
 