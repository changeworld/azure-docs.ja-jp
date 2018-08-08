---
title: SUSE Linux Enterprise Server for SAP Applications 上の SAP NetWeaver の Azure Virtual Machines 高可用性 | Microsoft Docs
description: SUSE Linux Enterprise Server for SAP Applications 上の SAP NetWeaver の高可用性ガイド
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: 935b501964435e80172ef3e147f777bf47119b48
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285124"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

この記事では、仮想マシンのデプロイと構成、クラスター フレームワークのインストール、高可用性 SAP NetWeaver 7.50 システムのインストールの方法について説明します。
この例の構成やインストール コマンドなどでは、ASCS インスタンス番号として 00、ERS インスタンス番号として 02、SAP システム ID として NW1 が使用されています。 この例のリソース (仮想マシン、仮想ネットワークなど) の名前は、SAP システム ID NW1 で[集約型テンプレート][template-converged]を使用してリソースを作成していることを想定しています。

はじめに、次の SAP Note およびガイドを確認してください

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン

* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2205917]: SUSE Linux Enterprise Server for SAP Applications 向けの推奨の OS 設定が記載されています。
* SAP Note [1944799]: SUSE Linux Enterprise Server for SAP Applications の SAP HANA ガイドラインが記載されています。
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1984787]: SUSE Linux Enterprise Server 12 に関する一般情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ (この記事)][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関する記事
* [SUSE SAP HA Best Practice Guides (SUSE SAP HA ベスト プラクティス ガイド)][suse-ha-guide] このガイドには、オンプレミスで Netweaver HA と SAP HANA System Replication を設定するために必要なすべての情報が記載されています。 一般的なベースラインとしてこのガイドを使用してください。 このガイドには詳細な情報が提供されています。
* [Highly Available NFS Storage with DRBD and Pacemaker (DRBD と Pacemaker による高可用性 NFS ストレージ)][suse-drbd-guide]: このガイドには、高可用性 NFS サーバーのセットアップに必要なすべての情報が記載されています。 このガイドをベースラインとして使用します。


## <a name="overview"></a>概要

高可用性を実現するため、SAP NetWeaver には NFS サーバーが必要です。 NFS サーバーは別のクラスターで構成されており、複数の SAP システムが使用できます。

![SAP NetWeaver の高可用性の概要](./media/high-availability-guide-suse/img_001.png)

NFS サーバー、SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS、SAP HANA データベースでは、仮想ホスト名と仮想 IP アドレスが使用されます。 Azure では、仮想 IP アドレスを使用するためにロード バランサーが必要になります。 (A)SCS および ERS ロード バランサーの構成を次に示します。

### <a name="ascs"></a>(A)SCS

* フロントエンドの構成
  * IP アドレス 10.0.0.7
* バックエンドの構成
  * (A)SCS/ERS クラスターに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み
* プローブ ポート
  * ポート 620**&lt;nr&gt;**
* 負荷分散規則
  * 32**&lt;nr&gt;** TCP
  * 36**&lt;nr&gt;** TCP
  * 39**&lt;nr&gt;** TCP
  * 81**&lt;nr&gt;** TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

### <a name="ers"></a>ERS

* フロントエンドの構成
  * IP アドレス 10.0.0.8
* バックエンドの構成
  * (A)SCS/ERS クラスターに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み
* プローブ ポート
  * ポート 621**&lt;nr&gt;**
* 負荷分散規則
  * 33**&lt;nr&gt;** TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>高可用性 NFS サーバーのセットアップ

SAP NetWeaver では、転送とプロファイル ディレクトリ用の共有ストレージが必要です。 SAP NetWeaver 用の NFS サーバーの設定方法については、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性][nfs-ha]」をご覧ください。

## <a name="setting-up-ascs"></a>(A)SCS のセットアップ

GitHub にある Azure テンプレートを使用して、仮想マシン、可用性セット、ロード バランサーなどの必要なすべての Azure リソースをデプロイできます。また、各リソースを手動でデプロイすることもできます。

### <a name="deploy-linux-via-azure-template"></a>Azure テンプレートを使用した Linux のデプロイ

Azure Marketplace には、SUSE Linux Enterprise Server for SAP Applications 12 のイメージが含まれており、新しい仮想マシンのデプロイに使用できます。 Marketplace のイメージには、SAP NetWeaver のリソース エージェントが含まれています。

GitHub にあるいずれかのクイック スタート テンプレートを使用して、必要なすべてのリソースをデプロイできます。 テンプレートでは、仮想マシン、ロード バランサー、可用性セットなどをデプロイできます。テンプレートをデプロイするには、次の手順に従います。

1. Azure Portal で、[ASCS/SCS マルチ SID テンプレート][template-multisid-xscs]または[集約型テンプレート][template-converged]を開きます。ASCS/SCS テンプレートで作成されるのは、SAP NetWeaver ASCS/SCS および ERS (Linux のみ) インスタンスの負荷分散規則のみであるのに対し、集約型テンプレートではデータベース (Microsoft SQL Server や SAP HANA など) の負荷分散規則も作成されます。 SAP NetWeaver ベースのシステムをインストールする予定があり、同じマシンにデータベースもインストールしたい場合は、[集約型テンプレート][template-converged]を使用してください。
1. 次のパラメーターを入力します
   1. リソース プレフィックス (ASCS/SCS マルチ SID テンプレートのみ)  
      使用するプレフィックスを入力します。 この値は、デプロイされるリソースのプレフィックスとして使用されます。
   3. SAP システム ID (集約型テンプレートのみ)  
      インストールする SAP システムの SAP システム ID を入力します。 この ID は、デプロイされるリソースのプレフィックスとして使われます。
   4. スタックの種類  
      SAP NetWeaver のスタックの種類を選択します
   5. OS の種類  
      いずれかの Linux ディストリビューションを選択します。 この例では、SLES 12 BYOS を選択します
   6. データベースの種類  
      HANA を選択します
   7. Sap System Size (SAP システムのサイズ)  
      新しいシステムで提供する SAPS の量。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
   8. システムの可用性  
      [HA] を選択します
   9. [管理ユーザー名] と[管理パスワード]  
      コンピューターへのログオンで使用できる新しいユーザーが作成されます。
   10. サブネット ID  
   仮想マシンを接続するサブネットの ID。  新しい仮想ネットワークを作成する場合は空のままにしておきます。または、NFS サーバー デプロイの一部として使用または作成したのと同じサブネットを選択します。 通常、この ID は、/subscriptions/**&lt;サブスクリプション ID&gt;**/resourceGroups/**&lt;リソース グループ名&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;仮想ネットワーク名&gt;**/subnets/**&lt;サブネット名&gt;** のようになります。

### <a name="deploy-linux-manually-via-azure-portal"></a>Azure Portal を使用した手動による Linux のデプロイ

まず、この NFS クラスターの仮想マシンを作成する必要があります。 その後、ロード バランサーを作成し、バックエンド プール内の仮想マシンを使用します。

1. リソース グループを作成します
1. 仮想ネットワークを作成します
1. 可用性セットを作成します  
   更新ドメインの最大数を設定します
1. 仮想マシン 1 を作成します   
   SLES4SAP 12 SP1 以上を使用します。この例では、SLES4SAP 12 SP1 イメージ (https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM) を使用します  
   SLES For SAP Applications 12 SP1 が使用されます  
   前に作成された可用性セットを選択します  
1. 仮想マシン 2 を作成します   
   SLES4SAP 12 SP1 以上を使用します。この例では、SLES4SAP 12 SP1 イメージ (https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM) を使用します  
   SLES For SAP Applications 12 SP1 が使用されます  
   前に作成された可用性セットを選択します  
1. 両方の仮想マシンに、少なくとも 1 つのデータ ディスクを追加します  
   データ ディスクは /usr/sap/`<SAPSID`> ディレクトリに使用されます
1. ロード バランサー (内部) を作成します  
   1. フロントエンド IP アドレスを作成します
      1. ASCS の IP アドレス 10.0.0.7
         1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
         1. 新規のフロントエンド IP プールの名前を入力します (例: **nw1-ascs-frontend**)
         1. 割り当てを "静的" に設定し、IP アドレスを入力します (例: **10.0.0.7**)
         1. [OK] をクリックします
      1. ASCS ERS の IP アドレス 10.0.0.8
         * 上記の手順を繰り返して、ERS の IP アドレスを作成します (例: **10.0.0.8** と **nw1-aers-backend**)
   1. バックエンド プールを作成します
      1. ASCS のバックエンド プールの作成
         1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
         1. 新規のバックエンド プールの名前を入力します (例: **nw1-ascs-backend**)
         1. [仮想マシンの追加] をクリックします。
         1. 前の手順で作成した可用性セットを選択します
         1. (A)SCS クラスターの仮想マシンを選択します
         1. [OK] をクリックします
      1. ASCS ERS のバックエンド プールを作成します
         * 上記の手順を繰り返して、ERS のバックエンド プールを作成します (例: **nw1-aers-backend**)
   1. 正常性プローブを作成します
      1. ASCS のポート 620**00**
         1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
         1. 新しい正常性プローブの名前を入力します (例: **nw1-ascs-hp**)
         1. プロトコルに TCP、ポートに 620**00** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
         1. [OK] をクリックします
      1. ASCS ERS のポート 621**02**
         * 上記の手順を繰り返して、ERS の正常性プローブを作成します (例: 621**02** と **nw1-aers-hp**)
   1. 負荷分散規則
      1. ASCS の 32**00** TCP
         1. ロード バランサーを開き、[負荷分散規則] を選択して [追加] をクリックします
         1. 新しいロード バランサー規則の名前を入力します (例: **nw1-lb-3200**)
         1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **nw1-ascs-frontend**)
         1. プロトコルは **TCP** のままにし、ポートに「**3200**」を入力します
         1. アイドル タイムアウトを 30 分に増やします
         1. **Floating IP を有効にします**
         1. [OK] をクリックします    
      1. ASCS の追加のポート
         * ASCS のポート 36**00**、39**00**、81**00**、5**00**13、5**00**14、5**00**16 と TCP に対して上記の手順を繰り返します
      1. ASCS ERS の追加のポート
         * ASCS ERS のポート 33**02**、5**02**13、5**02**14、5**02**16 と TCP に対して上記の手順を繰り返します

### <a name="create-pacemaker-cluster"></a>Pacemaker クラスターの作成

「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](high-availability-guide-suse-pacemaker.md)」の手順に従って、この (A)SCS サーバーに対して基本的な Pacemaker クラスターを作成します。

### <a name="installation"></a>インストール

次の各手順の先頭には、**[A]** - 全ノードが該当、**[1]** - ノード 1 のみ該当、**[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** SUSE コネクタをインストールします
   
   <pre><code>
   sudo zypper install sap-suse-cluster-connector
   </code></pre>

   SAP SUSE クラスター コネクタの新しいバージョンをインストールしたことを確認します。 古いものの名前は sap_suse_cluster_connector であり、新しいものの名前は **sap-suse-cluster-connector**です。

   <pre><code>
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

1. **[A]** SAP リソース エージェントを更新します  
   
   この記事で説明されている新しい構成を使用するには、resource-agents パッケージ用の修正プログラムが必要です。 この修正プログラムが既にインストールされているかどうかは、次のコマンドで確認できます。

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   出力は次のようになります。

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   grep コマンドで IS_ERS パラメーターが見つからない場合は、[SUSE ダウンロード ページ](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)に記載されている修正プログラムをインストールする必要があります。

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** ホスト名解決を設定します   

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver のインストールの準備

1. **[A]** 共有ディレクトリを作成します

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** autofs を構成します
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   次を含むファイルを作成します

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   /usr/sap/<b>NW1</b>/ASCS<b>00</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCS
   /usr/sap/<b>NW1</b>/ERS<b>02</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS
   </code></pre>

   autofs を再起動して新しい共有をマウントします

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** スワップ ファイルを構成します

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   エージェントを再起動して変更をアクティブにします

   <pre><code>
   sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS のインストール

1. **[1]** ASCS インスタンス用の仮想 IP リソースと正常性プローブを作成します

   <pre><code>
   sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

   <pre><code>
   sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** SAP NetWeaver ASCS をインストールします  

   root として SAP NetWeaver ASCS を最初のノードにインストールします。その際、ASCS のロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名 (たとえば、<b>nw1-ascs</b>、<b>10.0.0.7</b>) と、ロード バランサーのプローブに使用したインスタンス番号 (たとえば、<b>00</b>) を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   インストールで /usr/sap/**NW1**/ASCS**00** へのサブフォルダーの作成に失敗する場合は、ASCS **00** フォルダーの所有者とグループを設定し、もう一度試してください。

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** ERS インスタンス用の仮想 IP リソースと正常性プローブを作成します

   <pre><code>
   sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_NW1_ASCS,nc_NW1_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>
 
   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

   <pre><code>
   sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** SAP NetWeaver ERS をインストールします  

   root として SAP NetWeaver ERS を 2 番目のノードにインストールします。その際、ERS のロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名 (たとえば、<b>nw1-aers</b>、<b>10.0.0.8</b>) と、ロード バランサーのプローブに使用したインスタンス番号 (たとえば、<b>02</b>) を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > SWPM SP 20 PL 05 以降を使用します。 これより下位のバージョンではアクセス許可が正しく設定されないため、インストールが失敗します。

   インストールで /usr/sap/**NW1**/ERS**02** へのサブフォルダーの作成に失敗する場合は、ERS **02** フォルダーの所有者とグループを設定し、もう一度試してください。

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>
   > 

1. **[1]** ASCS/SCS および ERS インスタンス プロファイルを適用します
 
   * ASCS/SCS プロファイル

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS プロファイル

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   </code></pre>


1. **[A]** キープ アライブを構成します

   SAP NetWeaver アプリケーション サーバーと ASCS/SCS の間の通信は、ソフトウェア ロード バランサーを介してルーティングされます。 ロード バランサーは、構成可能なタイムアウト後に非アクティブな接続を切断します。 これを防ぐには、SAP NetWeaver ASCS/SCS プロファイルでパラメーターを設定し、Linux システム設定を変更する必要があります。 詳細については、[SAP Note 1410736][1410736] を参照してください。
   
   ASCS/SCS プロファイル パラメーター enque/encni/set_so_keepalive は、前の手順で既に追加されています。

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** インストール後に SAP ユーザーを構成します
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm   
   </code></pre>

1. **[1]** ASCS および ERS SAP サービスを sapservice ファイルに追加します

   ASCS サービス エントリを 2 番目のノードに追加し、ERS サービス エントリを最初のノードにコピーします。

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** SAP クラスター リソースを作成します

   <pre><code>
   sudo crm configure property maintenance-mode="true"   
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

   <pre><code>
   sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver アプリケーション サーバーの準備

一部のデータベースでは、データベース インスタンスのインストールがアプリケーション サーバーで実行される必要があります。 このような場合に使用できるようにアプリケーション サーバー仮想マシンを準備します。

次の手順では、ASCS/SCS および HANA サーバーとは別のサーバーにアプリケーション サーバーをインストールすることを前提としています。 それ以外の場合、以下の手順の一部 (ホスト名解決の構成など) は必要ありません。

1. ホスト名解決を設定します

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます
   ```bash
   sudo vi /etc/hosts
   ```
   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. sapmnt ディレクトリを作成します

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. autofs を構成します
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   次を含む新しいファイルを作成します

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   autofs を再起動して新しい共有をマウントします

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. スワップ ファイルを構成します
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   エージェントを再起動して変更をアクティブにします

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>データベースのインストール

この例では、SAP HANA に SAP NetWeaver がインストールされます。 このインストールではサポートされているすべてのデータベースを使用できます。 Azure への SAP HANA のインストール方法について詳しくは、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」をご覧ください。 サポートされているデータベースの一覧については、[SAP Note 1928533][1928533] をご覧ください。

1. SAP データベース インスタンスのインストールを実行します

   root として SAP NetWeaver データベース インスタンスをインストールします。その際、データベースのロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名 (たとえば、<b>nw1-db</b> と <b>10.0.0.13</b>) を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver アプリケーション サーバーのインストール

次の手順に従って、SAP アプリケーション サーバーをインストールします。 

1. アプリケーション サーバーを準備します

前述の「[SAP NetWeaver アプリケーション サーバーの準備](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)」の章の手順に従って、アプリケーション サーバーを準備します。

1. SAP NetWeaver アプリケーション サーバーをインストールします

   プライマリまたは追加の SAP NetWeaver アプリケーション サーバーをインストールします。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA Secure Store を更新します

   SAP HANA システム レプリケーション セットアップの仮想名を指すように SAP HANA Secure Store を更新します。

   次のコマンドを実行して、エントリを一覧表示します
   <pre><code>
   hdbuserstore List
   </code></pre>

   これにより、次のようにすべてのエントリが一覧表示されます
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: HN1
   </code></pre>

   出力には、既定のエントリの IP アドレスがロード バランサーの IP アドレスではなく仮想マシンを指していることが示されます。 このエントリは、ロード バランサーの仮想ホスト名を指すように変更する必要があります。 必ず同じポートを使用してください (上記の出力では **30313** です)。

   <pre><code>
   su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>次の手順
* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。
