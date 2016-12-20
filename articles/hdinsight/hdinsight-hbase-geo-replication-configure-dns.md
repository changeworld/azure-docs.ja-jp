---
title: "2 つの Azure 仮想ネットワーク間の DNS の構成 | Microsoft Docs"
description: "2 つの仮想ネットワーク間に VPN 接続とドメイン名の解決を構成する方法と、HBase geo レプリケーションを構成する方法について説明します。"
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 881f4c60-0cac-481b-aedb-e7c0c9400df1
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/28/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: dc2e5440d923fab0d029d72274c29c3ed9e38a5c


---
# <a name="configure-dns-between-two-azure-virtual-networks"></a>2 つの Azure 仮想ネットワーク間の DNS の構成
> [!div class="op_single_selector"]
> * [VPN 接続の構成](hdinsight-hbase-geo-replication-configure-vnets.md)
> * [DNS の構成](hdinsight-hbase-geo-replication-configure-dns.md)
> * [HBase レプリケーションの構成](hdinsight-hbase-geo-replication.md) 
> 
> 

Azure 仮想ネットワークに DNS サーバーを追加して構成し、仮想ネットワーク内および仮想ネットワーク間の名前解決を処理する方法を説明します。

このチュートリアルは、HBase geo レプリケーションの作成に関する[シリーズ][hdinsight-hbase-geo-replication]の第 2 部です。

* [2 つの仮想ネットワーク間の VPN 接続の構成][hdinsight-hbase-geo-replication-vnet]
* 仮想ネットワーク用に DNS を構成します (このチュートリアル)
* [HBase geo レプリケーションの構成][hdinsight-hbase-geo-replication]

次の図は、[2 つの仮想ネットワーク間の VPN 接続の構成][hdinsight-hbase-geo-replication-vnet]に関するページで作成した 2 つの仮想ネットワークを示したものです。

![HDInsight HBase レプリケーション仮想ネットワークの図][img-vnet-diagram]

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **Azure PowerShell を実行できるワークステーション**。
  
    PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。
  
        Add-AzureAccount
  
    Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。
  
        Select-AzureSubscription <AzureSubscriptionName>
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
* **VPN で接続された 2 つの Azure 仮想ネットワーク**。  方法については、「[2 つの Azure 仮想ネットワーク間の VPN 接続の構成][hdinsight-hbase-geo-replication-vnet]」を参照してください。

> [!NOTE]
> Azure のサービス名と仮想マシン名は一意である必要があります。 このチュートリアルで使用する名前は、Contoso-[Azure Service/VM name]-[EU/US] です。 たとえば、Contoso-VNet-EU は北ヨーロッパ データ センターの Azure 仮想ネットワークです。Contoso-DNS-US は East U.S. データ センターの DNS サーバー VM です。 独自の名前を使用する必要があります。
> 
> 

## <a name="create-azure-virtual-machines-to-be-used-as-dns-servers"></a>DNS サーバーとして使用する Azure 仮想マシンの作成
**Contoso-VNet-EU 内に Contoso-DNS-EU という名前の仮想マシンを作成するには**

1. **[新規]**、**[COMPUTE]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。
2. **[Windows Server 2012 R2 Datacenter]**を選択します。
3. 次のように入力します。
   * **仮想マシン名**: Contoso-DNS-EU
   * **新しいユーザー名**: 
   * **新しいパスワード**: 
4. 次のように入力します。
   
   * **クラウド サービス**: 新しいクラウド サービスの作成
   * **リージョン/アフィニティ グループ/仮想ネットワーク**: (Contoso-VNet-EU を選択)
   * **仮想ネットワーク サブネット**: Subnet-1
   * **ストレージ アカウント**: 自動的に生成されたストレージ アカウントを使用
     
     クラウド サービス名は、仮想マシンの名前と同じになります。 この場合は、Contoso-DNS-EU となります。 これ以降の仮想マシンについては、同じクラウド サービスを使用することを選択できます。  同じクラウド サービスに属するすべての仮想マシンは、同じ仮想ネットワークとドメインのサフィックスを共有します。
     
     ストレージ アカウントは、仮想マシンのイメージ ファイルを格納するために使用されます。 
   * **エンドポイント**: (下にスクロールして **[DNS]** を選択) 

仮想マシンが作成された後、内部 IP と外部 IP を確認します。

1. 仮想マシン名 **Contoso-DNS-EU**をクリックします。
2. **[ダッシュボード]**をクリックします。
3. 次の内容を記録します。
   * パブリック仮想 IP アドレス
   * 内部 IP アドレス

**Contoso-VNet-US 内に Contoso-DNS-US という名前の仮想マシンを作成するには** 

* 同じ手順を繰り返し、以下の値で仮想マシンを作成します。
  * 仮想マシン名: Contoso-DNS-US
  * リージョン/アフィニティ グループ/仮想ネットワーク: (Contoso-VNET-US を選択)
  * 仮想ネットワーク サブネット: Subnet-1
  * ストレージ アカウント: 自動的に生成されたストレージ アカウントを使用
  * エンドポイント: ([DNS] を選択)

## <a name="set-static-ip-addresses-for-the-two-virtual-machines"></a>2 つの仮想マシンへの静的 IP アドレスの設定
DNS サーバーには静的 IP アドレスが必要です。  この手順は、Azure クラシック ポータルから行うことはできません。 Azure PowerShell を使用します。

**2 つの仮想マシンに静的 IP アドレスを構成するには**

1. Windows PowerShell ISE を開きます。
2. 次のコマンドレットを実行します。  
   
        Add-AzureAccount
        Select-AzureSubscription [YourAzureSubscriptionName]
   
        Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
        Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 
   
    ServiceName はクラウド サービス名です。 DNS サーバーはクラウド サービスの最初の仮想マシンであるため、クラウド サービス名は仮想マシンと同じ名前です。
   
    実際の名前と一致するように ServiceName および Name を更新することが必要になる場合があります。

## <a name="add-the-dns-server-role-the-two-virtual-machines"></a>2 つの仮想マシンの DNS サーバー ロールの追加
**Contoso-DNS-EU の DNS サーバー ロールを追加するには**

1. Azure クラシック ポータルで、左側の **[Virtual Machines]** をクリックします。 
2. **Contoso-DNS-EU**をクリックします。
3. 上部にある **[ダッシュボード]** をクリックします。
4. 下部の **[接続]** をクリックし、指示に従って RDP 経由で仮想マシンに接続します。
5. RDP セッション内で、左下隅の Windows ボタンをクリックしてスタート画面を開きます。
6. **[サーバー マネージャー]** タイルをクリックします。
7. **[役割と機能の追加]**をクリックします。
8.  **[次へ]**
9. **[役割ベースまたは機能ベースのインストール]** を選択し、**[次へ]** をクリックします。
10. DNS 仮想マシン (既に強調表示されているはずです) を選択し、 **[次へ]**をクリックします。
11. **[DNS サーバー]**をオンにします。
12. **[機能の追加]** をクリックし、**[続行]** をクリックします。
13. **[次へ]** を 3 回クリックして、**[インストール]** をクリックします。 

**Contoso-DNS-US の DNS サーバー ロールを追加するには**

* 手順を繰り返して、 **Contoso-DNS-US**に DNS ロールを追加します。

## <a name="assign-dns-servers-to-the-virtual-networks"></a>仮想ネットワークへの DNS サーバーの割り当て
**2 つの DNS サーバーを登録するには**

1. Azure クラシック ポータルで、**[新規]**、**[NETWORK SERVICES]**、**[仮想ネットワーク]**、**[DNS サーバーの登録]** の順にクリックします。
2. 次のように入力します。
   
   * **名前**: Contoso-DNS-EU
   * **DNS サーバーの IP アドレス**: 10.1.0.4 – IP アドレスは DNS サーバー仮想マシンの IP アドレスと一致している必要があります。
3. 手順を繰り返し、次の設定で Contoso-DNS-US を登録します。
   
   * **名前**: Contoso-DNS-US
   * **DNS サーバーの IP アドレス**: 10.2.0.4

**2 つの仮想ネットワークに 2 つの DNS サーバーを割り当てるには**

1. クラシック ポータルの左側のウィンドウで、 **[ネットワーク]** をクリックします。
2. **Contoso-VNet-EU**をクリックします。
3. **[構成]**をクリックします。
4. **[DNS サーバー]** セクションで、**Contoso-DNS-EU** を選択します。
5. ページの下部にある **[保存]** をクリックし、**[はい]** をクリックして確定します。
6. 手順を繰り返し、**Contoso-DNS-US** DNS サーバーを **Contoso-VNet-US** 仮想ネットワークに割り当てます。

仮想ネットワークにデプロイされているすべての仮想マシンを再起動し、DNS サーバーの構成を更新する必要があります。

**仮想マシンを再起動するには**

1. Azure クラシック ポータルで、左側の **[Virtual Machines]** をクリックします。
2. **Contoso-DNS-EU**をクリックします。
3. 上部にある **[ダッシュボード]** をクリックします。
4. 下部の **[再起動]** をクリックします。
5. 同じ手順を繰り返して、 **Contoso-DNS-US**を再起動します。

## <a name="configure-dns-conditional-forwarders"></a>DNS 条件付フォワーダーの構成
各仮想ネットワーク上の DNS サーバーは、その仮想ネットワーク内の DNS 名のみを解決できます。 ピア仮想ネットワークでの名前解決のためにピア DNS サーバーを指し示すには、条件付フォワーダーを構成する必要があります。

条件付フォワーダーを構成するには、2 つの DNS サーバーのドメイン サフィックスを知っている必要があります。 DNS サフィックスは、仮想マシンの作成時に使用した Cloud Services の構成によって異なることがあります。 仮想ネットワークで使用されている DNS サフィックスごとに、条件付フォワーダーを追加する必要があります。 

**2 つの DNS サーバーのドメイン サフィックスを調べるには**

1. RDP で **Contoso-DNS-EU**に接続します。
2. Windows PowerShell コンソールまたはコマンド プロンプトを開きます。
3. **ipconfig** を実行し、**接続固有の DNS サフィックス**の値を記録します。
4. RDP セッションを閉じないでください。引き続き必要になります。 
5. 同じ手順を繰り返し、**Contoso-DNS-US** の**接続固有の DNS サフィックス**を調べます。

**DNS フォワーダーを構成するには**

1. **Contoso-DNS-EU**に対する RDP セッションで、左下の Windows キーをクリックします。
2. **[管理ツール]**をクリックします。
3. **[DNS]**をクリックします。
4. 左側のウィンドウで、**[DSN]**、**[Contoso-DNS-EU]** の順に展開します。
5. 次の情報を入力します。
   * **DNS ドメイン**: Contoso-DNS-US の DNS サフィックスを入力します。 例: Contoso-DNS-US.b5.internal.cloudapp.net
   * **マスター サーバーの IP アドレス**: 「10.2.0.4」と入力します。これは、Contoso-DNS-US の IP アドレスです。
6. **Enter** キーを押し、**[OK]** をクリックします。  以上で、Contoso-DNS-EU から Contoso-DNS-US の IP アドレスを解決できるようになります。
7. 同じ手順を繰り返し、次の値を使用して、Contoso-DNS-US 仮想マシン上の DNS サービスへの DNS フォワーダーを追加します。
   * **DNS ドメイン**: Contoso-DNS-EU の DNS サフィックスを入力します。 
   * **マスター サーバーの IP アドレス**: 「10.2.0.4」と入力します。これは、Contoso-DNS-EU の IP アドレスです。

## <a name="test-the-name-resolution-across-the-virtual-networks"></a>仮想ネットワーク間での名前解決のテスト
これで、仮想ネットワークの間でのホスト名の解決をテストできます。 既定では、Ping はファイアウォールによってブロックされます。  nslookup を使用して、ピア ネットワーク内の DNS サーバー仮想マシン (FQDN を使用する必要があります) を解決できます。  

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、VPN 接続を使用する仮想ネットワーク間での名前解決を構成する方法を説明しました。 このシリーズの他の 2 つの記事は次のような内容です。

* [2 つの Azure 仮想ネットワーク間の VPN 接続の構成][hdinsight-hbase-geo-replication-vnet]
* [HBase geo レプリケーションの構成][hdinsight-hbase-geo-replication]

[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png 



<!--HONumber=Nov16_HO3-->


