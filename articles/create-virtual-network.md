<properties linkid="manage-services-create-a-virtual-network" urlDisplayName="仮想ネットワークの作成" pageTitle="仮想ネットワークの作成 - Windows Azure サービス管理" metaKeywords="" description="Windows Azure の仮想ネットワークの作成方法について説明します。" metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Windows Azure での仮想ネットワークの作成" authors=""  solutions="" writer="" manager="" editor=""  />





<h1 id="vnettut1">Windows Azure での仮想ネットワークの作成</h1>

このチュートリアルでは、Windows Azure の管理ポータルを使用して基本的な Windows Azure の仮想ネットワークを作成する手順について説明します。Windows Azure の仮想ネットワークの詳細については、「[仮想ネットワーク](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)」を参照してください。

このチュートリアルは、Windows Azure を使用した経験がない読者を対象に作成されています。目的は、読者が仮想ネットワークの作成に必要な手順を習得できるようにすることです。仮想ネットワークのデザイン シナリオや詳細情報については、「[仮想ネットワーク](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)」を参照してください。

このチュートリアルを完了すると、Windows Azure のサービスと仮想マシンを展開できる仮想ネットワークが作成されます。

<div class="dev-callout"> 
<b>注</b> 
<p>このチュートリアルでは、クロスプレミス構成を作成する手順については説明しません。サイト間クロスプレミス接続 (社内の Active Directory または SharePoint への接続) が可能な仮想ネットワークの作成手順を説明するチュートリアルについては、「<a href="/en-us/manage/services/networking/cross-premises-connectivity/">Create a Virtual Network for Site-to-Site Cross-Premises Connectivity (サイト間クロスプレミス接続用の仮想ネットワークの作成)</a>」を参照してください。</p> 
</div>

仮想ネットワーク構成のその他の手順と設定については、「[Windows Azure の仮想ネットワークの構成タスク](http://go.microsoft.com/fwlink/?LinkId=296652)」を参照してください。

AD DS を Windows Azure の仮想マシンに展開する方法に関するガイダンスについては、「[Windows Azure の仮想マシンでの Windows Server Active Directory の展開ガイドライン](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx)」を参照してください。

##  目標

このチュートリアルでは、次の事項について説明します。

*  Windows Azure のクラウド サービスおよび仮想マシンを追加できる基本的な Windows Azure の仮想ネットワークを設定する方法。

##  前提条件

*  少なくとも 1 つのサブスクリプションが有効でアクティブな Windows Live アカウント。

##  仮想ネットワークの作成

**クラウド専用の仮想ネットワークを作成するには: **

1.	[Windows Azure の管理ポータル](http://manage.windowsazure.com/)にログインします。

2. 画面の左下隅で **[新規]** をクリックします。ナビゲーション ウィンドウで、**[ネットワーク]**、**[仮想ネットワーク]** の順にクリックします。**[カスタム作成]** をクリックして、構成ウィザードを開始します。

	![][Image1]

3. **[仮想ネットワークの詳細]** ページで、次の情報を入力し、右下にある次へ進む矢印をクリックします。詳細ページの設定の詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkID=248092">管理ポータルでの仮想ネットワークの構成について</a>」の「**[仮想ネットワークの詳細]** ページ」を参照してください。

- **[名前] -** 仮想ネットワークの名前を指定します。「*YourVirtualNetwork*」と入力します。

- **[アフィニティ グループ] -** ドロップダウン リストから **[新しいアフィニティ グループの作成]** を選択します。アフィニティ グループでは、Windows Azure サービスを同じデータ センターに物理的にグループ化してパフォーマンスを向上させます。1 つのアフィニティ グループに割り当てることができる仮想ネットワークは 1 つのみです。

- **[リージョン] -** ドロップダウン リストからリージョンを選択します。仮想ネットワークは、指定したリージョンにあるデータ センターに作成されます。

- **[アフィニティ グループ名] -** 新しいアフィニティ グループの名前を指定します。「*YourAffinityGroup*」と入力します。

	![][Image2]

4. **[DNS サーバーおよび VPN 接続]** ページで、次の情報を入力し、右下にある次へ進む矢印をクリックします。このページの設定の詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkID=248092">管理ポータルでの仮想ネットワークの構成について</a>」の「**[DNS サーバーおよび VPN 接続]** ページ」を参照してください。

	- **[DNS サーバー] (省略可能) -** 使用する DNS サーバー名と IP アドレスを入力します。この設定では DNS サーバーは作成されず、既存の DNS サーバーが参照されます。

		<div class="dev-callout"> 
		<b>注</b> 
		<p>パブリック DNS サービスを使用する場合は、該当する情報をこの画面で入力できます。それ以外の場合は、名前解決には既定で Windows Azure サービスが使用されます。詳細については、「<a href="http://go.microsoft.com/fwlink/?linkid=248097">名前解決</a>」を参照してください。</p> 
		</div>

	- **ポイント対サイト接続またはサイト間接続のチェック ボックスをオンにしないでください**。このチュートリアルで作成する仮想ネットワークは、クロスプレミス接続用にデザインされていません。

	![][Image3]

5.	**[仮想ネットワーク アドレス空間]** ページで、次の情報を入力し、右下にあるチェックマークをクリックしてネットワークを構成します。アドレス空間は、RFC 1918 で定義されたプライベート アドレスの範囲 (CIDR 表記では 10.0.0.0/8、172.16.0.0/12、または 192.168.0.0/16) 内にあることが必要です。このページの設定の詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkID=248092">管理ポータルでの仮想ネットワークの構成について</a>」の「**[仮想ネットワーク アドレス空間]** ページ」を参照してください。

	- **[アドレス空間]:** 右上隅にある [CIDR] をクリックし、次の情報を入力します。

		- **[開始 IP]:** 10.4.0.0

		- **[CIDR]:** /16

	- **[サブネットの追加]:** 次の情報を入力します。

		- **[Subnet-1]** を「*FrontEndSubnet*」に変更して、開始 IP を「 *10.4.2.0/24*」に設定し、**[サブネットの追加]** をクリックします。

		- **サブネット**「*BackEndSubnet*」を開始 IP「*10.4.3.0/24*」で作成します。

		- 2 つのサブネットが作成されたことを確認し、右下にあるチェックマークをクリックして仮想ネットワークを作成します。

	![][Image4]

6. チェックマークをクリックすると、仮想ネットワークの作成が開始されます。仮想ネットワークが作成されると、管理ポータルのネットワーク ページの **[状態]** に **[作成済み]** と表示されます。

	![][Image5]

7.	仮想ネットワークが作成されたら、次のチュートリアルに進むことができます。

	- <a href="/en-us/manage/services/networking/add-a-vm-to-a-virtual-network/">Add a Virtual Machine to a Virtual Network (仮想ネットワークへの仮想マシンの追加)</a> – この基本的なチュートリアルを使用して、仮想マシンを仮想ネットワークにインストールします。

	- 仮想マシンとインストール オプションの詳細については、「<a href="/en-us/manage/windows/how-to-guides/custom-create-a-vm/">How to Create a Custom Virtual Machine (カスタム仮想マシンの作成方法)</a>」および「<a href="/en-us/manage/windows/">Windows 仮想マシン</a>」を参照してください。

	- <a href="/en-us/manage/services/networking/active-directory-forest/">Install a new Active Directory forest in Windows Azure (Windows Azure での新しい Active Directory フォレストのインストール)</a> - このチュートリアルでは、他のネットワークに接続せずに新しい Active Directory フォレストをインストールします。このチュートリアルでは、新しいフォレストのインストール用に仮想マシン (VM) を作成する具体的な手順について説明します。このチュートリアルに進む場合は、管理ポータルを使用して VM を作成しないでください。

## 関連項目

-  [仮想ネットワーク](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)

-  [Windows Azure の仮想ネットワーク FAQ](http://go.microsoft.com/fwlink/?LinkId=296650)

-  [Windows Azure の仮想ネットワークの構成タスク](http://go.microsoft.com/fwlink/?LinkId=296652)

-  [ネットワーク構成ファイルを使用した仮想ネットワークの構成](http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx)

-  [名前解決](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png



