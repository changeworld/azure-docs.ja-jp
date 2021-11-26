---
title: RHEL での HSR と Pacemaker を使用した SAP HANA スケールアウト | Microsoft Docs
description: Red Hat Enterprise Linux (RHEL) での HANA システム レプリケーション (HSR) と Pacemaker を使用した SAP HANA スケールアウト
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/12/2021
ms.author: radeltch
ms.openlocfilehash: 74f9fef91149a34c189c696e1791ad6c035e963e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493649"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux での SAP HANA スケールアウト システムの高可用性 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:../../../azure-netapp-files/index.yml
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

この記事では、スケールアウト構成で高可用性 SAP HANA システムをデプロイする方法について説明します。 具体的には、この構成では Azure Red Hat Enterprise Linux 仮想マシン (VM) 上で HANA システム レプリケーション (HSR) と Pacemaker を使用します。 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) によって、提示されているアーキテクチャで共有ファイル システムが提供され、これらのファイル システムはネットワーク ファイル システム (NFS) にマウントされます。  

構成例やインストール コマンドでは、HANA インスタンスは `03`、HANA システム ID は `HN1` です。 例は HANA 2.0 SP4 と Red Hat Enterprise Linux (RHEL) for SAP 7.6 に基づいています。 

## <a name="prerequisites"></a>前提条件

この記事のトピックに進む前に、さまざまな SAP Note やリソースをご覧になると参考になる場合があります。

* SAP Note [1928533] には、次のものが含まれます。  
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧。
  * Azure VM サイズの容量に関する重要な情報。
  * サポートされる SAP ソフトウェア、およびオペレーティング システムとデータベースの組み合わせ。
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン。
* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2002167]: RHEL で推奨されるオペレーティング システム設定が記載されています。
* SAP Note [2009879]: RHEL の SAP HANA ガイドラインが記載されています。
* SAP Note [2178632]: Azure 上の SAP についてレポートされるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP ホスト エージェントのバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* SAP Note [1900823]: SAP HANA のストレージ要件に関する情報が記載されています。
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note が掲載されています。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]。
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ][deployment-guide]。
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]。
* [SAP HANA のネットワーク要件](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)。
* 一般的な RHEL ドキュメント:
  * [高可用性アドオンの概要](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)。
  * [高可用性アドオンの管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)。
  * [高可用性アドオンのリファレンス](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)。
  * [Red Hat Enterprise Linux ネットワーク ガイド](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)。
  * [NFS 共有上の HANA ファイル システムを使用して、Pacemaker クラスターで SAP HANA スケールアウト システム レプリケーションを構成する方法](https://access.redhat.com/solutions/5423971)。
  * [アクティブ/アクティブ (読み取り対応): SAP HANA のスケールアウトとシステム レプリケーションのための RHEL HA ソリューション](https://access.redhat.com/sites/default/files/attachments/v8_ha_solution_for_sap_hana_scale_out_system_replication_1.pdf)。
* Azure 固有の RHEL ドキュメント:
  * [Microsoft Azure で使用するために Red Hat Enterprise Linux に SAP HANA をインストールする](https://access.redhat.com/public-cloud/microsoft-azure)。
  * [SAP HANA のスケールアウトとシステム レプリケーションのための Red Hat Enterprise Linux ソリューション](https://access.redhat.com/solutions/4386601)。
* [Azure NetApp Files を使用した Microsoft Azure 上の NetApp SAP アプリケーション][anf-sap-applications-azure]。
* [Azure NetApp Files のドキュメント][anf-azure-doc]。 
* [SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)。

## <a name="overview"></a>概要

HANA スケールアウトのインストールで HANA の高可用性を実現するには、HANA システム レプリケーションを構成し、Pacemaker クラスターで自動フェールオーバーを許可することによりソリューションを保護できます。 アクティブ ノードで障害が発生すると、クラスターは HANA リソースを他のサイトにフェールオーバーします。  

次の図では、各サイトに 3 つの HANA ノードと、"スプリットブレイン" シナリオを防ぐためのマジョリティ メーカー ノードがあります。 より多くの VM を HANA DB ノードとして含めるように、手順を調整できます。  

[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) により、HANA 共有ファイル システム `/hana/shared` が提供されます。 これは、同じ HANA システム レプリケーション サイト内の各 HANA ノードに NFS v4.1 経由でマウントされます。 ファイル システム `/hana/data` と `/hana/log` はローカル ファイル システムであり、HANA DB ノード間では共有されません。 SAP HANA は非共有モードでインストールされます。 

> [!TIP]
> 推奨される SAP HANA のストレージ構成については、「[SAP HANA Azure VM のストレージ構成](./hana-vm-operations-storage.md)」を参照してください。   

[![HSR と Pacemaker クラスターを使用した SAP HANA スケールアウトの図。](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

上の図は、SAP HANA ネットワークの推奨事項に従って、1 つの Azure 仮想ネットワーク内に 3 つのサブネットがあることを示しています。 

* クライアント通信用: `client` 10.23.0.0/24  
* HANA 内部のノード間通信用: `inter` 10.23.1.128/26  
* HANA システム レプリケーション用: `hsr` 10.23.1.192/26  

`/hana/data` と `/hana/log` はローカル ディスク上にデプロイされるため、ストレージとの通信用に別のサブネットと仮想ネットワーク カードをデプロイする必要はありません。  

Azure NetApp ボリュームは、[Azure NetApp Files に委任された](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)別のサブネットにデプロイされます: `anf` 10.23.1.0/26。   

## <a name="set-up-the-infrastructure"></a>インフラストラクチャをセットアップする

これ以降の手順は、リソース グループおよび 3 つの Azure ネットワーク サブネット (`client`、`inter`、`hsr`) を持つ Azure 仮想ネットワークが既に作成されていることを前提としています。

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Azure portal を使用して Linux 仮想マシンをデプロイする

1. Azure VM を展開します。 この構成では、7 つの仮想マシンをデプロイします。 
   
   - HANA レプリケーション サイト 1 の HANA DB ノードとして機能する 3 台の仮想マシン: **hana-s1-db1**、**hana-s1-db2**、**hana-s1-db3**。  
   - HANA レプリケーション サイト 2 の HANA DB ノードとして機能する 3 台の仮想マシン: **hana-s2-db1**、**hana-s2-db2**、**hana-s2-db3**。  
   - マジョリティ メーカーとして機能する小規模な仮想マシン: **hana-s-mm**。

   SAP DB HANA ノードとしてデプロイされる VM は、[SAP HANA ハードウェア ディレクトリ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)で公開されているように、SAP for HANA によって認定されている必要があります。 HANA DB ノードをデプロイするときは、必ず[高速ネットワーク](../../../virtual-network/create-vm-accelerated-networking-cli.md)を選択します。  
  
   マジョリティ メーカー ノードにデプロイする VM では SAP HANA リソースが実行されないため、小規模の VM をデプロイできます。 マジョリティ メーカー VM は、スプリットブレイン シナリオで奇数個のクラスター ノードを実現するためのクラスター構成として使用されます。 今回の例では、マジョリティ メーカー VM には `client` サブネットに 1 つの仮想ネットワーク インターフェイスが必要となるだけです。        

   `/hana/data` および `/hana/log` 用のローカル マネージド ディスクを展開します。 `/hana/data` と `/hana/log` に推奨される最小ストレージ構成については、「[SAP HANA Azure VM のストレージ構成](./hana-vm-operations-storage.md)」を参照してください。

   各 VM のプライマリ ネットワーク インターフェイスを `client` 仮想ネットワーク サブネット内に展開します。 Azure portal 経由で VM が展開されると、ネットワーク インターフェイス名が自動的に生成されます。 この記事では、自動的に生成されたプライマリ ネットワーク インターフェイスを、**hana-s1-db1-client**、**hana-s1-db2-client**、**hana-s1-db3-client** などのように呼びます。 これらのネットワーク インターフェイスは、`client` Azure 仮想ネットワーク サブネットに接続されます。  

   > [!IMPORTANT]
   > 選択したオペレーティング システムが、使用している特定の VM の種類の SAP HANA に対して SAP 認定されていることを確認してください。 SAP HANA 認定 VM の種類と、その種類に対応するオペレーティング システム リリースの一覧については、[SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)に関するページを参照してください。 一覧表示されている VM の種類の詳細を表示すると、その種類に対して SAP HANA でサポートされているオペレーティング システム リリースの完全な一覧が表示されます。  
  
1. HANA DB 仮想マシンごとに 1 つずつ、6 つのネットワーク インターフェイスを `inter` 仮想ネットワーク サブネットに作成します (この例では、**hana-s1-db1-inter**、**hana-s1-db2-inter**、**hana-s1-db3-inter**、**hana-s2-db1-inter**、**hana-s2-db2-inter**、**hana-s2-db3-inter** とします)。  

1. HANA DB 仮想マシンごとに 1 つずつ、6 つのネットワーク インターフェイスを `hsr` 仮想ネットワーク サブネットに作成します (この例では、**hana-s1-db1-hsr**、**hana-s1-db2-hsr**、**hana-s1-db3-hsr**、**hana-s2-db1-hsr**、**hana-s2-db2-hsr**、**hana-s2-db3-hsr** とします)。  

1. 新しく作成した仮想ネットワーク インターフェイスを、対応する仮想マシンに接続します。  

    1. [Azure portal](https://portal.azure.com/#home) で仮想マシンに移動します。  

    1. 左側のペインで、 **[仮想マシン]** を選択します。 仮想マシン名でフィルター処理し (たとえば **hana-s1-db1**)、その仮想マシンを選択します。  

    1. **[概要]** ペインで、 **[停止]** を選択して仮想マシンの割り当てを解除します。  

    1. **[ネットワーク]** を選択してから、ネットワーク インターフェイスを接続します。 **[ネットワーク インターフェイスの接続]** ドロップダウン リストで、`inter` および `hsr` サブネットに対して既に作成したネットワーク インターフェイスを選択します。  
    
    1. **[保存]** を選択します。 
 
    1. 残りの仮想マシン (この例では **hana-s1-db2**、**hana-s1-db3**、**hana-s2-db1**、**hana-s2-db2**、**hana-s2-db3**) に対して、手順 b から e を繰り返します。
 
    1. 今のところ、仮想マシンは停止状態のままにしておきます。   

1. 以下のようにして、`inter` および `hsr` サブネット用の追加ネットワーク インターフェイスに対して[高速ネットワーク](../../../virtual-network/create-vm-accelerated-networking-cli.md)を有効にします。  

    1. [Azure portal](https://portal.azure.com/#home) で [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) を開きます。  

    1. 次のコマンドを実行して、`inter` および `hsr` サブネットに接続された、追加のネットワーク インターフェイスに対して高速ネットワークを有効にします。  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

1. HANA DB 仮想マシンを起動します。

### <a name="deploy-azure-load-balancer"></a>Azure Load Balancer をデプロイする

Standard Load Balancer を使用するのが最善です。 その方法は次のとおりです。

1. フロントエンド IP プールを作成する:

    1. ロード バランサーを開き、 **[frontend IP pool]\(フロントエンド IP プール\)** を選択して **[Add]\(追加\)** を選択します
    1. 新規のフロントエンド IP プールの名前を入力します (例: *hana-frontend*)。
    1. **[割り当て]** を **[静的]** に設定し、IP アドレスを入力します (例: *10.23.0.18*)。
    1. **[OK]** を選択します。
    1. 新しいフロントエンド IP プールが作成されたら、プールの IP アドレスを書き留めます。

1. バックエンド プールを作成し、すべてのクラスター VM をバックエンド プールに追加します。

    1. ロードバランサーを開き、 **[backend pools]\(バックエンド プール\)** を選択し、 **[Add]\(追加\)** を選択します。
    1. 新しいバックエンド プールの名前を入力します (例: *hana-backend*)。
    1. **[仮想マシンの追加]**  >  **[仮想マシン]** の順に選択します。
    1. SAP HANA クラスターの仮想マシンとその `client` サブネットの IP アドレスを選択します。
    1. **[追加]** を選択します。

1. 正常性プローブを作成する:

    1. ロード バランサーを開き、 **[health probes]\(正常性プローブ\)** を選択して **[Add]\(追加\)** を選択します。
    1. 新しい正常性プローブの名前を入力します (例: *hana-hp*)。
    1. プロトコルとして **[TCP]** を選択し、ポート 625 **03** を選択します。 **[Interval]\(間隔\)** の値を 5 に設定し、 **[Unhealthy threshold]\(異常しきい値\)** の値を 2 に設定します。
    1. **[OK]** を選択します。

1. 負荷分散規則を作成します。
   
    1. ロード バランサーを開き、 **[load balancing rules]\(負荷分散規則\)** を選択して **[Add]\(追加\)** を選択します。
    1. 新しいロード バランサー規則の名前を入力します (例: *hana-lb*)。
    1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **hana-frontend**、**hana-backend**、**hana-hp**)。
    1. **[HA ポート]** を選択します。
    1. **Floating IP を有効にします**。
    1. **[OK]** を選択します。

   > [!IMPORTANT]
   > フローティング IP は、負荷分散シナリオの NIC セカンダリ IP 構成ではサポートされていません。 詳細については、[Azure Load Balancer の制限事項](../../../load-balancer/load-balancer-multivip-overview.md#limitations)に関する記事を参照してください。 VM に追加の IP アドレスが必要な場合は、2 つ目の NIC をデプロイします。    
   
Standard Load Balancer を使用している場合は、次の制限に注意する必要があります。 パブリック IP アドレスのない VM を内部ロード バランサーのバックエンド プールに配置する場合、アウトバウンド インターネット接続はありません。 パブリック エンド ポイントへのルーティングを許可するには、追加の構成を行う必要があります。 詳細は、[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した仮想マシンのパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)を参照してください。  

   > [!IMPORTANT]
   > Azure Load Balancer の背後に配置された Azure VM では TCP タイムスタンプを有効にしないでください。 TCP タイムスタンプを有効にすると正常性プローブが失敗します。 パラメーター `net.ipv4.tcp_timestamps` を `0` に設定します。 詳細については、「[Load Balancer の正常性プローブ](../../../load-balancer/load-balancer-custom-probe-overview.md)」および SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421) を参照してください。  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files インフラストラクチャを展開する 

`/hana/shared` ファイル システムの Azure NetApp Files ボリュームをデプロイします。 HANA システム レプリケーション サイトごとに、個別の `/hana/shared` ボリュームが必要です。 詳細については、「[Azure NetApp Files インフラストラクチャを設定する](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure)」を参照してください。

この例では、次の Azure NetApp Files ボリュームを使用します。 

* ボリューム **HN1**-shared-s1 (nfs://10.23.1.7/**HN1**-shared-s1)
* ボリューム **HN1**-shared-s2 (nfs://10.23.1.7/**HN1**-shared-s2)

## <a name="operating-system-configuration-and-preparation"></a>オペレーティング システムの構成と準備

次のセクションの手順には、次の省略形のいずれかを表すプレフィックスが付いています。

* **[A]** :    すべてのノードに適用できます
* **[AH]** :   すべての HANA DB ノードに適用できます
* **[M]** :    マジョリティ メーカーノードに適用できます
* **[AH1]** :  SITE 1 のすべての HANA DB ノードに適用できます
* **[AH2]** :  SITE 2 のすべての HANA DB ノードに適用できます
* **[1]** :    HANA DB ノード 1、SITE 1 のみに適用できます
* **[2]** :    HANA DB ノード 1、SITE 2 のみに適用できます

次の手順を実行して、オペレーティング システムを構成して準備します。

1. **[A]** 仮想マシン上にホスト ファイルを維持します。 すべてのサブネットのエントリを含めます。 この例では、次のエントリが `/etc/hosts` に追加されます。  

    ```bash
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```

1. **[A]** SAP HANA を実行するためにオペレーティング システムを準備します。 詳細については、「[Azure NetApp Files を使用した Microsoft Azure 上の NetApp SAP アプリケーション][anf-sap-applications-azure]」を参照してください。 Azure NetApp Files 構成設定用の構成ファイル */etc/sysctl.d/netapp-hana.conf* を作成します。  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1    
    net.ipv4.tcp_sack = 1
    </code></pre>

1. **[A]** 追加の最適化設定を使用して、構成ファイル */etc/sysctl.d/ms-az.conf* を作成します。  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

    > [!TIP]
    > SAP ホスト エージェントからポート範囲を管理できるように、`sysctl` 構成ファイルでは明示的に `net.ipv4.ip_local_port_range` と `net.ipv4.ip_local_reserved_ports` を設定しないでください。 詳細については、SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421) を参照してください。  

1. **[A]** 「[Azure NetApp Files を使用した Microsoft Azure 上での NetApp SAP アプリケーション][anf-sap-applications-azure]」で推奨されているように、`sunrpc` 設定を調整します。  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

1. **[A]** NFS クライアント パッケージをインストールします。  

   `yum install nfs-utils`


1. **[AH]** HANA 用の Red Hat の構成。  

   [Red Hat カスタマー ポータル](https://access.redhat.com/solutions/2447641) および次の SAP Note で説明されているように RHEL を構成します。

   - [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7 (SAP HANA DB: RHEL 7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 - SAP HANA DB: RHEL 8 に推奨される OS 設定](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux:GCC 6.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux:GCC 7.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux:GCC 9.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>ファイル システムを準備する

以下のセクションでは、ファイル システムを準備する手順について説明します。

### <a name="mount-the-shared-file-systems"></a>共有ファイル システムをマウントする

この例では、共有 HANA ファイル システムが Azure NetApp Files にデプロイされ、NFS v4 経由でマウントされます。  

1. **[AH]** HANA データベース ボリュームのマウント ポイントを作成します。

    ```bash
    mkdir -p /hana/shared
    ```

1. **[AH]** NFS ドメイン設定を確認します。 ドメインが既定の Azure NetApp Files ドメイン (`defaultv4iddomain.com`) として設定されていることを確認します。 マッピングが `nobody` に設定されていることを確認します。  
   (この手順は、Azure NetAppFiles NFS v4.1 を使用する場合にのみ必要です。)  

    > [!IMPORTANT]
    > Azure NetApp Files の既定のドメイン構成 ( `defaultv4iddomain.com` ) と一致するように、VM 上の `/etc/idmapd.conf` に NFS ドメインを設定していることを確認します。 NFS クライアントと NFS サーバーのドメイン構成が一致しない場合、VM にマウントされている Azure NetApp ボリューム上のファイルのアクセス許可は `nobody` と表示されます。  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

1. **[AH]** `nfs4_disable_idmapping` を確認します。 これは `Y` に設定する必要があります。 `nfs4_disable_idmapping` が配置されるディレクトリ構造を作成するには、mount コマンドを実行します。 アクセスがカーネルまたはドライバー用に予約されるため、 */sys/modules* の下に手動でディレクトリを作成することはできなくなります。  
   この手順は、Azure NetAppFiles NFSv4.1 を使用する場合にのみ必要です。  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   `nfs4_disable_idmapping` パラメーターを変更する方法の詳細については、[Red Hat カスタマー ポータル](https://access.redhat.com/solutions/1749883)を参照してください。

1. **[AH1]** SITE1 HANA DB VM に共有 Azure NetApp Files ボリュームをマウントします。  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

1. **[AH2]** SITE2 HANA DB VM に共有 Azure NetApp Files ボリュームをマウントします。  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


1. **[AH]** 対応する `/hana/shared/` ファイル システムが、NFS プロトコル バージョン **NFSv4** を使用しているすべての HANA DB VM にマウントされていることを確認します。  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>データおよびログのローカル ファイル システムを準備する

提示されている構成では、ファイル システム `/hana/data` と `/hana/log` をマネージド ディスクにデプロイし、これらのファイル システムを各 HANA DB VM にローカルで接続します。 次の手順を実行して、各 HANA DB 仮想マシンで、ローカルのデータとログのボリュームを作成します。 

**論理ボリューム マネージャー (LVM)** を使用してディスク レイアウトを設定します。 次の例は、各 HANA 仮想マシンに 3 つのデータ ディスクが接続されていて、これらのディスクを使用して 2 つのボリュームを作成することを前提としています。

1. **[AH]** すべての使用できるディスクの一覧を出力します。
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   出力例:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

1. **[AH]** 使用するすべてのディスクの物理ボリュームを作成します。
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

1. **[AH]** データ ファイル用のボリューム グループを作成します。 ログ ファイル用に 1 つ、SAP HANA の共有ディレクトリ用に 1 つのボリューム グループを作成します。
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

1. **[AH]** 論理ボリュームを作成します。 `-i` スイッチを指定せずに `lvcreate` を使用すると、"*線形*" のボリュームが作成されます。 I/O パフォーマンスが向上するように、"*ストライプ*" ボリュームを作成することお勧めします。 ストライプ サイズは、[SAP HANA VM のストレージ構成](./hana-vm-operations-storage.md)に関するページに記載されている値に合わせます。 `-i` 引数は、基になる物理ボリュームの数、`-I` 引数はストライプ サイズにする必要があります。 この記事では、2 つの物理ボリュームを使用するので、`-i` スイッチ引数を `2` に設定します。 データ ボリュームのストライプ サイズは `256 KiB` です。 ログ ボリュームには物理ボリュームを 1 つ使用するので、ログ ボリューム コマンドに `-i` および `-I` スイッチを明示的には使用する必要はありません。  

   > [!IMPORTANT]
   > データまたはログ ボリュームごとに複数の物理ボリュームを使用する場合は、`-i` スイッチを使用して基になる物理ボリュームの数を設定します。 ストライプ ボリュームを作成するときにストライプ サイズを指定するには、`-I` スイッチを使用します。 ストライプ サイズやディスク数など、推奨されるストレージ構成については、[SAP HANA VM ストレージ構成](./hana-vm-operations-storage.md)に関する記事を参照してください。  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

1. **[AH]** マウント ディレクトリを作成し、すべての論理ボリュームの UUID をコピーします。
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

1. **[AH]** 論理ボリュームの `fstab` エントリを作成してマウントします。
    ```bash
    sudo vi /etc/fstab
    ```

   `/etc/fstab` ファイルに次の行を挿入します。

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   新しいボリュームをマウントします。

    ```bash
    sudo mount -a
    ```

## <a name="installation"></a>インストール  

この例では、Azure VM で HSR を使用したスケールアウト構成で SAP HANA をデプロイするために、HANA 2.0 SP4 を使用します。  

### <a name="prepare-for-hana-installation"></a>HANA のインストールの準備

1. **[AH]** HANA をインストールする前に、ルート パスワードを設定します。 インストールが完了した後で、ルート パスワードを無効にすることができます。 `root` として `passwd` コマンドを実行してパスワードを設定します。  

1. **[1,2]** `/hana/shared` のアクセス許可を変更します。 
    ```bash
    chmod 775 /hana/shared
    ```

1. **[1]** パスワードの入力を求められることなく、Secure Shell (SSH) 経由で **hana-s1-db2** および **hana-s1-db3** にサインインできることを確認します。 そうでない場合は、[キーベースの認証の使用](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)に関するページに記載されているように、`ssh` キーを交換します。  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

1. **[2]** パスワードの入力を求められることなく、SSH 経由で **hana-s2-db2** および **hana-s2-db3** にサインインできることを確認します。 そうでない場合は、[キーベースの認証の使用](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)に関するページに記載されているように、`ssh` キーを交換します。  
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

1. **[AH]** HANA 2.0 SP4 に必要な追加のパッケージをインストールします。 詳細については、RHEL 7 向けの SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) を参照してください。 

    ```bash
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


1. **[A]** HANA のインストールに干渉しないように、ファイアウォールを一時的に無効にします。 HANA のインストールが完了したら、再度有効にすることができます。 
    ```bash
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>各サイトの最初のノードへの HANA のインストール

1. **[1]** 「[SAP HANA 2.0 のインストールと更新ガイド](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)」の指示に従って、SAP HANA をインストールします。 次の手順では、SITE 1 の最初のノードへの SAP HANA のインストールを示します。   

   1. HANA のインストール ソフトウェア ディレクトリから、`hdblcm` プログラムを `root` として開始します。 `internal_network` パラメーターを使用して、内部 HANA のノード間通信に使用されるサブネットのアドレス空間を渡します。  

      ```bash
      ./hdblcm --internal_network=10.23.1.128/26
      ```
   1. プロンプトで次の値を入力します。

     * **[Choose an action]\(アクションを選択する\)** : 「**1**」と入力します (インストールの場合)。
     * **[Additional components for installation]\(追加でインストールするコンポーネント\)** : 「**2, 3**」と入力します。
     * [installation path]\(インストール パス\): Enter キーを押します (既定値は */hana/shared*)。
     * **[Local Host Name]\(ローカル ホスト名\)** : Enter キーを押して既定値をそのまま使用します。
     * **[Do you want to add hosts to the system?]\(システムにホストを追加しますか?\)** : 「**n**」と入力します。
     * **[SAP HANA System ID]\(SAP HANA システム ID\)** : 「**HN1**」と入力します。
     * **[Instance number]\(インスタンス番号\)** [00]: 「**03**」と入力します。
     * **[Local Host Worker Group]\(ローカル ホスト ワーカー グループ\)** [既定値]: Enter キーを押して既定値をそのまま使用します。
     * **[Select System Usage / Enter index [4]]\(システム用途の選択/インデックスを入力 [4]\)** : 「**4**」と入力します (カスタム)。
     * **[Location of Data Volumes]\(データ ボリュームの場所\)** [/hana/data/HN1]: Enter キーを押して既定値をそのまま使用します。
     * **[Location of Log Volumes]\(ログ ボリュームの場所\)** [/hana/log/HN1]: Enter キーを押して既定値をそのまま使用します。
     * **[Restrict maximum memory allocation?]\(メモリの最大割り当てを制限しますか?\)** [n]: 「**n**」と入力します。
     * **[Certificate Host Name For Host hana-s1-db1]\(ホスト hana-s1-db1 の証明書ホスト名\)** [hana-s1-db1]: Enter キーを押して既定値をそのまま使用します。
     * **[SAP Host Agent User (sapadm) Password]\(SAP ホスト エージェント ユーザー (sapadm) のパスワード\)** : パスワードを入力します。
     * **[Confirm SAP Host Agent User (sapadm) Password]\(SAP ホスト エージェント ユーザー (sapadm) のパスワードの確認\)** : パスワードを入力します。
     * **[System Administrator (hn1adm) Password]\(システム管理者 (hn1adm) のパスワード\)** : パスワードを入力します。
     * **[System Administrator Home Directory]\(システム管理者のホーム ディレクトリ\)** [/usr/sap/HN1/home]: Enter キーを押して既定値をそのまま使用します。
     * **[System Administrator Login Shell]\(システム管理者のログイン シェル\)** [/bin/sh]: Enter キーを押して既定値をそのまま使用します。
     * **[System Administrator User ID]\(システム管理者のユーザー ID\)** [1001]: Enter キーを押して既定値をそのまま使用します。
     * **[Enter ID of User Group (sapsys)]\(ユーザー グループ (sapsys) の ID を入力\)** [79]: Enter キーを押して既定値をそのまま使用します。
     * **[System Database User (system) Password]\(システム データベース ユーザー (system) のパスワード\)** : system のパスワードを入力します。
     * **[Confirm System Database User (system) Password]\(システム データベース ユーザー (system) のパスワードの確認\)** : system のパスワードを入力します。
     * **[Restart system after machine reboot?]\(コンピューターの再起動後にシステムを再起動しますか?\)** [n]: 「**n**」と入力します。 
     * **[Do you want to continue (y/n)]\(続行しますか? (y/n)\)** : 概要を検証し、すべて問題がなさそうな場合は「**y**」と入力します。

1. **[2]** 上記の手順を繰り返して、SITE 2 の最初のノードに SAP HANA をインストールします。   

1. **[1,2]** *global.ini* を確認します。  

   *global.ini* を表示し、内部 SAP HANA のノード間通信が正しく構成されていることを確認します。 `communication` のセクションを確認します。 `inter` サブネットに対するアドレス空間があり、`listeninterface` が `.internal` に設定されている必要があります。 `internal_hostname_resolution` のセクションを確認します。 `inter` サブネットに属する HANA 仮想マシンの IP アドレスが含まれている必要があります。  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

1. **[1,2]** SAP Note [2080991](https://launchpad.support.sap.com/#/notes/0002080991) の説明に従って、共有されていない環境でのインストールのために *global.ini* を準備します。  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

1. **[1,2]** SAP HANA を再起動して、変更をアクティブにします。  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

1. **[1,2]** クライアント インターフェイスが `client` サブネットの IP アドレスを使用して通信するようになっていることを確認します。  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   構成を確認する方法については、SAP Note 「[2183363 - SAP HANA 内部ネットワークの構成](https://launchpad.support.sap.com/#/notes/2183363)」を参照してください。  

1. **[AH]** HANA のインストール エラーを回避するために、データ ディレクトリとログ ディレクトリのアクセス許可を変更します。  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

1. **[1]** セカンダリ HANA ノードをインストールします。 このステップでは、例として SITE 1 での手順を示します。  
   1. 常駐の `hdblcm` プログラムを `root` で開始します。    
      ```bash
       cd /hana/shared/HN1/hdblcm
       ./hdblcm 
      ```

   1. プロンプトで次の値を入力します。

     * **[Choose an action]\(アクションを選択する\)** : 「**2**」と入力します (ホストの追加)。
     * **[Enter comma separated host names to add]\(追加するホストの名前をコンマ区切りで入力\)** : 「hana-s1-db2, hana-s1-db3」と入力します。
     * **[Additional components for installation]\(追加でインストールするコンポーネント\)** : 「**2, 3**」と入力します。
     * **[Enter Root User Name]\(ルート ユーザー名を入力\) [root]** : Enter キーを押して既定値をそのまま使用します。
     * **[Select roles for host 'hana-s1-db2']\(ホスト 'hana-s1-db2' のロールを選択\) [1]** : 1 を選択します (worker の場合)。
     * **[Enter Host Failover Group for host 'hana-s1-db2']\(ホスト 'hana-s1-db2' のホスト フェールオーバー グループを入力\) [既定値]** : Enter キーを押して既定値をそのまま使用します。
     * **[Enter Storage Partition Number for host 'hana-s1-db2']\(ホスト 'hana-s1-db2' のストレージ パーティション番号を入力\) [\<\<assign automatically\>\>]** : Enter キーを押して既定値をそのまま使用します。
     * **[Enter Worker Group for host 'hana-s1-db2']\(ホスト 'hana-s1-db2' の worker グループを入力\) [既定値]** : Enter キーを押して既定値をそのまま使用します。
     * **[Select roles for host 'hana-s1-db3']\(ホスト 'hana-s1-db3' のロールを選択\) [1]** : 1 を選択します (worker の場合)。
     * **[Enter Host Failover Group for host 'hana-s1-db3']\(ホスト 'hana-s1-db3' のホスト フェールオーバー グループを入力\) [既定値]** : Enter キーを押して既定値をそのまま使用します。
     * **[Enter Storage Partition Number for host 'hana-s1-db3']\(ホスト 'hana-s1-db3' のストレージ パーティション番号を入力\) [\<\<assign automatically\>\>]** : Enter キーを押して既定値をそのまま使用します。
     * **[Enter Worker Group for host 'hana-s1-db3']\(ホスト 'hana-s1-db3' の worker グループを入力\) [既定値]** : Enter キーを押して既定値をそのまま使用します。
     * **[System Administrator (hn1adm) Password]\(システム管理者 (hn1adm) のパスワード\)** : パスワードを入力します。
     * **[Enter SAP Host Agent User (sapadm) Password]\(SAP ホスト エージェント ユーザー (sapadm) のパスワードを入力\)** : パスワードを入力します。
     * **[Confirm SAP Host Agent User (sapadm) Password]\(SAP ホスト エージェント ユーザー (sapadm) のパスワードの確認\)** : パスワードを入力します。
     * **[Certificate Host Name For Host hana-s1-db2]\(ホスト hana-s1-db2 の証明書ホスト名\)** [hana-s1-db2]: Enter キーを押して既定値をそのまま使用します。
     * **[Certificate Host Name For Host hana-s1-db3]\(ホスト hana-s1-db3 の証明書ホスト名\)** [hana-s1-db3]: Enter キーを押して既定値をそのまま使用します。
     * **[Do you want to continue (y/n)]\(続行しますか? (y/n)\)** : 概要を検証し、すべて問題がなさそうな場合は「**y**」と入力します。

1. **[2]** 上記の手順を繰り返して、SITE 2 にセカンダリ SAP HANA ノードをインストールします。   

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0 システム レプリケーションの構成

次の手順では、システム レプリケーションを設定します。

1. **[1]** SITE 1 でシステム レプリケーションを構成します。

   **hn1** adm としてデータベースをバックアップします。

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   システム PKI ファイルをセカンダリ サイトにコピーします。

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   プライマリ サイトを作成します。

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

1. **[2]** SITE 2 でシステム レプリケーションを構成します。
    
   2 番目のサイトを登録して、システム レプリケーションを開始します。 <hanasid\>adm として次のコマンドを実行します。

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

1. **[1]** レプリケーションの状態をチェックし、すべてのデータベースが同期されるまで待機します。

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

1. **[1,2]** HANA システム レプリケーション仮想ネットワーク インターフェイスを通じて、HANA システム レプリケーションの通信が行われるように、HANA の構成を変更します。   
   1. 両方のサイトで HANA を停止します。
      ```bash
      sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
      ```

   1. *global.ini* を編集して、HANA システム レプリケーションのホスト マッピングを追加します。 `hsr` サブネットの IP アドレスを使用します。  
      ```bash
      sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
      #Add the section
      [system_replication_hostname_resolution]
      10.23.1.202 = hana-s1-db1
      10.23.1.203 = hana-s1-db2
      10.23.1.204 = hana-s1-db3
      10.23.1.205 = hana-s2-db1
      10.23.1.206 = hana-s2-db2
      10.23.1.207 = hana-s2-db3
      ```

   1. 両方のサイトで HANA を開始します。
     ```bash
      sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
     ```

   詳細については、[システム レプリケーションのホスト名前解決](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html)に関するページを参照してください。  

1. **[AH]** ファイアウォールを再び有効にし、必要なポートを開きます。  
   1. ファイアウォールを再度有効にします。
       ```bash
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   1. 必要なファイアウォール ポートを開きます。 HANA インスタンス番号に合わせてポートを調整する必要があります。  

       > [!IMPORTANT]
       > HANA のノード間通信とクライアント トラフィックを許可するファイアウォール規則を作成します。 必要なポートは、[すべての SAP 製品の TCP/IP ポート](https://help.sap.com/viewer/ports)のページにあります。 次にコマンドは 1 つの例にすぎません。 このシナリオでは、システム番号 03 を使用します。

       ```bash
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Pacemaker クラスターの作成

「[Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする](high-availability-guide-rhel-pacemaker.md)」の手順に従って、この HANA サーバーに対して基本的な Pacemaker クラスターを作成します。 クラスターのマジョリティ メーカーを含む、すべての仮想マシンを含めます。  

> [!IMPORTANT]
> `quorum expected-votes` は 2 に設定しません。 これは 2 ノード クラスターではありません。 ノード フェンシングが逆シリアル化されるように、クラスター プロパティ `concurrent-fencing` が有効になっていることを確認します。   

## <a name="create-file-system-resources"></a>ファイル システム リソースの作成

このプロセスの次の部分では、ファイル システム リソースを作成する必要があります。 その方法は次のとおりです。

1. **[1,2]** 両方のレプリケーション サイトで SAP HANA を停止します。 <sid\>adm として実行します。  

    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

1. **[AH]** インストールのためにすべての HANA DB VM に一時的にマウントされていた、ファイル システム `/hana/shared` をマウント解除します。 マウントを解除する前に、ファイル システムを使用しているプロセスとセッションを停止する必要があります。 
 
    ```bash
    umount /hana/shared 
    ```

1. **[1]** 無効な状態の `/hana/shared` 用のファイル システム クラスター リソースを作成します。 マウントを有効にする前に場所の制約を定義する必要があるため、`--disabled` を使用します。  

    ```bash
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   監視操作に `OCF_CHECK_LEVEL=20` 属性を追加して、監視操作でファイル システムの読み取りおよび書き込みテストを実行できるようにします。 この属性がない場合、監視操作ではファイル システムがマウントされていることのみを確認します。 接続が失われると、アクセス不可能であるにもかかわらずファイル システムがマウントされたままになる場合があるため、これは問題になる可能性があります。  

   `on-fail=fence` 属性も監視操作に追加されます。 このオプションを使用すると、ノードで監視操作が失敗した場合、そのノードはすぐにフェンスされます。 このオプションがない場合、既定の動作では、障害が発生したリソースに依存するすべてのリソースが停止され、障害が発生したリソースが再起動されてから、障害が発生したリソースに依存するすべてのリソースが起動されます。 障害が発生したリソースに SAP HANA リソースが依存している場合、この動作は時間がかかる可能性があるだけでなく、完全に失敗する可能性もあります。 HANA バイナリを保持している NFS 共有にアクセスできない場合、SAP HANA リソースは正常に停止できません。  

1. **[1]** ノード属性を構成して検証します。 レプリケーション サイト 1 のすべての SAP HANA DB ノードには属性 `S1` が割り当てられ、レプリケーション サイト 2 のすべての SAP HANA DB ノードには属性 `S2` が割り当てられます。  

    ```bash
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

1. **[1]** NFS ファイル システムをマウントする場所を決定する制約を構成し、ファイル システム リソースを有効にします。  
    ```bash
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   ファイル システム リソースを有効にすると、クラスターによって `/hana/shared` ファイル システムがマウントされます。
 
1. **[AH]** 両方のサイトのすべての HANA DB VM で、`/hana/shared` に Azure NetApp Files ボリュームがマウントされていることを確認します。

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

1. **[1]** 次のように、属性リソースを構成してクローンし、制約を構成します。  

    ```bash
    # Configure the attribute resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribute resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > 構成に /`hana/shared` 以外のファイル システムが含まれており、これらのファイル システムが NFS でマウントされている場合は、`sequential=false` オプションを指定します。 このオプションを指定すると、ファイル システム間に順序の依存関係がなくなります。 NFS でマウントされたすべてのファイル システムは、対応する属性リソースの前に起動する必要がありますが、互いに決まった順序で起動する必要はありません。 詳細については、[HANA ファイル システムが NFS 共有である場合の、Pacemaker クラスターでの SAP HANA スケールアウト HSR の構成方法](https://access.redhat.com/solutions/5423971)に関するページを参照してください。  

1. **[1]** HANA クラスター リソースの作成準備として、Pacemaker をメンテナンス モードにします。  
    ```bash
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA クラスター リソースの作成

これで、クラスター リソースを作成する準備が整いました。

1. **[A]** マジョリティ メーカーを含むすべてのクラスター ノードに HANA スケールアウト リソース エージェントをインストールします。    

    ```bash
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > お使いのオペレーティング システム リリースでサポートされているパッケージ `resource-agents-sap-hana-scaleout` の最小バージョンのパッケージについては、[RHEL HA クラスターのサポート ポリシー - クラスターでの SAP HANA の管理](https://access.redhat.com/articles/3397471)に関するページを参照してください。  

1. **[1, 2]** HANA システム レプリケーション フックを、各システム レプリケーション サイトの 1 つの HANA DB ノードにインストールします。 SAP HANA は引き続き停止している必要があります。        

   1. フックを `root` として準備します。 
      ```bash
       mkdir -p /hana/shared/myHooks
       cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
       chown -R hn1adm:sapsys /hana/shared/myHooks
      ```

   1. `global.ini` を調整します。
      ```bash
      # add to global.ini
      [ha_dr_provider_SAPHanaSR]
      provider = SAPHanaSR
      path = /hana/shared/myHooks
      execution_order = 1
    
      [trace]
      ha_dr_saphanasr = info
      ```

1. **[AH]** クラスターでは、<sid\>adm のクラスター ノードで sudoers 構成が必要です。 この例では、新しいファイルを作成することによって、これを実行します。 `root` としてコマンドを実行します。    
    ```bash
    sudo visudo -f /etc/sudoers.d/20-saphana
    # Insert the following lines and then save
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    Defaults!SOK, SFAIL !requiretty
    ```

1. **[1,2]** 両方のレプリケーション サイトで SAP HANA を開始します。 <sid\>adm として実行します。  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

1. **[1]** フックのインストールを確認します。 アクティブな HANA システム レプリケーション サイトで、<sid\>adm として実行します。   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

1. **[1]** HANA クラスター リソースを作成します。 `root` として次のコマンドを実行します。    
   1. クラスターが既にメンテナンス モードになっていることを確認します。  
    
   1. 次に、HANA トポロジ リソースを作成します。  
      RHEL **7.x** クラスターを構築している場合は、次のコマンドを使用します。  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      RHEL **8.x** クラスターを構築している場合は、次のコマンドを使用します。  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   1. HANA インスタンス リソースを作成します。  
      > [!NOTE]
      > この記事には、Microsoft が使用しなくなった "*スレーブ*" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。  
 
      RHEL **7.x** クラスターを構築している場合は、次のコマンドを使用します。    
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      RHEL **8.x** クラスターを構築している場合は、次のコマンドを使用します。  
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > フェールオーバー テストの実行中に `AUTOMATED_REGISTER` を `false` に設定して、失敗したプライマリ インスタンスが自動的にセカンダリとして登録されないようにすることをお勧めします。 ベスト プラクティスとして、テストが終わったら `AUTOMATED_REGISTER` を `true` に設定し、引き継ぎ後にシステム レプリケーションが自動的に再開できるようにします。 

   1. 仮想 IP と関連するリソースを作成します。  
      ```bash
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   1. クラスターの制約を作成します。  
      RHEL **7.x** クラスターを構築している場合は、次のコマンドを使用します。  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      RHEL **8.x** クラスターを構築している場合は、次のコマンドを使用します。  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

1. **[1]** クラスターのメンテナンス モードを解除します。 クラスターの状態が `ok` であることと、すべてのリソースが起動されていることを確認します。  
    ```bash
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > 前の構成のタイムアウトは例にすぎないため、特定の HANA セットアップに適合させる必要がある場合があります。 たとえば、SAP HANA データベースの起動に時間がかかる場合は、開始タイムアウトを長くする必要がある可能性があります。
  
## <a name="configure-hana-activeread-enabled-system-replication"></a>HANA のアクティブかつ読み取り可能のシステム レプリケーションの構成

SAP HANA 2.0 SPS 01 以降、SAP では SAP HANA システム レプリケーションに対してアクティブかつ読み取り可能なセットアップが可能になりました。 この機能を使用すると、読み取りを集中的に行うワークロードに対して、SAP HANA システム レプリケーションのセカンダリ システムをアクティブに使用できます。 クラスターでこのようなセットアップをサポートするには、2 番目の仮想 IP アドレスが必要です。これにより、読み取りが有効なセカンダリ SAP HANA データベースにクライアントからアクセスできます。 引き継ぎの実行後もセカンダリ レプリケーション サイトにアクセスできるようにするには、SAP HANA リソースのセカンダリを使用して、クラスターで仮想 IP アドレスを移動する必要があります。

このセクションでは、Red Hat 高可用性クラスターで、2 番目の仮想 IP アドレスを使用して、この種のシステム レプリケーションを管理するために必要な追加の手順について説明します。  

先に進む前に、この記事で前述したように、SAP HANA データベースを管理する Red Hat 高可用性クラスターの構成が完了していることを確認してください。  

![読み取り可能なセカンダリを使用した高可用性のスケールアウトSAP HANA](./media/sap-hana-high-availability-rhel/sap-hana-high-avalability-scale-out-hsr-rhel-read-enabled.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>アクティブかつ読み取り可能なセットアップ用の Azure Load Balancer の追加設定

2 番目の仮想 IP のプロビジョニングに進むには、「[Azure Load Balancer をデプロイする](#deploy-azure-load-balancer)」の説明に従って Azure Load Balancer の構成が完了していることを確認してください。

*Standard* Load Balancer の場合は、前のセクションで作成したのと同じロード バランサーで、下の追加手順に従います。

1. 2 番目のフロントエンド IP プールを作成する: 

   1. ロード バランサーを開き、 **[frontend IP pool]\(フロントエンド IP プール\)** を選択して **[Add]\(追加\)** を選択します
   1. この 2 番目のフロントエンド IP プールの名前を入力します (例: *hana-secondaryIP*)。
   1. **[割り当て]** を **[静的]** に設定し、IP アドレスを入力します (例: **10.23.0.19**)。
   1. **[OK]** を選択します。
   1. 新しいフロントエンド IP プールが作成されたら、プールの IP アドレスを書き留めます。

1. 次に、正常性プローブを作成します。

   1. ロード バランサーを開き、 **[health probes]\(正常性プローブ\)** を選択して **[Add]\(追加\)** を選択します。
   1. 新しい正常性プローブの名前を入力します (例: *hana-secondaryhp*)。
   1. プロトコルとして **[TCP]** を、ポートは **62603** を選択します。 **[Interval]\(間隔\)** の値を 5 に設定し、 **[Unhealthy threshold]\(異常しきい値\)** の値を 2 に設定します。
   1. **[OK]** を選択します。

1. 次に、負荷分散規則を作成します。

   1. ロード バランサーを開き、 **[load balancing rules]\(負荷分散規則\)** を選択して **[Add]\(追加\)** を選択します。
   1. 新しいロード バランサー規則の名前を入力します (例: *hana-secondarylb*)。
   1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **hana-secondaryIP**、**hana-backend**、**hana-secondaryhp**)。
   1. **[HA ポート]** を選択します。
   1. **Floating IP を有効にします**。
   1. **[OK]** を選択します。

### <a name="configure-hana-activeread-enabled-system-replication"></a>HANA のアクティブかつ読み取り可能のシステム レプリケーションの構成

HANA システム レプリケーションを構成する手順については、「[SAP HANA 2.0 システム レプリケーションの構成](#configure-sap-hana-20-system-replication)」セクションを参照してください。 読み取り可能なセカンダリ シナリオをデプロイする場合、2 番目のノードでシステム レプリケーションを構成するときに、次のコマンドを **hanasid** adm として実行します。

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2 --operationMode=logreplay_readaccess 
```

### <a name="add-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>アクティブかつ読み取り可能のセットアップ用のセカンダリ仮想 IP アドレス リソースを追加する

2 番目の仮想 IP と追加の制約は、次のコマンドを使用して構成できます。 セカンダリインスタンスがダウンしている場合は、セカンダリ仮想 IP がプライマリに切り替わります。   

```
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.23.0.19"
pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603
pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

# RHEL 8.x: 
pcs constraint location g_ip_HN1_03 rule score=500 role=master hana_hn1_roles eq "master1:master:worker:master" and hana_hn1_clone_state eq PROMOTED
pcs constraint location g_secip_HN1_03 rule score=50  hana_hn1_roles eq 'master1:master:worker:master'
pcs constraint order promote  SAPHana_HN1_HDB03-clone then start g_ip_HN1_03
pcs constraint order start g_ip_HN1_03 then start g_secip_HN1_03
pcs constraint colocation add g_secip_HN1_03 with Slave SAPHana_HN1_HDB03-clone 5

# RHEL 7.x:
pcs constraint location g_ip_HN1_03 rule score=500 role=master hana_hn1_roles eq "master1:master:worker:master" and hana_hn1_clone_state eq PROMOTED
pcs constraint location g_secip_HN1_03 rule score=50  hana_hn1_roles eq 'master1:master:worker:master'
pcs constraint order promote  msl_SAPHana_HN1_HDB03 then start g_ip_HN1_03
pcs constraint order start g_ip_HN1_03 then start g_secip_HN1_03
pcs constraint colocation add g_secip_HN1_03 with Slave msl_SAPHana_HN1_HDB03 5

pcs property set maintenance-mode=false
```
クラスターの状態が `ok` であることと、すべてのリソースが起動されていることを確認します。 2 番目の仮想 IP は、セカンダリ サイトで SAP HANA セカンダリ リソースと共に実行されます。

```
# Example output from crm_mon
#Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#
#Active resources:
#
#rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
#Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
#    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
#Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
#    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
#    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
#Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
#    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
#    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
#    Masters: [ hana-s1-db1 ]
#    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Resource Group: g_ip_HN1_03
#    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
#    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
#Resource Group: g_secip_HN1_03
#    secnc_HN1_03       (ocf::heartbeat:azure-lb):      Started hana-s2-db1
#    secvip_HN1_03      (ocf::heartbeat:IPaddr2):       Started hana-s2-db1

```

次のセクションでは、実行する典型的なフェールオーバー テストのセットを示します。

読み取りが有効なセカンダリで構成された HANA クラスターをテストする場合は、2 番目の仮想 IP の次の動作に注意します。

- クラスター リソース **SAPHana_HN1_HDB03** がセカンダリ サイト (**S2**) に移動すると、2 番目の仮想 IP がもう一方のサイト (**hana-s1-db1**) に移動します。 `AUTOMATED_REGISTER="false"` が構成済みで、HANA システム レプリケーションが自動的に登録されていない場合は、2 番目の仮想 **IP が hana-s2-db1** で実行されます。  

- サーバーのクラッシュをテストする場合、2 番目の仮想 IP リソース (**rsc_secvip_HN1_03**) と Azure Load Balancer のポート リソース (**secnc_HN1_03**) は、プライマリ仮想 IP リソースと共にプライマリ サーバー上で実行されます。 セカンダリ サーバーが停止している間、読み取り可能な HANA データベースに接続されているアプリケーションは、プライマリ HANA データベースに接続します。 これは正しい動作です。 セカンダリ サーバーが使用できなくても、読み取りが有効な HANA データベースに接続されているアプリケーションは動作することができます。   
  
- フェールオーバーとフォールバックの間は、2 番目の仮想 IP を使用して HANA データベースに接続するアプリケーションの既存の接続が中断される場合があります。  

## <a name="test-sap-hana-failover"></a>SAP HANA フェールオーバーのテスト 

1. テストを開始する前に、クラスターと SAP HANA システムのレプリケーション状態を確認します。  

   1. 失敗したクラスター アクションがないことを確認します。  
       ```bash
       #Verify that there are no failed cluster actions
       pcs status
       # Example
       #Stack: corosync
       #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
       #Last updated: Thu Sep 24 06:00:20 2020
       #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
       #
       #7 nodes configured
       #45 resources configured
       #
       #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #
       #Active resources:
       #
       #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
       #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
       #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
       #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
       #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
       #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
       #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
       #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
       #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
       #    Masters: [ hana-s1-db1 ]
       #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Resource Group: g_ip_HN1_03
       #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
       #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
       ```

   1. SAP HANA システム レプリケーションが同期されていることを確認します。

      ```bash
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

1. ノードが NFS 共有 (`/hana/shared`) へのアクセスを失ったときの障害シナリオに備えてクラスター構成を検証します。  

   SAP HANA リソース エージェントでは、フェールオーバー中の操作の実行は `/hana/shared` に保存されるバイナリに依存しています。 提示されている構成では、ファイル システム `/hana/shared` は NFS 経由でマウントされています。 実行できるテストは、`/hana/shared` ファイル システムを "*読み取り専用*" として再マウントすることです。 この方法では、アクティブなシステム レプリケーション サイトで `/hana/shared` へのアクセスが失われた場合に、クラスターがフェールオーバーされることを検証します。  

   **予想される結果**: `/hana/shared` を "*読み取り専用*" として再マウントすると、ファイル システムに対して読み取りおよび書き込み操作を実行する監視操作は失敗します。 これは、ファイル システムへの書き込みができないので、HANA リソースのフェールオーバーがトリガーされるためです。 HANA ノードが NFS 共有へのアクセスを失った場合も、同じ結果が予想されます。  
     
   クラスター リソースの状態を確認するには、`crm_mon` または `pcs status` を実行します。 テスト開始前のリソースの状態:
      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   プライマリ レプリケーション サイトの VM のいずれかで `/hana/shared` の失敗をシミュレートするには、次のコマンドを実行します。
      ```bash
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the preceding command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   `/hana/shared` へのアクセスが失われた HANA VM は、クラスター構成に応じて再起動または停止する必要があります。 クラスター リソースは、他の HANA システム レプリケーション サイトに移行されます。  
         
   再起動された VM でクラスターが開始されていない場合は、次を実行してクラスターを開始します。 

      ```bash
      # Start the cluster 
      pcs cluster start
      ```
   
   クラスターが開始されると、ファイル システム `/hana/shared` が自動的にマウントされます。 `AUTOMATED_REGISTER="false"` を設定している場合は、セカンダリ サイトで SAP HANA システム レプリケーションを構成する必要があります。 この場合、次のコマンドを実行して、SAP HANA をセカンダリとして再構成することができます。   

      ```bash
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and clean up failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   テスト後のリソースの状態は次のようになります。 

      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


[RHEL 上の Azure VM における SAP HANA の高可用性](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)に関するページに記載されているテストも行い、SAP HANA クラスター構成を十分にテストすることをお勧めします。

## <a name="next-steps"></a>次のステップ

* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法については、[Azure VM 上の SAP HANA の高可用性][sap-hana-ha]に関するページを参照してください。
