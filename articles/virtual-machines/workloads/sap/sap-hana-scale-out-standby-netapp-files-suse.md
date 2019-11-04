---
title: SUSE Linux Enterprise Server 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする | Microsoft Docs
description: SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の SAP NetWeaver の高可用性ガイド
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 11b0746c3e9e137775b2466af776b4cd9ba1e5df
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791674"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


この記事では、共有ストレージ ボリューム用の [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) を使用して、Azure 仮想マシン上に高可用性 HANA システムをスタンバイを備えたスケールアウト構成でデプロイする方法について説明します。  
構成例、インストール コマンドなどでは、HANA インスタンスは **03**、HANA システム ID は **HN1** です。 例は HANA 2.0 SP4 と SUSE Linux Enterprise Server for SAP 12 SP4 に基づいています。 

はじめに、次の SAP Note およびガイドを確認してください

* [Azure NetApp Files のドキュメント][anf-azure-doc] 
* SAP Note [1928533]: 次の情報が含まれています。  
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン
* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2205917]: SUSE Linux Enterprise Server for SAP Applications 向けの推奨の OS 設定が記載されています。
* SAP Note [1944799]: SUSE Linux Enterprise Server for SAP Applications の SAP ガイドラインが記載されています。
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1984787]: SUSE Linux Enterprise Server 12 に関する一般情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* SAP Note [1900823]: SAP HANA ストレージ要件に関する情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [SUSE SAP HA ベスト プラクティス ガイド][suse-ha-guide]: このガイドには、オンプレミスで Netweaver HA と SAP HANA System Replication を設定するために必要なすべての情報が記載されています。 一般的なベースラインとしてこのガイドを使用してください。 このガイドには詳細な情報が提供されています。
* [SUSE High Availability Extension 12 SP3 リリース ノート][suse-ha-12sp3-relnotes]
* [Azure NetApp Files を使用した Microsoft Azure 上の NetApp SAP アプリケーション][anf-sap-applications-azure]
* [NFS を使用した NetApp システムでの SAP HANA](https://www.netapp.com/us/media/tr-4435.pdf)。 構成ガイドには、Azure NetApp Files によって提供される NFS を使用した SAP HANA の設定方法に関する情報が含まれています。


## <a name="overview"></a>概要

HANA の高可用性を実現する 1 つの方法として、ホストの自動フェールオーバーがあります。 ホストの自動フェールオーバーを構成するために、1 つ以上の仮想マシンが HANA システムに追加され、スタンバイ ノードとして構成されます。 アクティブ ノードで障害が発生すると、スタンバイ ノードに自動的に引き継がれます。 これは Azure 仮想マシンで提供される構成では、[Azure NetApp Files の NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) によって実現されます。  

スタンバイ ノードは、すべてのデータベース ボリュームにアクセスできる必要があります。 HANA ボリュームは NFSv4 ボリュームとしてマウントする必要があります。 NFSv4 プロトコルでは、強化されたファイル リースベースのロック メカニズムが `I/O` フェンスに使用されます。 

> [!IMPORTANT]
> サポートされている構成を行うためには、NFSv 4.1 プロトコルを使用して、HANA データ ボリュームとログ ボリュームを NFSv 4.1 ボリュームとしてデプロイし、それらをマウントする必要があります。 スタンバイ ノードを使用した HANA ホストの自動フェールオーバー構成は、NFSv3 ではサポートされていません。

![SAP NetWeaver の高可用性の概要](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

SAP HANA ネットワークに関する推奨事項に従って、1 つの Azure 仮想ネットワーク内に、ストレージ システムとの通信用、内部 HANA のノード間通信用、クライアント通信用の 3 つのサブネットが作成されました。 Azure NetApp ボリュームは、[Azure NetApp Files に委任された](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)別のサブネットにあります。  

この構成例では、サブネットは次のとおりです。  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files インフラストラクチャの設定 

Azure NetApp Files インフラストラクチャの設定を続行する前に、「[Azure NetApp Files のドキュメント][anf-azure-doc]」の内容をよく理解しておいてください。 Azure NetApp Files はいくつかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)で利用できます。 選択した Azure リージョンで、Azure NetApp Files が利用できるかどうかを確認します。  

Azure リージョン別に Azure NetApp Files が利用可能かどうかを確認するには、[Azure リージョン別の Azure NetApp Files の利用可能性][anf-avail-matrix]に関するページを参照してください。  
Azure NetApp Files をデプロイする前に、「[Azure NetApp Files に登録する][anf-register]」の手順に従って、Azure NetApp Files へのオンボードを要求してください。 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files リソースのデプロイ  

以下の手順では、[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) が既にインストールされていることを前提としています。 Azure NetApp Files のリソースと、そのリソースがマウントされる VM は、同じ Azure Virtual Network 内またはピアリングされた Azure Virtual Network 内にデプロイする必要があります。  

1. まだそのようになっていない場合は、[Azure NetApp Files へのオンボード](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)を要求してください。  

2. [NetApp アカウントの作成手順](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)に関するページに従って、選択した Azure リージョン内で NetApp アカウントを作成します。  

3. [Azure NetApp Files の容量プールの設定手順](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)に関するページに従って、Azure NetApp Files の容量プールを設定します。  
この記事で示されている HANA アーキテクチャでは、1 つの Azure NetApp Files の容量プール、Ultra サービス レベルが使用されています。 Azure 上の HANA ワークロードについては、Azure NetApp Files の Ultra または Premium のいずれかの[サービス レベル](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)をお勧めします。  

4. [Azure NetApp Files へのサブネットの委任手順](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)に関するページの説明に従って、サブネットを Azure NetApp Files に委任します。  

5. [Azure NetApp Files 用のボリュームの作成手順](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)に関するページに従って、Azure NetApp Files のボリュームをデプロイします。  ボリュームをデプロイする場合は、**NFSv 4.1** バージョンを必ず選択してください。 現在、NFSv 4.1 にアクセスするには、追加のホワイトリスト登録が必要です。 指定された Azure NetApp Files の[サブネット](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)内にボリュームをデプロイします。 Azure NetApp Files のリソースと Azure VM は、同じ Azure Virtual Network 内またはピアリングされた Azure Virtual Network 内に配置する必要があることに注意してください。 たとえば、<b>HN1</b>-data-mnt00001、<b>HN1</b>-log-mnt00001 などはボリューム名で、nfs://10.23.1.5/<b>HN1</b>-data-mnt00001、nfs://10.23.1.4/<b>HN1</b>-log-mnt00001 などは Azure NetApp Files ボリュームのファイル パスです。  

   1. ボリューム <b>HN1</b>-data-mnt00001 (nfs://10.23.1.5/<b>HN1</b>-data-mnt00001)
   2. ボリューム <b>HN1</b>-data-mnt00002 (nfs://10.23.1.6/<b>HN1</b>-data-mnt00002)
   3. ボリューム <b>HN1</b>-log-mnt00001 (nfs://10.23.1.4/<b>HN1</b>-log-mnt00001)
   4. ボリューム <b>HN1</b>-log-mnt00002 (nfs://10.23.1.6/<b>HN1</b>-log-mnt00002)
   5. ボリューム <b>HN1</b>-shared (nfs://10.23.1.4/<b>HN1</b>-shared)
   
   この例では、HANA データとログ ボリュームごとに個別の Azure NetApp Files が使用されています。 小規模または非運用システムでよりコストを最適化した構成にするために、すべてのデータ マウントとすべてのログ マウントを 1 つのボリュームに配置することができます。  

### <a name="important-considerations"></a>重要な考慮事項

SUSE High Availability アーキテクチャ上で SAP Netweaver 用に Azure NetApp Files を検討するときは、以下の重要な考慮事項に注意してください。

- 最小容量プールは 4 TiB です。  
- 最小ボリューム サイズは 100 GiB です。
- Azure NetApp Files と、Azure NetApp Files のボリュームがマウントされるすべての仮想マシンは、同じ Azure 仮想ネットワーク内、または同じリージョン内の[ピアリングされた仮想ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)内に存在する必要があります。  
- 選択した仮想ネットワークには、Azure NetApp Files に委任されているサブネットがある必要があります。
- Azure NetApp ボリュームのスループットは、「[Azure NetApp Files のサービス レベル](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)」に記載されているように、ボリューム クォータとサービス レベルの機能です。 HANA Azure NetApp ボリュームのサイズを設定するときは、そのスループットが HANA システム要件を満たしていることを確認してください。  
- Azure NetApp Files の[エクスポート ポリシー](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)では、ユーザーが制御できるのは、許可されたクライアント、アクセスの種類 (読み取りおよび書き込み、読み取り専用など) です。 
- Azure NetApp Files 機能は、ゾーンにはまだ対応していません。 現在、Azure NetApp Files 機能は、Azure リージョン内のすべての可用性ゾーンにはデプロイされていません。 Azure リージョンによっては、待ち時間が発生する可能性があることに注意してください。  
- 待ち時間を短縮するには、仮想マシンを Azure NetApp ストレージに近い場所にデプロイすることが重要です。 SAP HANA ワークロードにとって、待ち時間の短縮は重要です。 Microsoft の担当者と協力して、仮想マシンと Azure NetApp Files ボリュームが確実に近接してデプロイされるようにします。  
- 仮想マシン上の <b>sid</b>adm のユーザー ID と `sapsys` のグループ ID は、Azure NetApp Files の構成と一致している必要があります。 

> [!IMPORTANT]
> SAP HANA ワークロードにとって、待ち時間の短縮は重要です。 Microsoft の担当者と協力して、仮想マシンと Azure NetApp Files ボリュームが確実に近接してデプロイされるようにします。  

> [!IMPORTANT]
> 仮想マシンと Azure NetApp の構成の間で、<b>sid</b>adm のユーザー ID と、`sapsys` のグループ ID が一致しない場合は、仮想マシンにマウントされた Azure NetApp ボリューム上のファイルのアクセス許可が `nobody` として表示されます。 Azure NetApp Files に[新しいシステムをオンボード](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)するときに、正しい <b>sid</b>adm のユーザー ID と `sapsys` のグループ ID を指定していることを確認してください。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files 上の HANA データベースのサイズ指定

Azure NetApp ボリュームのスループットは、「[Azure NetApp Files のサービス レベル](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)」に記載されているように、ボリューム サイズとサービス レベルの機能です。 

Azure で SAP のインフラストラクチャを設計する際には、SAP による最小ストレージ要件に注意する必要があります。これは、最小スループット特性につながります。

- I/O サイズが 1 MB で、250 MB/秒の /hana/log で読み取り/書き込みを有効にする  
- I/O サイズが 16 MB および 64 MB の /hana/data で、少なくとも 400 MB/秒の読み取りアクティビティを有効にする  
- I/O サイズが 16 MB および 64 MB の /hana/data で、少なくとも 250 MB/秒の書き込みアクティビティを有効にする  

ボリューム クォータの 1 TiB あたりの [Azure NetApp Files スループットの上限](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)は次のとおりです。
- Premium ストレージ層 - 64 MiB/秒  
- Ultra ストレージ層 - 128 MiB/秒  

データとログの SAP 最小スループット要件を満たすため、および `/hana/shared` のガイドラインに従うと、推奨されるサイズは次のようになります。

| ボリューム | Size<br /> Premium ストレージ層 | Size<br /> Ultra ストレージ層 |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB |
| /hana/data | 6.3 TiB | 3.2 TiB |
| /hana/shared | 4 ワーカー ノードあたり最大 (512 GB、1xRAM) | 4 ワーカー ノードあたり最大 (512 GB、1xRAM) |

Azure NetApp Files Ultra ストレージ層を使用している、この記事に記載されているレイアウトの SAP HANA 構成は、次のようになります。

| ボリューム | Size<br /> Ultra ストレージ層 |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB |
| /hana/log/mnt00002 | 2 TiB |
| /hana/data/mnt00001 | 3.2 TiB |
| /hana/data/mnt00002 | 3.2 TiB |
| /hana/shared | 2 TiB |

> [!NOTE]
> ここで説明されている Azure NetApp Files のサイズ設定の推奨事項は、SAP がそのインフラストラクチャ プロバイダーに表明している最小要件を満たすことを目標としています。 実際の顧客のデプロイとワークロードのシナリオでは、これらは十分ではない場合があります。 これらの推奨事項は開始点として利用し、ご自分の固有のワークロードの要件に合わせて調整してください。  

> [!TIP]
> Azure NetApp Files ボリュームは、ボリュームを `unmount` したり、仮想マシンを停止したり、SAP HANA を停止したりすることなく、動的にサイズ変更することができます。 これにより、予想されるスループット要求と予期しないスループット要求の両方を柔軟に満たすことができます。

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>Azure portal を使用した Linux 仮想マシンのデプロイ

最初に Azure NetApp Files ボリュームを作成する必要があります。 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) で [Azure 仮想ネットワーク サブネット](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)を作成します。 仮想マシンをデプロイします。 追加のネットワーク インターフェイスを作成し、対応する VM にネットワーク インターフェイスを接続します。 各仮想マシンには、3 つの Azure 仮想ネットワーク サブネット (`storage`、`hana`、`client`) に対応する 3 つのネットワーク インターフェイスがあります。  「[複数のネットワーク インターフェイス カードを使用して Linux 仮想マシンを Azure に作成する方法](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)」を参照してください。  

> [!IMPORTANT]
> SAP HANA ワークロードにとって、待ち時間の短縮は重要です。 Microsoft の担当者と協力して、待ち時間の短縮を実現するため、仮想マシンと Azure NetApp Files ボリュームが近接してデプロイされるようにします。 SAP HANA Azure NetApp Files を使用している[新しい SAP HANA システムをオンボードする](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)ときに、必要な情報を送信します。  
> 
次の手順では、リソース グループ、Azure 仮想ネットワーク、3 つの Azure Virtual Network サブネット (`storage`、`hana`、`client`) が既に作成されていることを前提としています。  仮想マシンを展開するときに、ストレージ ネットワーク インターフェイスが仮想マシンのプライマリ インターフェイスになるように、ストレージ サブネットを選択します。 これが不可能な場合は、ストレージ サブネット ゲートウェイ経由で Azure NetApp の委任されたサブネットへの明示的なルートを構成する必要があります。 

> [!IMPORTANT]
> 選択した OS が、使用している特定の VM の種類の SAP HANA に対して認定されていることを確認してください。 SAP HANA 認定 VM の種類と、その種類に対応する OS リリースの一覧は、[SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)に関するページに記載されています。 表示されている VM の種類をクリックすると、特定の VM の種類に対して SAP HANA でサポートされている OS のリリースの一覧が表示され、詳細を確認できます。  

1. SAP HANA 用の可用性セットを作成します。 必ず、更新ドメインの最大数を設定してください。  

2. 3 つの Virtual Machines (**hanadb1**、**hanadb2**、**hanadb3**) を作成します  
   - SAP HANA でサポートされている SLES4SAP イメージを、Azure ギャラリーから使用します。 この例では、SLES4SAP 12 SP4 イメージが使用されています。  
   - 前に作成された SAP HANA 用の可用性セットを選択します。  
   - ストレージ Azure 仮想ネットワーク サブネットを選択します。 [高速ネットワーク](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)を選択します。  
仮想マシンをデプロイすると、ネットワーク インターフェイス名が自動的に生成されます。 ここでは、ストレージ Azure Virtual Network サブネットに接続されたネットワーク インターフェイスを、**hanadb1**、**hanadb2**、**hanadb3** と呼びます。 
3. `hana` 仮想ネットワーク サブネットに対して、仮想マシンごとに 1 つずつ、3 つのネットワーク インターフェイスを作成します。 この例では、**hanadb1-hana**、**hanadb2**、**hanadb3-hana**です。  
4. **クライアント**仮想ネットワーク サブネットに対して、仮想マシンごとに 1 つずつ、3 つのネットワーク インターフェイスを作成します。 この例では、**hanadb1**、**hanadb2-client**、**hanadb3-client**です。  
5. 新しく作成した仮想ネットワーク インターフェイスを対応する仮想マシンに接続します。  

    1. [Azure portal](https://portal.azure.com/#home) で仮想マシンに移動します。  
    2. 左側のナビゲーション ウィンドウで [Virtual Machines] を選択します。 仮想マシン名 (**hanadb1** など) でフィルター処理します。 仮想マシンをクリックします。  
    3. [概要] が表示されている場合は、[停止] を選択して仮想マシンの割り当てを解除します。  
    4. [ネットワーク] を選択し、ネットワーク インターフェイスを接続します。 [ネットワーク インターフェイスの接続] の下にあるドロップダウン リストから、 **`hana`** および**クライアント** サブネットに対して既に作成されているネットワーク インターフェイスを選択します。  保存します。 
    5. 残りの仮想マシンについても同じ手順を繰り返します。 この例では、**hanadb2** と **hanadb3** です。
    6. 今のところ、仮想マシンは停止状態のままにしておきます。 次に、新しく接続されたすべてのネットワーク インターフェイスに対して[高速ネットワーク](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)を有効にします。  

6. **`hana`** および **`client`** サブネットの追加のネットワーク インターフェイスに対して高速ネットワークを有効にします。  

    1. [Azure portal](https://portal.azure.com/#home) で [Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) を開きます。  
    2. 次のコマンドを実行して、 **`hana`** および **`client`** サブネットに接続された、追加のネットワーク インターフェイスに対して高速ネットワークを有効にします。  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. 仮想マシンを起動します。  

    1. 左側のナビゲーション ウィンドウで [Virtual Machines] を選択します。 仮想マシン名 (**hanadb1** など) でフィルター処理します。 仮想マシンをクリックします。  
    2. [概要] が表示されている場合は、[開始] を選択します。  

## <a name="operating-system-configuration-and-preparation"></a>オペレーティング システムの構成と準備

次の項目の先頭には、 **[A]** - 全ノードが該当、 **[1]** - ノード 1 のみ該当、 **[2]** - ノード 2 のみ該当、 **[3]** - ノード 3 のみ該当、のいずれかが付いています。

1. **[A]** 仮想マシン上にホスト ファイルを維持します。 すべてのサブネットのエントリを含めます。 この例では、次のエントリが `/etc/hosts` に追加されています。  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** DHCP とクラウドの構成設定を変更して、意図しないホスト名の変更を回避します。  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** 「[SAP HANA on NetApp AFF Systems with NFS configuration guide](https://www.netapp.com/us/media/tr-4435.pdf)」 (NetApp AFF Systems で NFS を使用した SAP HANA の構成ガイド) で説明されているように、NFS を使用して NetApp 上で SAP HANA を実行するためにオペレーティング システムを準備します。 NetApp 構成設定の構成ファイルを作成します: `/etc/sysctl.d/netapp-hana.conf`。  

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
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Azure 用の Microsoft の構成設定を使用して、構成ファイルを作成します: `/etc/sysctl.d/ms-az.conf`。  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **[A]** 「[SAP HANA on NetApp AFF Systems with NFS configuration guide](https://www.netapp.com/us/media/tr-4435.pdf)」 (NetApp AFF Systems で NFS を使用した SAP HANA の構成ガイド) で推奨されているとおりに sunrpc 設定を調整します。  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Azure NetApp Files ボリュームのマウント

1. **[A]** HANA データベース ボリュームのマウント ポイントを作成します。  
    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** **HN1**-shared で `/usr/sap` 用のノード固有ディレクトリを作成します。  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** NFS ドメイン設定を確認します。 ドメインが **`localdomain`** として構成され、マッピングが **nobody** に設定されていることを確認します。  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** NFSv4 ID マッピングを無効にします。 mount コマンドを実行して、`nfs4_disable_idmapping` が配置されるディレクトリ構造を作成します。  アクセスがカーネル/ドライバー用に予約されるため、/sys/modules の下に手動でディレクトリを作成することはできなくなります。  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** SAP HANA グループとユーザーを手動で作成します。 グループ sapsys とユーザー **hn1**adm の ID は、オンボード時に指定したものと同じ ID に設定する必要があります。 この例では、ID は **1001** に設定されています。 そうしないと、ボリュームにアクセスできなくなります。  グループ sapsys とユーザー アカウント **hn1**adm と sapadm の ID は、すべての仮想マシンで同じである必要があります。  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** 共有 Azure NetApp Files ボリュームをマウントします。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** **hanadb1** にノード固有のボリュームをマウントします。  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** **hanadb2** にノード固有のボリュームをマウントします。  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** **hanadb3** にノード固有のボリュームをマウントします。  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** すべての HANA ボリュームが NFS プロトコル バージョン **NFSv4** でマウントされていることを確認します。  
    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>インストール  

この例では、Azure でスタンバイ ノードを使用したスケールアウト構成で SAP HANA をデプロイするために、HANA 2.0 SP4 を使用しました。  

### <a name="prepare-for-hana-installation"></a>HANA のインストールの準備

1. **[A]** HANA をインストールする前に、ルート パスワードを設定します (ルート パスワードは、インストールが完了した後に無効にすることができます)。 `root` として `passwd` コマンドを実行します。  

2. **[1]** パスワードの入力を求められることなく、**hanadb2** および **hanadb3** に ssh 接続できることを確認します。  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** HANA 2.0 SP4 に必要な追加のパッケージをインストールします。 詳しくは、SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) を参照してください。 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2、3]** SAP HANA の `data` と `log` のディレクトリの所有権を **hn1**adm に変更します。   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA のインストール

1. **[1]** 「[SAP HANA 2.0 Installation and Update guide](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)」 (SAP HANA 2.0 のインストールと更新ガイド) の指示に従って、SAP HANA をインストールします。 この例では、マスター、1 つのワーカー、および 1 つのスタンバイ ノードを使用して、SAP HANA スケールアウトをインストールします。  
   HANA のインストール ソフトウェア ディレクトリから **hdblcm** プログラムを起動します。 `internal_network` パラメーターを使用して、内部 HANA のノード間通信に使用されるサブネットのアドレス空間を渡します。  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   プロンプトで次の値を入力します。

     * アクションを選択します。「**1**」を入力します (インストールの場合)
     * Select additional components for installation (追加でインストールするコンポーネントの選択):「**2, 3**」を入力します
     * Enter Installation Path\(インストール パスを入力\):Enter キーを押します (既定値は /hana/shared)
     * Enter Local Host Name\(ローカル ホスト名を入力\):Enter キーを押して既定値をそのまま使用します
     * Do you want to add hosts to the system?\(システムにホストを追加しますか?\) 「**y**」を入力します
     * Enter coma-separated host names to add\(追加するホスト名をコンマで区切って入力\): **hanadb2, hanadb3**
     * Enter Root User Name\(ルート ユーザー名を入力\) [root]: Enter キーを押して既定値をそのまま使用します
     * Enter Root User Password\(ルート ユーザー パスワードを入力\):ルートのパスワードを入力します
     * Select roles for host hanadb2\(ホスト hanadb2 のロールを選択\):「**1**」を入力します (ワーカーの場合)
     * Enter Host Failover Group for host hanadb2\(ホスト hanadb2 のホスト フェールオーバー グループを入力\) [default]:Enter キーを押して既定値をそのまま使用します
     * Enter Storage Partition Number for host hanadb2\(ホスト hanadb2 のストレージ パーティション番号を入力\) [<<assign automatically>>]:Enter キーを押して既定値をそのまま使用します
     * Enter Worker Group for host hanadb2\(ホスト hanadb2 のワーカー グループを入力\) [default]:Enter キーを押して既定値をそのまま使用します
     * Select roles for host hanadb3\(ホスト hanadb3 のロールを選択\):「**2**」を入力します (スタンバイの場合)
     * Enter Host Failover Group for host hanadb3 [default]\(ホスト hanadb3 のホスト フェールオーバー グループを入力 [既定]\):Enter キーを押して既定値をそのまま使用します
     * Enter Worker Group for host hanadb3\(ホスト hanadb3 のワーカー グループを入力\) [default]:Enter キーを押して既定値をそのまま使用します
     * Enter SAP HANA System ID (SAP HANA のシステム ID を入力):「**HN1**」を入力します
     * Enter Instance Number\(インスタンス番号を入力) [00] :「**03**」を入力します
     * Enter Local Host Worker Group\(ローカル ホスト ワーカー グループを入力\) [default]:Enter キーを押して既定値をそのまま使用します
     * Select System Usage / Enter index\(システム使用率の選択/インデックスの入力\) [4]:「**4**」を入力します (カスタムの場合)
     * Enter Location of Data Volumes (データ ボリュームの場所の入力) [/hana/data/HN1]:Enter キーを押して既定値をそのまま使用します
     * Enter Location of Log Volumes (ログ ボリュームの場所の入力) [/hana/log/HN1]:Enter キーを押して既定値をそのまま使用します
     * Restrict maximum memory allocation? (メモリの最大割り当てを制限しますか?) [n]:「**n**」を入力します。
     * Enter Certificate Host Name For Host hanadb1\(ホスト hanadb1 の証明書のホスト名を入力\) [hanadb1]:Enter キーを押して既定値をそのまま使用します
     * Enter Certificate Host Name For Host hanadb2\(ホスト hanadb2 の証明書のホスト名を入力\) [hanadb2]:Enter キーを押して既定値をそのまま使用します
     * Enter Certificate Host Name For Host hanadb3\(ホスト hanadb3 の証明書のホスト名を入力\) [hanadb3]:Enter キーを押して既定値をそのまま使用します
     * Enter System Administrator (hn1adm) Password\(システム管理者 (hn1adm) のパスワードを入力\):パスワードを入力する
     * Enter System Database User (SYSTEM) Password\(システム データベース ユーザー (SYSTEM) のパスワードを入力\):SYSTEM のパスワードを入力します
     * Confirm System Database User (SYSTEM) Password\(システム データベース ユーザー (SYSTEM) のパスワードを確認\):SYSTEM のパスワードを入力します
     * Restart system after machine reboot? (コンピューターの再起動後にシステムを再起動しますか?) [n]:「**n**」を入力します。 
     * Do you want to continue\(続行しますか?\) (y/n):概要を検証し、すべて問題がなさそうな場合は「**y**」を入力します


2. **[1]** global.ini を確認します  

   global.ini を表示し、内部 SAP HANA のノード間通信が正しく構成されていることを確認します。 セクション **[communication]** を確認します。 ここには、 **`hana`** サブネットのアドレス空間があり、`listeninterface` が `.internal`に設定されている必要があります。 セクション **[internal_hostname_resolution]** を確認します。 ここには、 **`hana`** サブネットに属する HANA 仮想マシンの IP アドレスが含まれている必要があります。  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** ホスト マッピングを追加して、クライアントの通信でクライアントの IP アドレスが確実に使用されるようにします。 セクション `public_host_resolution` を追加し、クライアント サブネットから対応する IP アドレスを追加します。  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** SAP HANA を再起動して、変更をアクティブにします。  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** クライアント インターフェイスが **クライアント** サブネットの IP アドレスを使用して通信するようになっていることを確認します。  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   構成を確認する方法の詳細については、SAP Note「[2183363 - SAP HANA 内部ネットワークの構成](https://launchpad.support.sap.com/#/notes/2183363)」を参照してください。  

6. 基になる Azure NetApp Files ストレージ向けに SAP HANA を最適化するには、次の SAP HANA パラメーターを設定します。

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks` **all**

   詳細については、「[SAP HANA on NetApp AFF Systems with NFS configuration guide](https://www.netapp.com/us/media/tr-4435.pdf)」 (NetApp AFF Systems で NFS を使用した SAP HANA の構成ガイド) を参照してください。 

   SAP HANA 2.0 以降のシステムでは、`global.ini` でパラメーターを設定できます。 SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930) をご覧ください。  
   SAP HANA 1.0 システム、SPS12 までのバージョンでは、SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798) に記載されているように、インストール時にこれらのパラメーターを設定できます。  

7. Azure NetApp Files で使用されるストレージには、ファイル サイズの制限 (16 TB) があります。 SAP HANA では、ストレージの制限が暗黙的に認識されず、ファイル サイズの上限の 16 TB に達したときに新しいデータ ファイルが自動的に作成されることはありません。 SAP HANA では 16 TB を超えてファイルを拡張しようとするため、エラーとなり、最終的にはインデックス サーバーがクラッシュします。 

   > [!IMPORTANT]
   > SAP HANA がストレージ サブシステムの [16 TB の制限](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits)を超えてデータ ファイルを拡張しようとするのを防ぐには、`global.ini` で次のパラメーターを設定します。  
   > -  datavolume_striping=true
   > - datavolume_striping_size_gb = 15000 詳細については、SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005)を参照してください。
   > SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285) に注意してください。 

## <a name="test-sap-hana-failover"></a>SAP HANA フェールオーバーのテスト 

1. SAP HANA ワーカー ノードでノード クラッシュをシミュレートする  

   ノード クラッシュをシミュレートする前に、**hn1**adm として次のコマンドを実行して環境の状態をキャプチャします。  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   ワーカー ノード (この場合は **hanadb2**) で次のコマンドを root として実行し、ノード クラッシュをシミュレートします。  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   システムのフェールオーバーの完了を監視します。 フェールオーバーで状態のキャプチャが完了すると、次のような状態が示されます。  
   <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > SAP HANA フェールオーバーによる遅延を回避するには、ノードでカーネル パニックが発生したときに、**すべて**の HANA 仮想マシンで `kernel.panic` を 20 秒に設定します。 構成は `/etc/sysctl` で行われます。 仮想マシンを再起動して、変更をアクティブにします。 この変更が行われていないと、ノードでカーネル パニックが発生したときに、フェールオーバーに 10 分以上かかることがあります。  

2. ネーム サーバーを強制終了する  
   テストの前に、**hn1**adm として次のコマンドを実行して、環境の状態を確認します。  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   アクティブなマスター ノード (この場合は **hanadb1**) で **hn1**adm として次のコマンドを実行します。  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   スタンバイ ノード **hanadb3** がマスター ノードとして引き継ぎます。 フェールオーバー テスト完了後のリソースの状態:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   **hanadb1** で (つまり、ネーム サーバーが強制終了されたのと同じ仮想マシンで) HANA インスタンスをもう一度開始します。 **hanadb1** ノードが環境に再び参加し、スタンバイ ロールを保持します。  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **hanadb1**で SAP HANA が起動されると、次のような状態になります。  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   ここで、現在アクティブなマスター ノード (つまり、hanadb3) でネーム サーバーをもう一度強制終了します。  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   ノード **hanadb1** が、マスター ノードのロールを再開します。 フェールオーバー テスト完了後の状態は、次のようになります。
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   **hanadb3** で SAP HANA を起動します。スタンバイ ノードとして機能する準備が整います。  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **hanadb3** で SAP HANA を起動後の状態。  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>次の手順

* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* 高可用性を確立し、SAP のディザスター リカバリーを計画する方法について学びます 
* HANA on Azure (大規模なインスタンス)。[Azure 上での SAP HANA (大規模なインスタンス) の高可用性およびディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)に関するページを参照してください。
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]に関するページを参照してください。
