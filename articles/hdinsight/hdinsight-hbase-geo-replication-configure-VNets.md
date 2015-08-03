<properties 
   pageTitle="2 つの仮想ネットワーク間の VPN 接続の構成 | Microsoft Azure" 
   description="2 つの Azure 仮想ネットワーク間に VPN 接続とドメイン名の解決を構成する方法と、HBase geo レプリケーションを構成する方法について説明します。" 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/08/2015"
   ms.author="jgao"/>

# 2 つの Azure 仮想ネットワーク間の VPN 接続の構成  

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 

Azure 仮想ネットワークのサイト間接続では、VPN ゲートウェイを使用して Ipsec/IKE を使用する安全なトンネルが提供されます。VNet は異なるサブスクリプションおよび異なるリージョンであってもかまいません。マルチサイト構成と VNet 間通信を組み合わせることもできます。Vnet 間接続にはいくつかの理由があります。

- リージョン間の geo 冗長性および geo プレゼンス 
- 特定のリージョン内で強固な分離境界を備えた多層アプリケーション 
- サブスクリプションや組織の境界を越えた通信を Azure 内で実現

詳細については、「[VNet 間の接続の構成](https://msdn.microsoft.com/library/azure/dn690122.aspx)」を参照してください。

次のビデオを参照してください。

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

このチュートリアルは、HBase geo レプリケーションの作成に関する[シリーズ][hdinsight-hbase-replication]の第 1 部です。

- 2 つの仮想ネットワーク間に VPN 接続を構成します (このチュートリアル)
- [仮想ネットワーク用に DNS を構成します][hdinsight-hbase-geo-replication-DNS]
- [HBase geo レプリケーションの構成][hdinsight-hbase-geo-replication]

次の図は、このチュートリアルで作成する 2 つの仮想ネットワークを示したものです。

![HDInsight HBase レプリケーション仮想ネットワークの図][img-vnet-diagram]
 

##前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。

	PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。

		Add-AzureAccount

	Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。

		Select-AzureSubscription <AzureSubscriptionName>


>[AZURE.NOTE]Azure のサービス名と仮想マシン名は一意である必要があります。このチュートリアルで使用する名前は、Contoso-[Azure Service/VM name]-[EU/US] です。たとえば、Contoso-VNet-EU は北ヨーロッパ データ センターの Azure 仮想ネットワークです。Contoso-DNS-US は East U.S. データ センターの DNS サーバー VM です。独自の名前を使用する必要があります。
 

##2 つの Azure Vnet の作成



**北ヨーロッパに Contoso-VNet-EU という名前の仮想ネットワークを作成するには**

1.	[Azure ポータル][azure-portal]にサインインします。
2.	**[新規]**、**[NETWORK SERVICES]**、**[仮想ネットワーク]**、**[カスタム作成]** の順にクリックします。
3.	次のように入力します。

	- **名前**: Contoso-VNet-EU
	- **場所**: 北ヨーロッパ

		このチュートリアルでは、北ヨーロッパと米国東部のデータセンターを使います。独自のデータセンターを選択することもできます。
4.	次のように入力します。

	- **DNS サーバー**: (空白のままに) 
	
		仮想ネットワーク内での名前解決には、独自の DNS サーバーが必要になります。Azure によって提供される名前解決を使用する状況と独自の DNS サーバーを使用する状況の詳細については、「[名前解決 (DNS)](https://msdn.microsoft.com/library/azure/jj156088.aspx)」を参照してください。VNet 間で名前解決を構成する手順については、「[2 つの Azure 仮想ネットワーク間の DNS の構成][hdinsight-hbase-dns]」を参照してください。
  
	- **ポイント対サイト VPN の構成**: (オフ)

		ポイント対サイトは、このシナリオに適用されません。

 	- **サイト間 VPN の構成**: (オフ)
 	
		米国東部データ センター内の Azure 仮想ネットワークへのサイト対サイト VPN 接続を構成します。
5.	次のように入力します。

	- 	**アドレス空間開始 IP**: 10.1.0.0
	- 	**アドレス空間 CIDR**: /16
	- 	**Subnet-1 開始 IP**: 10.1.0.0
	- 	**Subnet-1 CIDR**: /24

	U.S. 仮想ネットワークとアドレス空間が重なることはできません。

**西ヨーロッパに Contoso-VNet-US という名前の仮想ネットワークを作成するには**

- 前の手順を繰り返して次の値を指定します。

	- **名前**: Contoso-VNet-US
	- **場所**: East US
	 
	- **DNS サーバー**: (空白のままに)
	- **ポイント対サイト VPN の構成**: (オフ)
	- **サイト間 VPN の構成**: (オフ)
	 
	- **アドレス空間開始 IP**: 10.2.0.0
	- **アドレス空間 CIDR**: /16
	- **Subnet-1 開始 IP**: 10.2.0.0
	- **Subnet-1 CIDR**: /24

















##2 つの Vnet 間の VPN 接続の構成

###ローカル ネットワークの作成

VNet 間の構成を作成する場合は、VNet が互いをローカル ネットワーク サイトとして認識するように各 VNet を構成する必要があります。このセクションでは、各 VNet をローカル ネットワークとして構成します。ローカル ネットワークは、対応する VNet と同じ IP アドレス空間を共有します。

![Azure VPN サイト間接続の構成 - Azure ローカル ネットワーク][img-vnet-lnet-diagram]


**Contoso-VNet-EU ネットワーク アドレス空間と一致する Contoso-LNet-EU という名前のローカル ネットワークを作成するには**

1. Azure ポータルで、**[新規]**、**[NETWORK SERVICES]**、**[仮想ネットワーク]**、**[ローカル ネットワークの追加]** の順にクリックします。
3. 次のように入力します。

	- **名前**: Contoso-LNet-EU
	- **VPN デバイスの IP アドレス**: 192.168.0.1 (このアドレスは後で更新されます)

		通常は、VPN デバイスの実際の外部 IP アドレスを使用します。VNet 間の構成の場合は、VPN ゲートウェイ IP アドレスを使用します。2 つの VNet の VPN ゲートウェイをまだ作成していない場合は、任意の IP アドレスを入力して、後で修正します。
4.	次のように入力します。

	- **アドレス空間開始 IP**: 10.1.0.0
	- **アドレス空間 CIDR**: /16
	
	この入力値は、Contoso-VNet-EU に対して以前に指定した範囲と厳密に対応する必要があります。

**Contoso-VNet-US ネットワーク アドレス空間と一致する Contoso-LNet-US という名前のローカル ネットワークを作成するには**

- 前の手順を繰り返して次のパラメーターを指定します。

	- **名前**: Contoso-LNet-US
	- **VPN デバイスの IP アドレス**: 192.168.0.1 (このアドレスは後で更新されます)
	 
	- **アドレス空間開始 IP**: 10.2.0.0
	- **アドレス空間 CIDR**: /16


###VPN ゲートウェイの作成

この構成には 2 つの部分があります。最初にローカル ネットワークに対する VNet サイト間接続を構成した後、動的ルーティング VPN を作成します。VNet 間には、動的ルーティング VPN を使用する Azure VPN ゲートウェイが必要です。Azure 静的ルーティング VPN はサポートされません。

**Contoso-LNet-US に対する Contoso-VNet-EU サイト間接続を構成するには**

1.	Azure ポータルの左側のウィンドウで **[ネットワーク]** をクリックします。
2.	**Contoso-VNet-EU** をクリックします。
3.	**[構成]** タブをクリックします。
4.	**[ローカル ネットワークに接続する]** をオンにします。
5.	**[ローカル ネットワーク]** で、**Contoso-LNet-US** を選択します。
6.	仮想ネットワーク アドレス空間セクションで **[ゲートウェイ サブネットの追加]** をクリックします。
7.	**[保存]** をクリックします。
8.	**[OK]** をクリックして確定します。


**Contoso-VNet-EU の VPN ゲートウェイを作成するには**

1.	Azure ポータルで **[ダッシュボード]** タブをクリックします。
4.	ページ下部の **[ゲートウェイの作成]** をクリックし、**[動的ルーティング]** をクリックします。
5.	**[はい]** をクリックして確定します。ページ上のゲートウェイの図が黄色に変わり、"ゲートウェイを作成しています" と表示されることを確認します。ゲートウェイが作成されるまでに、通常、15 分ぐらいかかります。

	ゲートウェイの状態が "接続中" に変化すると、各ゲートウェイの IP アドレスがダッシュボードに表示されます。各 VNet に対応する IP アドレスを書き留めます。混同しないように注意してください。先ほど [ローカル ネットワーク] の [VPN デバイスの IP アドレス] に指定した仮のアドレスを編集する際は、これらの IP アドレスを使用することになります。

6.	**[ゲートウェイ IP アドレス]** をコピーします。次のセクションでは、これを使用して Contoso-VNet-EU の VPN ゲートウェイ IP アドレスを構成します。

**Contoso-VNet-EU の VPN ゲートウェイを作成するには**

- 前の 2 つの手順を繰り返して、Contoso-LNet-EU に対する Contoso-VNet-US サイト間接続を構成し、Contoso-Vnet-US に対する VPN ゲートウェイを作成します。完了すると、Contoso-VNet-US の VPN ゲートウェイ IP アドレスが作成されます。


### ローカル ネットワークの VPN デバイス IP アドレスの設定
最後のセクションでは、Vnet ごとに VPN ゲートウェイを作成します。VPN ゲートウェイの IP アドレスを作成したので、前に戻ってローカル ネットワークの VPN デバイス IP アドレスを構成できます。

**Contoso-LNet-EU の VPN デバイス IP アドレスを構成するには**

1.	Azure ポータルの左側のウィンドウで **[ネットワーク]** をクリックします。
2.	上部の **[ローカル ネットワーク]** をクリックします。
3.	**Contoso-LNet-EU** をクリックし、下部の **[編集]** をクリックします。
4.	**[VPN デバイスの IP アドレス]** を更新します。これは、VContoso-VNET-EU の [ダッシュボード] タブから取得したアドレスです。
5.	右側のボタンをクリックします。
6.	チェック マークのボタンをクリックします。

**Contoso-LNet-US の VPN デバイス IP アドレスを構成するには**

- 前の手順を繰り返し、Contoso-LNet-US の VPN デバイス IP アドレスを構成します。

###VNet ゲートウェイ キーの設定

Vnet ゲートウェイは、共有キーを使用して仮想ネットワーク間の接続を認証します。Azure ポータルからキーを構成することはできません。PowerShell または .NET SDK を使用する必要があります。

**キーを設定するには**

1. ワークステーションで、**Windows PowerShell ISE** または Windows PowerShell コンソールを開きます。
2. 次のスクリプトのパラメーターを更新して実行します。

		Add-AuzreAccount
		Select-AzureSubscription -[AzureSubscriptionName]
		Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
		Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##VPN 接続の確認 

Vnet に VM をデプロイすることなく、Azure ポータルの VNet ダッシュボード ページの仮想ネットワークのビジュアルな図を使用して、接続の状態を確認できます。

![HDInsight HBase レプリケーション仮想ネットワーク VPN 接続の状態][img-vpn-status]
  



##次のステップ

このチュートリアルでは、2 つの Azure 仮想ネットワーク間に VPN 接続を構成する方法を説明しました。このシリーズの他の 2 つの記事は次のような内容です。

- [2 つの Azure 仮想ネットワーク間の DNS の構成][hdinsight-hbase-geo-replication-dns]
- [HBase geo レプリケーションの構成][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: http://manage.windowsazure.com


[powershell-install]: ../install-configure-powershell



[hdinsight-hbase-replication]: ../hdinsight-hbase-geo-replication/
[hdinsight-hbase-dns]: ../hdinsight-hbase-geo-replication-configure-DNS/


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.LNet.diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.status.png

<!---HONumber=July15_HO4-->