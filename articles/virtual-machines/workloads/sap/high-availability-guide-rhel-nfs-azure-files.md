---
title: Azure Files 上の NFS での RHEL 上の SAP NW 用の Azure VM の高可用性 | Microsoft Docs
description: Azure Files 上の NFS を使用して Azure 仮想マシン (VM) RHEL 上の SAP NW の高可用性を確立します。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.service: virtual-machines-sap
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/16/2021
ms.author: radeltch
ms.openlocfilehash: badc6696936b3cdf99b91a5abf0d95217ec9b2e7
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557214"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-with-nfs-on-azure-files"></a>Azure Files 上の NFS を使用した Red Hat Enterprise Linux 上の Azure VM での SAP NetWeaver の高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[afs-azure-doc]:../../../storage/files/storage-files-introduction.md
[afs-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2772999]:https://launchpad.support.sap.com/#/notes/2772999
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[2360818]:https://launchpad.support.sap.com/#/notes/2360818

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html


[sap-hana-ha]:sap-hana-high-availability-rhel.md


この記事では、[Azure Files 上の NFS](../../../storage/files/files-nfs-protocol.md) を使用して、VM のデプロイと構成、クラスター フレームワークのインストール、HA SAP NetWeaver システムのインストールを行う方法について説明します。 この構成例では、Red Hat Enterprise Linux (RHEL) で実行される VM を使用します。  

## <a name="prerequisites"></a>前提条件

* [Azure Files のドキュメント][afs-azure-doc] 
* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン
* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2002167]: Red Hat Enterprise Linux 7.x 用の推奨 OS 設定が記載されています。
* SAP Note [2772999]: Red Hat Enterprise Linux 8.x 用の推奨 OS 設定が記載されています。
* SAP Note [2009879] には、Red Hat Enterprise Linux 用の SAP HANA ガイドラインが記載されています
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [Pacemaker クラスターでの SAP Netweaver](https://access.redhat.com/articles/3150081)
* 一般的な RHEL ドキュメント
  * [高可用性アドオンの概要](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性アドオンの管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性アドオンの参照](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL 7.5 内でスタンドアロン リソースを使用して SAP Netweaver のために ASCS/ERS を構成する](https://access.redhat.com/articles/3569681)
  * [RHEL 上の Pacemaker で SAP S/4HANA ASCS/ERS と Standalone Enqueue Server 2 (ENSA2) を構成する](https://access.redhat.com/articles/3974941)
* Azure 固有の RHEL ドキュメント:
  * [RHEL 高可用性クラスターに関するポリシーをサポート - クラスター メンバーとしての Microsoft Azure Virtual Machines](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure 上で Red Hat Enterprise Linux 7.4 (およびそれ以降) の高可用性クラスターをインストールして構成する](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>概要

SAP NetWeaver アプリケーション レイヤーをデプロイするには、環境内に `/sapmnt/SID` や `/usr/sap/trans` のような共有ディレクトリが必要です。 さらに、HA SAP システムをデプロイする場合は、`/sapmnt/SID` や `/usr/sap/SID/ASCS` のようなファイル システムを保護し、高可用性にする必要があります。

現在では、これらのファイル システムを [Azure Files 上の NFS](../../../storage/files/files-nfs-protocol.md) に配置できます。 Azure Files 上の NFS は、HA ストレージ ソリューションです。 このソリューションは、同期ゾーン冗長ストレージ (ZRS) を提供し、Availability Zones にデプロイされた SAP ASCS/ERS インスタンスに向いています。  SAP Netweaver セントラル サービス (ASCS/SCS) などの単一障害点コンポーネントを保護するには、引き続き Pacemaker クラスターが必要です。  

構成とインストール コマンドの例では、次のインスタンス番号を使用します。

| インスタンス名 | インスタンス番号 |
| ---------------- | ------------------ |
| ABAP SAP セントラル サービス (ASCS) | 00 |
| ERS | 01 |
| プライマリ アプリケーション サーバー (PAS) | 02 |
| 追加のアプリケーション サーバー (AAS) | 03 |
| SAP システム識別子 | NW1 |

:::image type="complex" source="./media/high-availability-guide-rhel/high-availability-guide-rhel-nfs-azure-files.png" alt-text="Azure Files 上の NFS を使用する SAP NetWeaver の高可用性":::
   この図は、一般的な SAP Netweaver HA アーキテクチャを示しています。 "sapmnt" と "saptrans" のファイル システムは、Azure Files 上の NFS 共有にデプロイされます。 SAP セントラル サービスは Pacemaker クラスターによって保護されます。 クラスター化された VM は、Azure ロード バランサーの背後に配置されます。 NFS 共有は、プライベート エンド ポイントを介してマウントされます。
:::image-end:::

## <a name="prepare-infrastructure"></a>インフラストラクチャの準備

このドキュメントは、[Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)、サブネット、およびリソース グループが既にデプロイ済みであることを前提としています。

1. VM をデプロイします。 可用性セットまたは可用性ゾーン (Azure リージョンでこれらのオプションがサポートされている場合) に VM をデプロイできます。 VM に追加の IP アドレスが必要な場合は、2 つ目の NIC をデプロイしてアタッチします。 プライマリ NIC にセカンダリ IP アドレスを追加しないでください。 [Azure Load Balancer のフローティング IP では、このシナリオがサポートされません](../../../load-balancer/load-balancer-multivip-overview.md#limitations)。  
 
2. 仮想 IP に対して、Azure [ロード バランサー](../../../load-balancer/load-balancer-overview.md)をデプロイして構成します。 [Standard ロード バランサー](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)を使用することをお勧めします。 

    1. 2 つのフロントエンド IP を構成します。1 つは ASCS 用 (`10.90.90.10`)、もう 1 つは ERS 用 (`10.90.90.9`) です。
    2. バックエンド プールを作成し、クラスターの一部になる両方の VM を追加します。
    3. ASCS 用の正常性プローブを作成します。 プローブ ポートは `62000` です。 ERS 用のプローブ ポートを作成します。 ERS プローブ ポートは `62101` です。 後で Pacemaker リソースを構成するときに、一致するプローブ ポートを使用する必要があります。
    4. ASCS と ERS の負荷分散規則を構成します。 対応するフロント IP、正常性プローブ、バックエンド プールを選びます。 HA ポートを選択し、アイドル タイムアウトを 30 分に増やし、フローティング IP を有効にします。

### <a name="deploy-azure-files-storage-account-and-nfs-shares"></a>Azure Files ストレージ アカウントと NFS 共有のデプロイ 

Azure Files 上の NFS は、[Azure Files Premium ストレージ][afs-azure-doc]の上で実行されます。 Azure Files 上の NFS を設定する前に、「[NFS 共有を作成する方法](../../../storage/files/storage-files-how-to-create-nfs-shares.md?tabs=azure-portal)」をご覧ください。    

Azure リージョン内の冗長性には、次の 2 つのオプションがあります。

- [ローカル冗長ストレージ (LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage)。ローカルのゾーン内同期データ レプリケーションを提供します。
- [ゾーン冗長ストレージ (ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage)。リージョン内の 3 つの[可用性ゾーン](../../../availability-zones/az-overview.md)間でデータを同期的に複製します。

選択した Azure リージョンで、適切な冗長性を備えた Azure Files 上の NFS 4.1 が提供されていることを確認してください。 [リージョン別に利用可能な Azure Files][afs-avail-matrix] で **Premium Files Storage** を確認してください。 ZRS のメリットがあるシナリオの場合は、[お使いの Azure リージョンで ZRS を使用した Premium ファイル共有がサポートされていることを確認してください](../../../storage/common/storage-redundancy.md#zone-redundant-storage)。

[Azure プライベート エンドポイント](../../../storage/files/storage-files-networking-endpoints.md?tabs=azure-portal)を使用して、Azure Storage アカウントにアクセスすることをお勧めします。 Azure Files ストレージ アカウントのエンドポイントと、NFS 共有をマウントする必要がある VM を、同じ Azure VNet またはピアリングされた Azure VNet にデプロイしてください。

1. `sapafsnfs` という名前の File Storage アカウントをデプロイします。 この例では、ZRS を使用します。 このプロセスに慣れていない場合は、Azure portal での「[ストレージ アカウントの作成](../../../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal#create-a-storage-account)」を参照してください。
1. **[基本]** タブで、次の設定を使用します。

    1. **[ストレージ アカウント名]** に、`sapafsnfs` と入力します。
    
    1. **[パフォーマンス]** では **[Premium]** を選択します。
    
    1. **[Premium account type]\(Premium アカウントの種類\)** で **[FileStorage]** を選択します。
    
    1. **[レプリケーション]** で、ゾーン冗長 (ZRS) を選択します。

1. **[次へ]** を選択します。

1. **[詳細設定]** タブで、 **[REST API 操作の安全な転送を必須にする]** の選択を解除します。 このオプションの選択を解除しないと、NFS 共有を VM にマウントできません。 マウント操作がタイム アウトします。

1. **[次へ]** を選択します。

1. **[ネットワーク]** セクションで、これらの設定を構成します。 

    1. **[ネットワーク接続]** の **[接続方法]** で、 **[プライベート エンドポイント]** を選びます。  
    
    1. **[プライベート エンドポイント]** で **[プライベート エンドポイントの追加]** を選びます。
    
1. **[プライベート エンドポイントの作成]** ウィンドウで、**サブスクリプション**、**リソース グループ**、**場所** を選択します。 
    
    **名前** には、`sapafsnfs_pe`を入力します。
        
    **[ストレージ サブリソース]** で **[ファイル]** を選択します。
        
    **[ネットワーク]** の **[仮想ネットワーク]** で、使用する VNet とサブネットを選びます。 ここでも、SAP VM がある VNet またはピアリングされた VNet を使用できます。
       
    **[プライベート DNS 統合]** では、 **[プライベート DNS ゾーンと統合する]** の既定のオプション **[はい]** をそのまま使用します。 お使いの **プライベート DNS ゾーン** を選択します。
        
    **[OK]** を選択します。
        
1. **[ネットワーク]** タブに戻り、 **[次へ]** を選びます。

1. **[データ保護]** タブで、すべての既定の設定をそのまま維持します。 

1. **[確認および作成]** を選択して構成を検証します。

1. 検証の終了を待ちます。 問題があれば、続行する前に修正してください。

1. **[確認および作成]** タブで、 **[作成]** を選択します。


次に、作成したストレージ アカウントに NFS 共有をデプロイします。 この例では、`sapnw1` と `saptrans` の 2 つの NFS 共有があります。    
1. [Azure portal](https://portal.azure.com) にサインインします。

1. **ストレージ アカウント** を選択するか、検索します。 

1. **[ストレージ アカウント]** ページで、 **[sapafsnfs]** を選びます。

1.  **sapafsnfs** のリソース メニューで、 **[データ ストレージ]** の下の **[ファイル共有]** を選びます。

1. **[ファイル共有]** ページで、 **[ファイル共有]** を選択します。

   1. **[名前]** に、「`sapnw1`」、「`saptrans`」と入力します。
   1.  適切な共有サイズを選択します。 たとえば、**128 GB** です。  共有に格納されるデータのサイズ、IOPS、スループットの要件を考慮してください。  詳細については、[Azure ファイル共有のターゲット](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets)に関するページをご覧ください。
   1. プロトコルとして **[NFS]** を選びます。
   1. **[ルート スカッシュなし]** を選びます。  そうしないと、VM に共有をマウントするときに、ファイルの所有者またはグループが表示できません。

   > [!IMPORTANT]
   > 上記の共有サイズは単なる例です。 共有のサイズを必ず適切に設定してください。 サイズは、共有に格納されるデータのサイズだけでなく、IOPS とスループットの要件にも基づいて決定してください。 詳細については、[Azure ファイル共有のターゲット](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets)に関するページを参照してください。  

   NFS 経由でマウントする必要がない SAP ファイル システムを [Azure ディスク ストレージ](../../disks-types.md#premium-ssds)にデプロイすることもできます。 この例では、`/usr/sap/NW1/D02` と `/usr/sap/NW1/D03` を Azure ディスク ストレージにデプロイできます。 

### <a name="important-considerations-for-nfs-on-azure-files-shares"></a>Azure Files 共有上の NFS に関する重要な考慮事項

Azure Files 上の NFS を使用したデプロイを計画する際は、次の重要な点を考慮してください。  

- 共有の最小サイズは 100 GiB です。 [プロビジョニングされた共有の容量](../../../storage/files/understanding-billing.md#provisioned-model)に対してのみ料金が発生します。 
- 容量の要件だけでなく、IOPS とスループットの要件にも基づいて NFS 共有のサイズを決定してください。 詳細については、[Azure ファイル共有のターゲット](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets)に関するページを参照してください。
- ワークロードをテストしてサイズ設定を検証し、パフォーマンス目標を満たしていることを確認してください。 Azure Files 上の NFS のパフォーマンスの問題のトラブルシューティング方法については、[Azure ファイル共有のパフォーマンスのトラブルシューティング](../../../storage/files/storage-troubleshooting-files-performance.md)に関する記事を参照してください。
- SAP J2EE システムの場合、Azure Files の NFS に `/usr/sap/<SID>` を配置することはサポートされません。
- SAP システムのバッチ ジョブの負荷が高い場合は、ジョブ ログが数百万件になる可能性があります。 SAP のバッチ ジョブ ログがファイル システムに格納されている場合は、`sapmnt` 共有のサイズ設定に特に注意してください。 SAP_BASIS 7.52 の時点では、バッチ ジョブ ログの既定の動作では、データベースに格納されます。 詳細については、[データベースのジョブ ログ][2360818]に関するページを参照してください。     
- SAP システムごとに個別の `sapmnt` 共有をデプロイする
- 他のアクティビティ (インターフェイスなど) には `sapmnt` 共有や `saptrans` を使用しない
- 他のアクティビティ (インターフェイスなど) には `saptrans` 共有や `sapmnt` を使用しない
- 1 つのストレージ アカウントに統合する共有の SAP システムの数が多くなり過ぎないようにします。 [ストレージ アカウントのパフォーマンス スケール ターゲット](../../../storage/files/storage-files-scale-targets.md#storage-account-scale-targets)も用意されています。 ストレージ アカウントの制限を超えないことにも注意してください。
- 通常、1 つのストレージ アカウントに 5 つを超える SAP システムの共有を統合しないようにしてください。 このガイドラインは、ストレージ アカウントの制限を超過することを回避し、パフォーマンス分析を簡略化するのに役立ちます。   
- 通常は、非実稼働と実稼働の SAP システムの共有を同じストレージ アカウントに混在しないようにします。
- [NFS クライアントの機能強化](../../../storage/files/storage-troubleshooting-files-nfs.md#ls-hangs-for-large-directory-enumeration-on-some-kernels)の恩恵を受けるために、RHEL 8.4 以上にデプロイすることをお勧めします。   
- プライベート エンドポイントを使用してください。 万が一ゾーン障害が発生した場合、NFS セッションは自動的に正常なゾーンにリダイレクトされます。 NFS 共有を VM に再マウントする必要はありません。
- VM を Availability Zones にデプロイする場合は、ZRS をサポートする Azure リージョンで[ストレージ アカウントと ZRS](../../../storage/common/storage-redundancy.md#zone-redundant-storage) を使用します。 
- Azure Files では現在、ディザスター リカバリー シナリオにおけるリージョン間の自動レプリケーションはサポートされていません。

## <a name="setting-up-ascs"></a>(A)SCS のセットアップ

この例では、[Azure portal](https://portal.azure.com/#home) を使用してリソースを手動でデプロイします。

### <a name="deploy-azure-load-balancer-via-azure-portal"></a>Azure portal で Azure Load Balancer をデプロイする

SAP システム用の VM をデプロイした後、ロード バランサーを作成します。 次に、バックエンド プール内の VM を使用します。

1. 内部の Standard ロード バランサーを作成します。
   1. フロントエンド IP アドレスを作成します
      1. ASCS の IP アドレス 10.90.90.10
         1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
         1. 新しいフロントエンド IP プールの名前を入力します (例: **frontend.NW1.ASCS**)
         1. [割り当て] を [静的] に設定し、IP アドレスを入力します (例: **10.90.90.10**)
         1. [OK] をクリックします。
      1. ASCS ERS の IP アドレス 10.90.90.9
         * 上記の "a" 以下の手順を繰り返して、ERS の IP アドレスを作成します (例: **10.90.90.9** と **frontend.NW1.ERS**)
   1. バックエンド プールの作成
      1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
      1. 新しいバックエンド プールの名前を入力します (例: **backend.NW1**)
      1. [仮想マシンの追加] をクリックします。
      1. 仮想マシンを選択します。
      1. (A)SCS クラスターの仮想マシンとその IP アドレスを選択します。
      1. [追加] をクリックします。  
      
   1. 正常性プローブを作成します
      1. ASCS のポート 620 **00**
         1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
         1. 新しい正常性プローブの名前を入力します (例: **health.NW1.ASCS**)
         1. プロトコルに TCP、ポートに 620 **00** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
         1. [OK] をクリックします
      1. ASCS ERS のポート 621 **01**
            * 上記の "c" 以下の手順を繰り返して、ERS の正常性プローブを作成します (例: 621 **01** および **health.NW1.ERS**)
   1. 負荷分散規則
      1. ASCS のバックエンド プールの作成
         1. ロード バランサーを開き、負荷分散規則 を選択して [追加] をクリックします
         1. 新しいロード バランサー規則の名前を入力します (例: **lb.NW1.ASCS**)
         1. 前に作成した ASCS 用のフロントエンド IP アドレス、バックエンド プール、および正常性プローブを選択します (例: **frontend.NW1.ASCS**、**backend.NW1**、**health.NW1.ASCS**)
         1. **[HA ポート]** を選択します
         1. **Floating IP を有効にします**
         1. [OK] をクリックします
         * 上記の手順を繰り返して、ERS の負荷分散規則を作成します (例: **lb.NW1.ERS**)
1. または、シナリオに基本的なロード バランサー (内部) が必要な場合は、次の手順に従ってください。  
   1. フロントエンド IP アドレスを作成します
      1. ASCS の IP アドレス 10.90.90.10
         1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
         1. 新しいフロントエンド IP プールの名前を入力します (例: **frontend.NW1.ASCS**)
         1. [割り当て] を [静的] に設定し、IP アドレスを入力します (例: **10.90.90.10**)
         1. [OK] をクリックします。
      1. ASCS ERS の IP アドレス 10.90.90.9
         * 上記の "a" 以下の手順を繰り返して、ERS の IP アドレスを作成します (例: **10.90.90.9** と **frontend.NW1.ERS**)
   1. バックエンド プールの作成
      1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
      1. 新しいバックエンド プールの名前を入力します (例: **backend.NW1**)
      1. [仮想マシンの追加] をクリックします。
      1. 前に作成した ASCS 用の可用性セットを選択します 
      1. (A)SCS クラスターの仮想マシンを選択します
      1. [OK] をクリックします
   1. 正常性プローブを作成します
      1. ASCS のポート 620 **00**
         1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
         1. 新しい正常性プローブの名前を入力します (例: **health.NW1.ASCS**)
         1. プロトコルに TCP、ポートに 620 **00** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
         1. [OK] をクリックします
      1. ASCS ERS のポート 621 **01**
            * 上記の "c" 以下の手順を繰り返して、ERS の正常性プローブを作成します (例: 621 **01** および **health.NW1.ERS**)
   1. 負荷分散規則
      1. ASCS の 32 **00** TCP
         1. ロード バランサーを開き、負荷分散規則 を選択して [追加] をクリックします
         1. 新しいロード バランサー規則の名前を入力します (例: **lb.NW1.ASCS.3200**)
         1. 前に作成した ASCS 用のフロントエンド IP アドレス、バックエンド プール、および正常性プローブを選択します (例: **frontend.NW1.ASCS**)
         1. プロトコルは **TCP** のままにし、ポートに「**3200**」を入力します
         1. アイドル タイムアウトを 30 分に増やします
         1. **Floating IP を有効にします**
         1. [OK] をクリックします
      1. ASCS の追加のポート
         * ASCS のポート 36 **00**、39 **00**、81 **00**、5 **00** 13、5 **00** 14、5 **00** 16 と TCP に対して上記の手順を繰り返します
      1. ASCS ERS の追加のポート
         * ASCS ERS のポート 32 **01**、33 **01**、5 **01** 13、5 **01** 14、5 **01** 16 と TCP に対して上記の "d" 以下の手順を繰り返します

      
      > [!IMPORTANT]
      > フローティング IP は、負荷分散シナリオの NIC セカンダリ IP 構成ではサポートされていません。 詳細については、[Azure Load Balancer の制限事項](../../../load-balancer/load-balancer-multivip-overview.md#limitations)に関する記事を参照してください。 VM に追加の IP アドレスが必要な場合は、2 つ目の NIC をデプロイします。  

      > [!Note]
      > パブリック IP アドレスのない VM が、内部 (パブリック IP アドレスがない) Standard の Azure Load Balancer のバックエンド プール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。 送信接続を実現する方法の詳細については、「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)」を参照してください。  

      > [!IMPORTANT]
      > Azure Load Balancer の背後に配置された Azure VM では TCP タイムスタンプを有効にしないでください。 TCP タイムスタンプを有効にすると正常性プローブが失敗することになります。 パラメーター **net.ipv4.tcp_timestamps** は **0** に設定します。 詳しくは、「[Load Balancer の正常性プローブ](../../../load-balancer/load-balancer-custom-probe-overview.md)」を参照してください。


### <a name="create-pacemaker-cluster"></a>Pacemaker クラスターの作成

「[Setting up Pacemaker on Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md)」 (Azure で Red Hat Enterprise Linux に Pacemaker を設定する) に記載の手順に従って、この (A)SCS サーバーに対して基本的な Pacemaker クラスターを作成します。

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver のインストールの準備

次の各手順の先頭には、 **[A]** - 全ノードが該当、 **[1]** - ノード 1 のみ該当、 **[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** ホスト名解決を設定します

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます

    ```bash
    sudo vi /etc/hosts
    ```

   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します

    ```bash
     # IP address of cluster node 1
     10.90.90.7    sap-cl1
     # IP address of cluster node 2
     10.90.90.8     sap-cl2
     # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
     10.90.90.10   sapascs
     # IP address of the load balancer frontend configuration for SAP Netweaver ERS
     10.90.90.9    sapers
    ```

2. **[A]** NFS クライアントとその他の要件をインストールします

    ```bash
    sudo yum -y install nfs-utils resource-agents resource-agents-sap
    ```

3. **[1]** NFS 共有に SAP ディレクトリを作成します。  
   NFS 共有 **sapnw1** をいずれかの VM に一時的にマウントし、入れ子になったマウント ポイントとして使用される SAP ディレクトリを作成します。  

    ```bash
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    sudo mount -t nfs sapnfs.file.core.windows.net:/sapnfsafs/sapnw1 /saptmp -o vers=4,minorversion=1,sec=sys
    # create the SAP directories
    sudo cd /saptmp
    sudo mkdir -p sapmntNW1
    sudo mkdir -p usrsapNW1ascs
    sudo mkdir -p usrsapNW1ers
    sudo mkdir -p usrsapNW1sys
    # unmount the volume and delete the temporary directory
    cd ..
    sudo umount /saptmp
    sudo rmdir /saptmp
    ``` 

4. **[A]** 共有ディレクトリを作成します

    ```bash
    sudo mkdir -p /sapmnt/NW1
    sudo mkdir -p /usr/sap/trans
    sudo mkdir -p /usr/sap/NW1/SYS
    sudo mkdir -p /usr/sap/NW1/ASCS00
    sudo mkdir -p /usr/sap/NW1/ERS01
    
    sudo chattr +i /sapmnt/NW1
    sudo chattr +i /usr/sap/trans
    sudo chattr +i /usr/sap/NW1/SYS
    sudo chattr +i /usr/sap/NW1/ASCS00
    sudo chattr +i /usr/sap/NW1/ERS01
    ```

5. **[A]** resource-agents-sap のバージョンを確認します

   インストールされている resource-agents-sap パッケージのバージョンが、3.9.5-124.el7 以上であることを確認します
    ```
    sudo yum info resource-agents-sap
   ```


6. **[A]** マウント エントリを追加します

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1sys/ /usr/sap/NW1/SYS  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```

7. **[A]** スワップ ファイルを構成します

    ```bash
    sudo vi /etc/waagent.conf
    
    # Set the property ResourceDisk.EnableSwap to y
    # Create and use swapfile on resource disk.
    ResourceDisk.EnableSwap=y
    
    # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
    # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
    # Size of the swapfile.
    ResourceDisk.SwapSizeMB=2000
    ```

   エージェントを再起動して変更をアクティブにします

    ```bash
    sudo service waagent restart
    ```

8. **[A]** RHEL 構成

   RHEL 7.x 用の SAP Note [2002167] または RHEL 8.x 用の SAP Note [2772999] の説明に従って RHEL を構成します。  

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS のインストール

1. **[1]** クラスターの既定のプロパティの構成

    ```bash
    # If using RHEL 7.X
    pcs resource defaults resource-stickiness=1
    pcs resource defaults migration-threshold=3
    # If using RHEL 8.X
    pcs resource defaults update resource-stickiness=1
    pcs resource defaults update migration-threshold=3
    ```

1. **[1]** ASCS インスタンス用の仮想 IP リソースと正常性プローブを作成します

    ```bash
    sudo pcs node standby sap-cl2
   
    sudo pcs resource create fs_NW1_ASCS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ascs' \
      directory='/usr/sap/NW1/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
      op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
      --group g-NW1_ASCS
   
    sudo pcs resource create vip_NW1_ASCS IPaddr2 \
      ip=10.90.90.10 cidr_netmask=24 \
      --group g-NW1_ASCS
   
    sudo pcs resource create nc_NW1_ASCS azure-lb port=62000 \
      --group g-NW1_ASCS
    ```

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

    ```bash
    sudo pcs status
    
    # Node sap-cl2: standby
    # Online: [ sap-cl1 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl1
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl1 
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    ```

1. **[1]** SAP NetWeaver ASCS をインストールします  

   root として SAP NetWeaver ASCS を最初のノードにインストールします。その際、ASCS のロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名 (たとえば、**sapascs**、**10.90.90.10**) と、ロード バランサーのプローブに使用したインスタンス番号 (たとえば、**00**) を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

    ```bash
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
    sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

    インストールで /usr/sap/**NW1**/ASCS **00** へのサブフォルダーの作成に失敗する場合は、ASCS **00** フォルダーの所有者とグループを設定し、もう一度試してください。

    ```bash
    sudo chown nw1adm /usr/sap/NW1/ASCS00
    sudo chgrp sapsys /usr/sap/NW1/ASCS00
    ```

1. **[1]** ERS インスタンス用の仮想 IP リソースと正常性プローブを作成します

    ```bash
    sudo pcs node unstandby sap-cl2
    sudo pcs node standby sap-cl1
    
    sudo pcs resource create fs_NW1_AERS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ers' \
      directory='/usr/sap/NW1/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
      op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW1_AERS
   
    sudo pcs resource create vip_NW1_AERS IPaddr2 \
      ip=10.90.90.9 cidr_netmask=24 \
     --group g-NW1_AERS
   
    sudo pcs resource create nc_NW1_AERS azure-lb port=62101 \
     --group g-NW1_AERS
    ```
 
   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

    ```bash
    sudo pcs status
   
    # Node sap-cl1: standby
    # Online: [ sap-cl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #  Resource Group: g-NW1_AERS
    #      fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    ```

1. **[2]** SAP NetWeaver ERS をインストールします  

   root として SAP NetWeaver ERS を 2 番目のノードにインストールします。その際、ERS のロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名 (たとえば、**sapers**、**10.90.90.9**) と、ロード バランサーのプローブに使用したインスタンス番号 (たとえば、**01**) を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

    ```bash
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
    sudo firewall-cmd --zone=public  --add-port=4237/tcp

    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

   インストールで /usr/sap/**NW1**/ERS **01** へのサブフォルダーの作成に失敗する場合は、ERS **01** フォルダーの所有者とグループを設定し、もう一度試してください。

    ```
    sudo chown qaadm /usr/sap/NW1/ERS01
    sudo chgrp sapsys /usr/sap/NW1/ERS01
    ```

1. **[1]** ASCS/SCS および ERS インスタンス プロファイルを適用します

   * ASCS/SCS プロファイル

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ASCS00_sapascs
   
    # Change the restart command to a start command
    #Restart_Program_01 = local $(_EN) pf=$(_PF)
    Start_Program_01 = local $(_EN) pf=$(_PF)
   
    # Add the keep alive parameter, if using ENSA1
    enque/encni/set_so_keepalive = true
    ```

   ENSA1 と ENSA2 の両方について、`keepalive` OS パラメーターが SAP ノート [1410736](https://launchpad.support.sap.com/#/notes/1410736) の説明に従って設定されていることを確認します。  

   * ERS プロファイル

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ERS01_sapers
   
    # Change the restart command to a start command
    #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
    Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
    # remove Autostart from ERS profile
    # Autostart = 1
    ```

1. **[A]** キープ アライブを構成します

   SAP NetWeaver アプリケーション サーバーと ASCS/SCS の間の通信は、ソフトウェア ロード バランサーを介してルーティングされます。 ロード バランサーは、構成可能なタイムアウト後に非アクティブな接続を切断します。 これを回避するには、SAP NetWeaver ASCS/SCS プロファイルにパラメーターを設定する必要があります (ENSA1 を使用する場合)。 ENSA1 と ENSA2 の両方について、すべての SAP サーバーの Linux システムの `keepalive` 設定を変更します。 詳細については、[SAP Note 1410736][1410736] を参照してください。

    ```bash
    # Change the Linux system configuration
    sudo sysctl net.ipv4.tcp_keepalive_time=300
    ```

1. **[A]** /usr/sap/sapservices ファイルを更新します

   Sapinit スタートアップ スクリプトによってインスタンスが開始されるのを防ぐために、Pacemaker によって管理されているすべてのインスタンスを /usr/sap/sapservices ファイルからコメント アウトする必要があります。 SAP HANA インスタンスが HANA SR と一緒に使用されている場合は、SAP HANA インスタンスをコメント アウトしないでください。

    ```bash
    sudo vi /usr/sap/sapservices
   
    # On the node where you installed the ASCS, comment out the following line
    # LD_LIBRARY_PATH=/usr/sap/NW1/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW1/ASCS00/exe/sapstartsrv pf=/usr/sap/NW1/SYS/profile/NW1_ASCS00_sapascs -D -u nw1adm
   
    # On the node where you installed the ERS, comment out the following line
    # LD_LIBRARY_PATH=/usr/sap/NW1/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW1/ERS01/exe/sapstartsrv pf=/usr/sap/NW1/ERS01/profile/NW1_ERS01_sapers -D -u nw1adm
    ```

1. **[1]** SAP クラスター リソースを作成します

   エンキュー サーバー 1 のアーキテクチャ (ENSA1) を使用する場合は、次のようにリソースを定義します。

    ```bash
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_NW1_ASCS00 SAPInstance \
     InstanceName=NW1_ASCS00_sapascs START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_sapascs" \
     AUTOMATIC_RECOVER=false \
     meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
     op monitor interval=20 on-fail=restart timeout=60 \
     op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW1_ASCS
   
    sudo pcs resource meta g-NW1_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW1_ERS01 SAPInstance \
     InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" \
     AUTOMATIC_RECOVER=false IS_ERS=true \
     op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW1_AERS
     
    sudo pcs constraint colocation add g-NW1_AERS with g-NW1_ASCS -5000
    sudo pcs constraint location rsc_sap_NW1_ASCS00 rule score=2000 runs_ers_NW1 eq 1
    sudo pcs constraint order start g-NW1_ASCS then stop g-NW1_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby sap-cl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP では、SAP NW 7.52 の時点で、レプリケーションを含むエンキュー サーバー 2 のサポートが導入されました。 ABAP Platform 1809 以降では、エンキュー サーバー 2 が既定でインストールされます。 エンキュー サーバー 2 のサポートについては、SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) を参照してください。
   エンキュー サーバー 2 アーキテクチャ ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) を使用する場合は、resource-agents-sap-4.1.1-12.el7.x86_64 以降のリソース エージェントをインストールし、リソースを次のように定義します。

    ```bash
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_NW1_ASCS00 SAPInstance \
    InstanceName=NW1_ASCS00_anftstsapvh START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-NW1_ASCS
   
    sudo pcs resource meta g-NW1_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW1_ERS01 SAPInstance \
    InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-NW1_AERS
      
    sudo pcs resource meta rsc_sap_NW1_ERS01  resource-stickiness=3000

    sudo pcs constraint colocation add g-NW1_AERS with g-NW1_ASCS -5000
    sudo pcs constraint order start g-NW1_ASCS then start g-NW1_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW1_ASCS then stop g-NW1_AERS kind=Optional symmetrical=false
   
    sudo pcs node unstandby sap-cl1
    sudo pcs property set maintenance-mode=false
    ```

   以前のバージョンからアップグレードし、エンキュー サーバー 2 に切り替えている場合は、SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322) を参照してください。 

   > [!NOTE]
   > 上記のタイムアウト構成はほんの一例であり、特定の SAP 設定では調整する必要がある場合があります。 

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

    ```bash
    sudo pcs status
    
    # Online: [ sap-cl1 sap-cl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started sap-cl2
    #  Resource Group: g-NW1_AERS
    #      fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started sap-cl1
    #      nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    #      rsc_sap_NW1_ERS01  (ocf::heartbeat:SAPInstance):   Started sap-cl1
   ```

1. **[A]** ASCS および ERS に対するファイアウォール規則を両方のノード上に追加します ASCS および ERS に対するファイアウォール規則を両方のノード上に追加します。

    ```bash
    # Probe Port of ASCS
    sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62000/tcp
    sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3200/tcp
    sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3600/tcp
    sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3900/tcp
    sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8100/tcp
    sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50013/tcp
    sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50014/tcp
    sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50016/tcp
    # Probe Port of ERS
    sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62101/tcp
    sudo firewall-cmd --zone=public --add-port=3201/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3201/tcp
    sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3301/tcp
    sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50113/tcp
    sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50114/tcp
    sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50116/tcp
    ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver アプリケーション サーバーの準備

   一部のデータベースでは、データベース インスタンスのインストールがアプリケーション サーバーで実行される必要があります。 このような場合に使用できるようにアプリケーション サーバー仮想マシンを準備します。  

   下の手順では、ASCS/SCS および HANA サーバーとは別のサーバーにアプリケーション サーバーをインストールすることを前提としています。 それ以外の場合、以下の手順の一部 (ホスト名解決の構成など) は必要ありません。  

   次の各手順の先頭には、 **[A]** - PAS と AAS の両方が該当、 **[P]** - PAS のみ該当、 **[S]** - ノード AAS のみ該当、のいずれかが付いています。  

1. **[A]** ホスト名解決を設定します DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます。  

    ```bash
    sudo vi /etc/hosts
    ```

   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します。

    ```bash
    10.90.90.7    sap-cl1
    10.90.90.8    sap-cl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    10.90.90.10   sapascs
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    10.90.90.9    sapers
    10.90.90.12   sapa01
    10.90.90.13   sapa02
    ```

1. **[A]** sapmnt ディレクトリを作成します
   
    ```bash
    sudo mkdir -p /sapmnt/NW1
    sudo mkdir -p /usr/sap/trans

    sudo chattr +i /sapmnt/NW1
    sudo chattr +i /usr/sap/trans
   ```

1. **[A]** NFS クライアントとその他の要件をインストールします  

    ```bash
    sudo yum -y install nfs-utils uuidd
    ```

1. **[A]** マウント エントリを追加します  

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```   

1. **[A]** スワップ ファイルを構成します
 
    ```bash
    sudo vi /etc/waagent.conf
   
    # Set the property ResourceDisk.EnableSwap to y
    # Create and use swapfile on resource disk.
    ResourceDisk.EnableSwap=y
   
    # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
    # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
    # Size of the swapfile.
    ResourceDisk.SwapSizeMB=2000
    ```

   エージェントを再起動して変更をアクティブにします

    ```bash
    sudo service waagent restart
    ```

## <a name="install-database"></a>データベースのインストール

この例では、SAP HANA に SAP NetWeaver がインストールされます。 このインストールではサポートされているすべてのデータベースを使用できます。 Azure への SAP HANA のインストール方法の詳細については、「[High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533]」 (Red Hat Enterprise Linux 上の Azure VM での SAP HANA の高可用性) をご覧ください。

root として SAP NetWeaver データベース インスタンスをインストールします。その際、データベースのロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名を使用します。

sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   ```bash
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver アプリケーション サーバーのインストール

次の手順に従って、SAP アプリケーション サーバーをインストールします。

1. **[A]** アプリケーション サーバーを準備します

   前述の「[SAP NetWeaver アプリケーション サーバーの準備](high-availability-guide-rhel-nfs-azure-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)」の章の手順に従って、アプリケーション サーバーを準備します。

1. **[A]** SAP NetWeaver アプリケーション サーバーをインストールします。  

   プライマリまたは追加の SAP NetWeaver アプリケーション サーバーをインストールします。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

    ```bash
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
    ```

1. **[A]** SAP HANA Secure Store を更新します

   SAP HANA システム レプリケーション セットアップの仮想名を指すように SAP HANA Secure Store を更新します。

   次のコマンドを実行して、エントリを `<sapsid>adm` として一覧表示します

    ```bash
    hdbuserstore List
    ```

   これにより、次のようにすべてのエントリが一覧表示されます
    ```bash
    DATA FILE       : /home/nw1adm/.hdb/sapa01/SSFS_HDB.DAT
    KEY FILE        : /home/nw1adm/.hdb/sapa01/SSFS_HDB.KEY
   
    KEY DEFAULT
      ENV : 10.90.90.5:30313
      USER: SAPABAP1
      DATABASE: NW1
    ```

   この例では、既定のエントリの IP アドレスは、ロードバランサーではなく VM を指しています。 ロード バランサーの仮想ホスト名を指すようにエントリを変更します。 必ず同じポートとデータベース名を使用してください。 たとえば、サンプル出力の `30313` と `NW1` です。


    ```bash
    su - nw1adm
    hdbuserstore SET DEFAULT nw1db:30313@NW1 SAPABAP1 <password of ABAP schema>
    ```

## <a name="test-cluster-setup"></a>クラスターのセットアップのテスト

Pacemaker クラスターを十分にテストします。 [一般的なフェールオーバー テストを実行](./high-availability-guide-rhel.md#test-the-cluster-setup)します。

## <a name="next-steps"></a>次のステップ

* [RHEL for SAP アプリケーション マルチ SID 上の Azure VM での SAP NW の HA ガイド](./high-availability-guide-rhel-multi-sid.md)
* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。