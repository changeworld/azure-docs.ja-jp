<properties urlDisplayName="Tutorial: Create a Cross-Premises Virtual Network for Site-to-Site Connectivity" pageTitle="チュートリアル: サイト間接続用のクロスプレミス仮想ネットワークの作成 " metaKeywords="" description="このチュートリアルでは、クロスプレミス接続を使う Azure の仮想ネットワークを作成する方法について説明します。" metaCanonical="" services="virtual-network" documentationCenter="" title="Create a Virtual Network for Site-to-Site Cross-Premises Connectivity" authors="cherylmc" solutions="" manager="adinah" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="cherylmc" />





<h1 id="vnettut1">チュートリアル: サイト間接続用のクロスプレミス仮想ネットワークの作成</h1>

このチュートリアルでは、サイト間接続のクロスプレミス仮想ネットワークのサンプルを作成する手順について説明します。

クラウド専用の仮想ネットワークを作成するには、「[チュートリアル: Azure でのクラウド専用仮想ネットワークの作成](http://azure.microsoft.com/ja-jp/documentation/articles/create-virtual-network/)」を参照してください。証明書と VPN クライアントを使用してポイント対サイト VPN を作成する場合は、「[Configure a Point-to-Site VPN in the Management Portal (管理ポータルでのポイント対サイト VPN の構成)](http://go.microsoft.com/fwlink/?LinkId=296653)」を参照してください。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。目的は、読者がクロスプレミス仮想ネットワークのサンプルの作成に必要な手順を習得できるようにすることです。仮想ネットワークのデザイン シナリオや詳細情報については、「[仮想ネットワークの概要](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156007.aspx)」を参照してください。

このチュートリアルを完了すると、クロスプレミス仮想ネットワークのサンプルが作成されます。次の図は、このチュートリアルの設定例に基づいた詳細を示しています。

![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_12_TutorialCrossPremVNet.png)

この図のコピーや、独自のクロスプレミス仮想ネットワークを作成する際に使用できる図については、「[Example cross-premises virtual network figure from tutorial topic (チュートリアル トピックに掲載されたクロスプレミス仮想ネットワーク サンプルの図)](http://gallery.technet.microsoft.com/Example-cross-premises-e5ecb8bb)」を参照してください。

このチュートリアルで用いられる構成設定の例は、組織のネットワーク向けにカスタマイズされていません。したがって、このトピックで説明した構成設定の例を使用して仮想ネットワークやサイト間接続を構成しても機能しません。クロスプレミス仮想ネットワークが機能するよう構成するには、組織の IT 部門やネットワーク管理者に働きかけて、正しい設定を入手する必要があります。詳細については、このトピックの「**前提条件**」セクションを参照してください。

仮想マシンを追加し、内部設置型の Active Directory を Azure の仮想ネットワークに拡張する方法については、次のページを参照してください。

-  [カスタム仮想マシンを作成する方法](http://go.microsoft.com/fwlink/?LinkID=294356)

-  [Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール](http://go.microsoft.com/fwlink/?LinkId=299877)

AD DS を Azure Virtual Machines にデプロイする方法に関するガイダンスについては、「[Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156090.aspx)」を参照してください。

仮想ネットワーク構成のその他の手順と設定については、「[仮想ネットワークの構成タスク](http://go.microsoft.com/fwlink/?LinkId=296652)」を参照してください。

##  目標

このチュートリアルでは、次の事項について説明します。

-  Azure サービスを追加できるクロスプレミス Azure の仮想ネットワークのサンプルを設定する方法。

-  組織のネットワークと通信するように仮想ネットワークを構成する方法。

##  前提条件

-  少なくとも 1 つの Azure サブスクリプションが有効でアクティブな Microsoft アカウント。Azure サブスクリプションがない場合は、[Azure の 1 か月間無料評価版のページ](http://www.windowsazure.com/pricing/free-trial/)で無料評価版にサインアップすることもできます。MSDN サブスクリプションがある場合は、[MSDN サブスクライバー向けの Azure の特典](http://azure.microsoft.com/ja-jp/pricing/member-offers/msdn-benefits-details/)を参照してください。

このチュートリアルを使用して、組織向けにカスタマイズされた実用のクロスプレミス仮想ネットワークを構成する場合は、下記が必要です。

-  仮想ネットワークおよびサブネットのためのプライベート IPv4 アドレス空間 (CIDR 表記)。

-  オンプレミス DNS サーバーの名前および IP アドレス。

-  パブリック IP アドレスを持つ VPN デバイス。このウィザードを完了するには、IP アドレスが必要です。VPN デバイスはネットワーク アドレス変換 (NAT) の背後に配置することはできず、最低のデバイス標準を満たしている必要があります。詳細については、「[仮想ネットワークに使用する VPN デバイスについて](http://go.microsoft.com/fwlink/?LinkID=248098)」を参照してください。 

注: VPN ソリューションの一部として、Windows Server でルーティングとリモート アクセス サービス (RRAS) を使用できます。ただし、このチュートリアルでは RRAS の構成手順については説明しません。 
RRAS の構成情報については、「[Routing and Remote Access Service templates (ルーティングおよびリモート アクセス サービスのテンプレート)](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx)」を参照してください。 

-  IPsec トンネル モード接続用のルーターの構成経験、または構成時に補助する人。

-  オンプレミス ネットワークの到達可能な場所 (ローカル ネットワークとも呼ばれる) をまとめたアドレス空間のセット (CIDR 表記) 


## 手順概要

1.	[仮想ネットワークの作成](#CreateVN)

2.	[ゲートウェイの起動とネットワーク管理者用の情報の収集](#StartGateway)

3.  [VPN デバイスの構成](#ConfigVPN)

##  <a name="CreateVN">仮想ネットワークの作成</a>

会社のネットワークに接続するサンプル仮想ネットワークを作成するには: 

1.	[Azure の管理ポータル](http://manage.windowsazure.com/)にログインします。

2.	画面の左下隅で **[新規]** をクリックします。ナビゲーション ウィンドウで、**[ネットワーク]**、**[仮想ネットワーク]** の順にクリックします。**[カスタム作成]** をクリックして、構成ウィザードを開始します。 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.	**[仮想ネットワークの詳細]** ページで、次の情報を入力し、右下にある次へ進む矢印をクリックします。詳細ページの設定の詳細については、「[管理ポータルでの仮想ネットワークの構成について](http://go.microsoft.com/fwlink/?LinkID=248092)」の **[仮想ネットワークの詳細]** セクションを参照してください。

	-  **名前: **仮想ネットワークの名前を指定します。たとえば、このチュートリアルでは「**YourVirtualNetwork**」と入力します。

	-  **リージョン: **ドロップダウン リストで目的のリージョンを選択します。仮想ネットワークは、指定したリージョンにある Azure データ センターに作成されます。

	
4.	**[DNS サーバーおよび VPN 接続]** ページで、次の情報を入力し、右下にある次へ進む矢印をクリックします。

	<div class="dev-callout"> 
	<b>注</b> 
	<p>このページでは、<b>Point-To-Site</b> 構成と <b>Site-To-Site</b> 構成の両方を同時に選択できます。このチュートリアルでは、<b>[サイト間]</b> 構成だけを選択します。このページの設定の詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkID=248092">管理ポータルでの仮想ネットワークの構成について</a>」の <b>[DNS サーバーおよび VPN 接続]</b> ページを参照してください。</p> 
	</div>

	-  **DNS サーバー: **名前解決に使用する DNS サーバー名と IP アドレスを入力します。通常、これは内部設置型の名前解決に使用する DNS サーバーです。この設定で、DNS サーバーは作成されません。たとえば、このチュートリアルでは名前として「**YourDNS**」、IP アドレスとして「**10.1.0.4**」と入力します。
	-  **ポイント対サイト VPN の構成: **このフィールドは空のままにします。 
	-  **サイト間 VPN の構成: ** チェックボックスをオンにします。
	-  **ローカル ネットワーク: **ドロップダウン リストから **[新しいローカル ネットワークを指定する]** を選択します。
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.	**[サイト間接続]** ページで、次の情報を入力し、ページの右下にあるチェックマークをクリックにします。このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの構成について](http://go.microsoft.com/fwlink/?LinkID=248092)」の **[サイト間接続]** ページ セクションを参照してください。 

	-  **名前: **たとえば、このチュートリアルでは「**YourCorpHQ**」と入力します。

	-  **VPN デバイスの IP アドレス: **たとえば、このチュートリアルでは「**3.2.1.1**」と入力します。または、VPN デバイスのパブリック IP アドレスを入力します。この情報がない場合は、ウィザードの次の手順に進む前にそれを取得する必要があります。VPN デバイスは NAT の背後に配置することはできません。VPN デバイスの詳細については、「[仮想ネットワークに使用する VPN デバイスについて](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156075.aspx)」を参照してください。

	-  **アドレス空間: **たとえば、このチュートリアルでは「**10.1.0.0/16**」と入力します。
	-  **アドレス空間の追加: ** このチュートリアルでは、追加のアドレス空間は必要ありません。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  **[仮想ネットワーク アドレス空間]** ページで、次の情報を入力し、右下にあるチェックマークをクリックしてネットワークを構成します。

	アドレス空間は、RFC 1918 で定義されたプライベート アドレスの範囲 (CIDR 表記では 10.0.0.0/8、172.16.0.0/12、または 192.168.0.0/16) から指定する必要があります。このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの構成について](http://go.microsoft.com/fwlink/?LinkID=248092)」の **[仮想ネットワーク アドレス空間]** ページを参照してください。

	-  **アドレス空間: **たとえば、このチュートリアルでは右上の**[CIDR]** をクリックし、次の情報を入力します。
		-  **開始 IP:** 10.4.0.0
		-  **CIDR:** /16
	-  **サブネットの追加: ** たとえば、このチュートリアルでは次の情報を入力します。
		-  **Subnet-1** を **FrontEndSubnet** に変更して、開始 IP を **10.4.2.0/24** に設定します。
		- サブネット **BackEndSubnet** を、開始 IP **10.4.3.0/24** で追加します。
		-  サブネット **ADDNSSubnet** を、開始 IP **10.4.4.0/24** で追加します。
		-  ゲートウェイ サブネットを、開始 IP **10.4.1.0/24** で追加します。
	-  たとえば、このチュートリアルでは 3 つのサブネットと 1 つのゲートウェイ サブネットが作成されたことを確認し、右下にあるチェックマークをクリックして仮想ネットワークを作成します。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.	チェックマークをクリックすると、仮想ネットワークの作成が開始されます。仮想ネットワークが作成されると、管理ポータルのネットワーク ページの **[状態]** に **[作成済み]** と表示されます。 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

##  <a name="StartGateway">ゲートウェイの起動</a>

Azure Virtual Network を作成した後、サイト間 VPN を作成するために、次の手順で仮想ネットワーク ゲートウェイを構成します。この手順では、最小要件を満たす VPN デバイスが必要です。VPN デバイスとデバイスの構成の詳細については、「[仮想ネットワークに使用する VPN デバイスについて](http://go.microsoft.com/fwlink/?LinkID=248098)」を参照してください。

**ゲートウェイを起動するには: **

1.	仮想ネットワークが作成されると、仮想ネットワークの状態として **[ネットワーク]** ページに **[作成済み]** と表示されます。

	**[名前]** 列で **[YourVirtualNetwork]** (このチュートリアルで作成) をクリックして、ダッシュボードを開きます。
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.	ページの上部にある **[ダッシュボード]** をクリックします。[ダッシュボード] ページで、ページの下部にある **[ゲートウェイの作成]** をクリックします。作成するゲートウェイの種類として、**[動的ルーティング]** または **[静的ルーティング]** を選択します。 

	この仮想ネットワークを、サイト間接続だけでなくポイント対サイト接続にも使用する場合は、ゲートウェイの種類として **[動的ルーティング]** を選択する必要があります。ゲートウェイを作成する前に、VPN デバイスが作成するゲートウェイをサポートすることを確認してください。「[仮想ネットワークに使用する VPN デバイスについて](http://go.microsoft.com/fwlink/?LinkID=248098)」を参照してください。ゲートウェイを作成するかどうかを確認するように求められたら、**[はい]** をクリックします。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.	ゲートウェイの作成が開始されると、ゲートウェイが開始されたことを知らせるメッセージが表示されます。

	ゲートウェイの作成には最大 15 分かかる場合があります。

4.	ゲートウェイの作成後、VPN デバイスの構成で使用する以下の情報を収集する必要があります。

	-  ゲートウェイ IP アドレス
	-  共有キー
	-  VPN デバイス構成スクリプトのテンプレート

	次の手順では、このプロセスについて説明します。

5.	ゲートウェイ IP アドレスを見つけるには: ゲートウェイ IP アドレスは、仮想ネットワークの **[ダッシュボード]** ページにあります。たとえば次のようになります。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.	共有キーを取得するには: 共有キーは、仮想ネットワークの **[ダッシュボード]** ページにあります。画面の下部にある **[キーの管理]** をクリックし、ダイアログ ボックスに表示されたキーをコピーします。このキーは、組織の VPN デバイス上で IPsec トンネルを構成するときに必要です。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.	VPN デバイス構成スクリプト テンプレートをダウンロードするには: ダッシュボードで **[VPN デバイス スクリプトのダウンロード]** をクリックします。

8.	**[VPN デバイス構成スクリプトのダウンロード]** ダイアログ ボックスで、会社の VPN デバイスのベンダー、プラットフォーム、オペレーティング システムを選択します。チェックマークをクリックして、ファイルを保存します。 

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

使用する VPN デバイスがドロップダウン リストに表示されない場合、その他のスクリプト テンプレートについては、MSDN ライブラリの「[仮想ネットワークに使用する VPN デバイスについて](http://go.microsoft.com/fwlink/?LinkID=248098)」を参照してください。


##  <a name="ConfigVPN">VPN デバイスの構成 (ネットワーク管理者)</a>

VPN デバイスごとに異なるため、ここでは大まかな手順を示します。この手順はネットワーク管理者が行う必要があります。

管理ポータルまたは「[仮想ネットワークに使用する VPN デバイスについて](http://go.microsoft.com/fwlink/?LinkId=248098)」から VPN 構成スクリプトを入手できます。これには、使用するルーティング構成と互換性のあるルーティングの種類とデバイスに関する説明も含まれます。

仮想ネットワーク ゲートウェイの構成の詳細については、「[管理ポータルでの仮想ネットワーク ゲートウェイの構成](http://go.microsoft.com/fwlink/?LinkId=299878)」と VPN デバイスのドキュメントを参照してください。

次の前提条件はこの手順で必要になります。

-  VPN デバイスを構成する人は、選択されたデバイスの構成について詳しく知っている。仮想ネットワークと互換性があるデバイスの数と各デバイス ファミリに固有の構成の数が多いので、以下の手順ではデバイス構成について細かく説明しません。そのため、デバイスを構成する人はデバイスとその構成について詳しいことが重要です。 

-  選択したデバイスは、仮想ネットワークと互換性がある。デバイスの互換性については、[ここ](http://go.microsoft.com/fwlink/?LinkID=248098)で確認してください。


**VPN デバイスを構成するには: **

1.	VPN 構成スクリプトを変更します。次の項目を構成します。

a.	セキュリティ ポリシー

	b.	受信トンネル

	c.	送信トンネル

2.	変更した VPN 構成スクリプトを実行して VPN デバイスを構成します。

3.	次のいずれかのコマンドを実行して接続をテストします。

	<table border="1">
	<tr>
	<th>-</th>
	<th>Cisco ASA</th>
	<th>Cisco ISR/ASR</th>
	<th>Juniper SSG/ISG</th>
	<th>Juniper SRX/J</th>
	</tr>
	
	<tr>
	<td><b>メイン モード SA の確認</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get ike cookie</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ike security-association</FONT></td>
	</tr>
	
	<tr>
	<td><b>クイック モード SA の確認</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ipsec security-association</FONT></td>
	</tr>
	</table>


##  次のステップ
内部設置型の Active Directory を、ここで作成した仮想ネットワークに拡張するには、次に示しているチュートリアルに進みます。

-  [カスタム仮想マシンを作成する方法](http://go.microsoft.com/fwlink/?LinkID=294356)

-  [Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール](http://go.microsoft.com/fwlink/?LinkId=299877)

構成をバックアップしたりテンプレートとして使用したりするために、仮想ネットワークの設定をネットワーク構成ファイルにエクスポートする場合は、「[Export Virtual Network Settings to a Network Configuration File (仮想ネットワークの設定のネットワーク構成ファイルへのエクスポート)](http://go.microsoft.com/fwlink/?LinkID=299880)」を参照してください。

## 関連項目

-  [Azure 仮想ネットワーク](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156007.aspx)

-  [仮想ネットワーク FAQ](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [ネットワーク構成ファイルを使用した仮想ネットワークの構成](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156097.aspx)

-  [仮想ネットワークへの仮想マシンの追加](http://www.windowsazure.com/ja-jp/manage/services/networking/add-a-vm-to-a-virtual-network/)

-  [仮想ネットワークの VPN デバイスについて](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156075.aspx)

-  [名前解決 (DNS)](http://go.microsoft.com/fwlink/?LinkId=248097)





