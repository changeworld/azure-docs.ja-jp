<properties linkid="manage-services-create-a-virtual-network" urlDisplayName="Tutorial: Create a cloud-only virtual network" pageTitle="Tutorial: Create a cloud-only virtual network" metaKeywords="" description="Learn how to create an example cloud-only Azure Virtual Network in this tutorial." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Tutorial: Create a Clound-only Virtual Network in Azure" authors="cherylmc" solutions="" manager="adinah" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/29/2014" ms.author="cherylmc"></tags>

# チュートリアル: Azure でのクラウド専用仮想ネットワークの作成

このチュートリアルでは、Azure 管理ポータルで 2 つのサブセットを含むクラウド専用 Azure 仮想ネットワークのサンプルを作成する手順について説明します。作成された仮想ネットワークは次のようになります。

![createvnet][createvnet]

たとえば、FrontEndSubnet は Web サーバーに使用でき、BackEndSubnet は SQL Server またはドメイン コントローラーに使用できます。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。目的は、構成の手順を実行して、読者が仮想ネットワークの作成に必要な手順を習得できるようにすることです。特定の構成で機能するクラウド専用の仮想ネットワークを作成するには、「[管理ポータルでのクラウド専用仮想ネットワークの構成][管理ポータルでのクラウド専用仮想ネットワークの構成]」を参照してください。仮想ネットワークのデザイン シナリオや詳細情報については、「[仮想ネットワークの概要][仮想ネットワークの概要]」を参照してください。

<div class="dev-callout"> 
<b>注</b> 
<p>このチュートリアルでは、仮想ネットワークを組織のネットワークに接続するクロスプレミス構成を作成する手順については説明しません。クロスプレミス接続およびサイト間 VPN 接続 (社内の Active Directory または SharePoint への接続) が可能な仮想ネットワークの作成手順を説明するチュートリアルについては、「<a href="/ja-jp/manage/services/networking/cross-premises-connectivity/">チュートリアル: サイト間接続用のクロスプレミス仮想ネットワークの作成</a>」を参照してください。</p> 
</div>

## 目標

このチュートリアルでは、2 つのサブネットを含む基本的な Azure クラウド専用仮想ネットワークの作成方法を学習します。

## 前提条件

-   少なくとも 1 つの Azure サブスクリプションが有効でアクティブな Microsoft アカウント。Azure サブスクリプションがない場合は、[Azure の 1 か月間無料評価版のページ][Azure の 1 か月間無料評価版のページ]で無料評価版にサインアップすることもできます。MSDN サブスクリプションがある場合は、[MSDN サブスクライバー向けの Azure の特典][MSDN サブスクライバー向けの Azure の特典]を参照してください。

## このチュートリアル用の仮想ネットワークの作成

このサンプルのクラウド専用仮想ネットワークを作成するには、以下の手順を実行します。

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインします。

2.  画面の左下隅で、**[新規]**、**[ネットワーク サービス]**、**[仮想ネットワーク]** の順にクリックし、**[カスタム作成]** をクリックして構成ウィザードを開始します。

    ![][]

3.  **[仮想ネットワークの詳細]** ページで、次の情報を入力します。

-   **名前 -** 「**YourVirtualNetwork**」と入力します。

-   **リージョン -** 仮想ネットワークは指定したリージョンにあるデータ センターに作成されます。パフォーマンスを最高にするには、ドロップダウン リストから属しているリージョンを選択します。

    ![][1]

1.  右下にある次へ進む矢印をクリックします。このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの設定について][管理ポータルでの仮想ネットワークの設定について]」を参照してください。

2.  **[DNS サーバーおよび VPN 接続]** ページで、右下にある次へ進む矢印をクリックします。Azure はインターネットベースの Azure DNS サーバーを新しい仮想マシンに割り当てます。この仮想マシンはインターネット リソースへのアクセスを許可する、この仮想ネットワークに追加されます。このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの設定について][管理ポータルでの仮想ネットワークの設定について]」の「[DNS サーバーおよび VPN 接続] ページ」を参照してください。

3.  実際のネットワークと同じように、仮想ネットワークには配置する仮想マシンに割り当てるために、一定範囲の IP アドレス (アドレス空間と呼ばれる) が必要です。仮想ネットワークはサブネットもサポートします。サブネットには仮想ネットワーク アドレス空間から派生する独自のアドレス空間が必要です。このチュートリアルでは、BackEndSubnet と FrontEndSubnet を作成します。**[仮想ネットワーク アドレス空間]** ページで、次の内容を構成します。

    -   アドレス空間については、**[CIDR (アドレス数)]** で **[/16 (65535)]** を選択します。

    -   サブネットについては、最初の行で既存の名前に「**BackEndSubnet**」と入力し、開始 IP に「**10.0.1.0**」と入力して、**[CIDR (アドレス数)]** で **[/24 (256)]** を選択します。**[サブネットの追加]** をクリックし、名前に「**FrontEndSubnet**」、開始 IP に「**10.0.2.0**」と入力します。

    ![][2]

    仮想ネットワークの図に戻ると、次のアドレス空間が構成されています。

    ![][Image7] 

 仮想ネットワークのアドレス空間は、10.0.0.0/8、172.16.0.0/12 または 192.168.0.0/16 のプライベート アドレス空間内にあり、クラスレス ドメイン間ルーティング (CIDR) 表記 (ネットワークのプレフィックス表記とも呼ばれる) で指定されている必要があります。このページの設定の詳細については、「[管理ポータルでの仮想ネットワークの設定について][管理ポータルでの仮想ネットワークの設定について]」の「[仮想ネットワーク アドレス空間]ページ」を参照してください。

1.  ページの右下にあるチェックマークをクリックすると、仮想ネットワークの作成が開始されます。仮想ネットワークが作成されると、Azure 管理ポータルの **[ネットワーク]** ページの [状態] に **[作成済み]** と表示されます。

    ![][3]

Azure インフラストラクチャ サービスについてさらに学習するには、以下を参照してください。

-   [カスタム仮想マシンの作成方法][カスタム仮想マシンの作成方法] - このトピックでは仮想ネットワークに仮想マシンをインストールします。仮想マシンとインストール オプションの詳細については、「[Azure Virtual Machines][Azure Virtual Machines]」を参照してください。

-   [Azure の仮想ネットワークでの Active Directory フォレストのインストール][Azure の仮想ネットワークでの Active Directory フォレストのインストール] - このトピックでは、他のネットワークに接続しない、新しい Windows Server Active Directory (AD) フォレストをインストールします。このチュートリアルでは、新しいフォレストのインストール用に仮想マシン (VM) を作成する具体的な手順について説明します。このチュートリアルに進む場合は、管理ポータルを使用して VM を作成しないでください。詳細については、「[Azure の仮想マシンでの Windows Server Active Directory の展開ガイドライン][Azure の仮想マシンでの Windows Server Active Directory の展開ガイドライン]」を参照してください。

この仮想ネットワークを削除するには、選択して **[削除]**、**[はい]** の順にクリックします。

特定の構成で機能するクラウド専用の仮想ネットワークを作成する準備ができたら、「[管理ポータルでのクラウド専用仮想ネットワークの構成][管理ポータルでのクラウド専用仮想ネットワークの構成]」を参照してください。

仮想ネットワークのデザイン シナリオや詳細情報については、「[仮想ネットワークの概要][仮想ネットワークの概要]」を参照してください。

仮想ネットワーク構成のその他の手順と設定については、「[仮想ネットワークの構成タスク][仮想ネットワークの構成タスク]」を参照してください。

## 関連項目

-   [仮想ネットワーク FAQ][仮想ネットワーク FAQ]

-   [仮想ネットワークの構成タスク][4]

-   [ネットワーク構成ファイルを使用した仮想ネットワークの構成][ネットワーク構成ファイルを使用した仮想ネットワークの構成]

-   [名前解決 (DNS)][名前解決 (DNS)]

  [createvnet]: ./media/create-virtual-network/createVNet_06_VNetExample.png
  [管理ポータルでのクラウド専用仮想ネットワークの構成]: http://msdn.microsoft.com/library/azure/dn631643.aspx
  [仮想ネットワークの概要]: http://msdn.microsoft.com/library/windowsazure/jj156007.aspx
  [チュートリアル: サイト間接続用のクロスプレミス仮想ネットワークの作成]: /ja-jp/manage/services/networking/cross-premises-connectivity/
  [Azure の 1 か月間無料評価版のページ]: http://www.windowsazure.com/pricing/free-trial/
  [MSDN サブスクライバー向けの Azure の特典]: http://azure.microsoft.com/ja-jp/pricing/member-offers/msdn-benefits-details/
  [Azure 管理ポータル]: http://manage.windowsazure.com/
  []: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
  [1]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
  [管理ポータルでの仮想ネットワークの設定について]: http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409
  [2]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
  [3]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
  [カスタム仮想マシンの作成方法]: http://www.windowsazure.com/ja-jp/manage/windows/how-to-guides/custom-create-a-vm/
  [Azure Virtual Machines]: http://www.windowsazure.com/ja-jp/manage/windows/
  [Azure の仮想ネットワークでの Active Directory フォレストのインストール]: http://www.windowsazure.com/ja-jp/manage/services/networking/active-directory-forest/
  [Azure の仮想マシンでの Windows Server Active Directory の展開ガイドライン]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156090.aspx
  [仮想ネットワークの構成タスク]: http://go.microsoft.com/fwlink/?linkid=296652&clcid=0x409
  [仮想ネットワーク FAQ]: http://go.microsoft.com/fwlink/?LinkId=296650
  [4]: http://go.microsoft.com/fwlink/?LinkId=296652
  [ネットワーク構成ファイルを使用した仮想ネットワークの構成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156097.aspx
  [名前解決 (DNS)]: http://go.microsoft.com/fwlink/?LinkId=248097
  [Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
