---
title: 1 つの Azure リージョン内での SAP HANA の可用性 | Microsoft Docs
description: SAP HANA on Azure ネイティブ VM の運用
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/5/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28fe9bc7c8cb1d59df404b7dd7429def54648a18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>1 つの Azure リージョン内での SAP HANA の可用性
このセクションでは、1 つの Azure リージョン内での可用性について説明する複数のシナリオを示します。 Azure には多くのリージョンがあり、世界中に分散しています。 Azure リージョンの一覧については、「[Azure リージョン](https://azure.microsoft.com/regions/)」をご覧ください。 1 つの Azure リージョン内の VM に SAP HANA をデプロイする場合は、Microsoft から単一の VM と HANA インスタンスのデプロイが提供されています。 または、可用性を高める場合は、可用性のために HANA システム レプリケーションを使う 2 つの VM と 2 つの HANA インスタンスを [Azure 可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)内にデプロイできます。 Azure には、[Azure 可用性ゾーン](https://docs.microsoft.com/azure/availability-zones/az-overview)のパブリック プレビューがあります。 可用性ゾーンについては、まだ詳しく説明しません。 可用性セットと可用性ゾーンの使用の比較についての概要は説明します。

Azure の可用性セットと可用性ゾーンの相違点:  可用性ゾーンが提供される Azure リージョンには複数のデータ センターがあり、電源、冷却装置、ネットワークが独立しています。 1 つの Azure リージョン内で異なるゾーンを提供する理由は、2 つまたは 3 つの可用性ゾーンにまたがってアプリケーションをデプロイできるようにするためです。 電源やネットワークの問題により 1 つの可用性ゾーンのインフラストラクチャだけが影響を受けた場合でも、Azure リージョン内のアプリケーションのデプロイは完全に機能します。 最終的には、1 つのゾーンの VM がいくつか失われて、容量が若干減る可能性があります。 ただし、他の 2 つのゾーンの VM はまだ稼働しています。 
 
これに対し、Azure 可用性セットは、Azure で使用できる論理グループ作成機能であり、グループに配置された VM リソースは、Azure データ センター内にデプロイされるときに互いに障害が分離されます。 Azure では、可用性セット内に配置された VM は、複数の物理サーバー、コンピューティング ラック、ストレージ ユニット、およびネットワーク スイッチ間で実行されます。 他の Azure ドキュメントの中には、これを異なる[更新ドメインと障害ドメイン](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)への配置と呼んでいるものもあります。 通常は、1 つの Azure データ センター内に配置されます。 アプリケーションがデプロイされているデータ センターが電源やネットワークの問題の影響を受けた場合、1 つの Azure リージョン内のすべての容量が影響を受けます。

Azure 可用性ゾーンを構成するデータ センターをどのように配置するかは、ほとんどのアプリケーションが許容できる異なるゾーンにデプロイされたサービス間のネットワーク待機時間と、データ センター間の距離を比較検討して決めます。 通常、自然災害によって、1 つのリージョン内のすべての可用性ゾーンの電源、ネットワーク、インフラストラクチャが影響を受けることはありません。 ただし、非常に大きな自然災害では、1 つのリージョン内の可用性ゾーンでは目標とする可用性を提供できない可能性があります。 2017 年 8 月 20 日のハリケーン Maria で幅 90 マイルの島であるプエルトリコがほぼ 100% 停電したような場合が考えられます。   
  


## <a name="single-vm-scenario"></a>単一の VM のシナリオ
このシナリオでは、SAP HANA インスタンス用に Azure 仮想マシンを作成します。 Azure Premium Storage を使って、オペレーティング システム ディスクとすべてのデータ ディスクをホストします。 Azure の 99.9% の SLA と他の Azure コンポーネントの SLA は、顧客に対する可用性 SLA を満たすのに十分です。 このシナリオでは、DBMS レイヤーを実行する VM 用に Azure 可用性セットを利用する必要はありません。 このシナリオでは、2 つの異なる機能を使用します。

- Azure VM 自動再起動 (Azure サービス復旧と呼ぶこともあります)
- SAP HANA 自動再起動

Azure VM 自動再起動または "サービス復旧" は、2 つのレベルで動作する Azure の機能です。

- サーバー ホストでホストされている VM の正常性を確認する Azure サーバー ホスト
- サーバー ホストの正常性と可用性を監視する Azure ファブリック コントローラー

Azure サーバー ホストでホストされているすべての VM について、正常性チェック機能がホストされている VM の正常性を監視します。 VM で異常が発生すると、VM の正常性をチェックする Azure ホスト エージェントが VM の再起動を開始できます。 Azure ファブリック コントローラーは、ホスト ハードウェアの問題を示す多くのパラメーターをチェックしてホストの正常性を確認しますが、ネットワーク経由でホストのアクセス可能性も確認します。 ホストに問題があると次のようなアクションが実行されます。

- ホストが異常を示した場合、ホストの再起動およびホスト上で実行している VM の再起動
- 再起動後にホストが異常な場合、ホストの再起動と、正常なホストでもともとホストされていた VM の再起動。 この場合、ホストは異常とマークされ、正常化されるか交換されるまではそれ以上デプロイに使われません。
- 異常なホストの再起動プロセスに問題がある場合、正常なホストでの VM の即時再起動。 

Azure によって提供されるホストと VM の監視により、ホストの問題の影響を受けている Azure VM は正常な Azure ホストで自動的に再起動します 

このようなシナリオで使用する 2 番目の機能は、再起動された VM で実行されている HANA サービスが VM の再起動後に自動的に開始するということです。 [HANA サービスの自動再起動](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)は、異なるHANA サービスのウォッチドッグ サービスで構成できます。

この単一 VM シナリオは、SAP HANA 構成にコールド フェールオーバー ノードを追加すると改善できます。 SAP HANA のドキュメントでは[ホスト自動フェールオーバー](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html)とも呼ばれています。この構成は、サーバー ハードウェアが制限され、一連の運用ホストに対するホスト自動フェールオーバー ノードとして 1 つのサーバー ノードを専用に使うオンプレミスの展開状況で有効です。 ただし、Azure の基盤インフラストラクチャが VM の正常な再起動用に正常な対象サーバーを提供するような状況では、SAP HANA ホスト自動フェールオーバー シナリオはデプロイする意味がありません。 

そのため、HANA ホスト自動フェールオーバーのスタンバイ ノードを予測する参照アーキテクチャはありません。 これは、SAP HANA スケールアウト構成にも適用されます。


## <a name="availability-scenarios-involving-two-different-vms"></a>2 つの異なる VM での可用性シナリオ
Azure 可用性セット内の 2 つの Azure VM を使うと、これらの VM が 1 つの Azure リージョン内の Azure 可用性セットに配置されている場合、2 つの VM の間で稼働時間を増やすことができます。 Azure の基本設定は次の図のようになります。![2 つの VM とすべてのレイヤー](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

異なる可用性シナリオを示すため、上のレイヤーのいくつかは切り取られており、この図では VM、ホスト、可用性セット、Azure リージョンのレイヤーだけに限定しています。 Azure VNet、リソース グループ、サブスクリプションは、説明したシナリオでは役割を果たしません。

### <a name="replicating-backups-to-second-virtual-machine"></a>2 番目の仮想マシンへのバックアップのレプリケーション
最も基本的な設定の 1 つはバックアップの作成です (特に、1 つの VM から別の Azure 仮想マシンに配布されるトランザクション ログ バックアップ)。 任意の Azure Storage タイプを選択できます。 ユーザーは第 1 の VM で行われたスケジュール バックアップを第 2 の VM にコピーするスクリプトを作成する必要があります。 第 2 の VM のインスタンスが必要な場合は、完全、増分/差分バックアップ、トランザクション ログ バックアップを必要な時点に復元する必要があります。 アーキテクチャは次のようになります。![2 つの VM とストレージ レプリケーション](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

この設定は、優れた RPO および RTO の実現には適していません。 特に RTO 時間はコピーされたバックアップで完全なデータベースを完全に復元する必要があるため損なわれてしまいます。 ただし、このセットアップは、メイン インスタンスでの意図しないデータ削除からの復旧には役に立ちます。 そのような設定では、特定時点への復元、データ抽出、メイン インスタンスへの削除されたデータのインポートが、いつでも可能です。 そのため、このようなバックアップ コピー方法を他の高可用性機能と組み合わせて使うと有効です。 バックアップだけをコピーするときは、メインの SAP HANA インスタンスより小さい VM で実行する方がうまくいく可能性があります。 ただし、VM が小さいほど接続できる VHD の数が少ないことに留意してください。 個別の VM タイプの制限については、「[Azure の Linux 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)」を確認してください。

### <a name="using-sap-hana-system-replication-without-automatic-failover"></a>自動フェールオーバーを伴わない SAP HANA システム レプリケーションの使用
次のシナリオでは、SAP HANA システム レプリケーションを使います。 最初に SAP のドキュメント「[System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)」(システム レプリケーション) をご覧ください。 単一の Azure リージョン内の 2 つの Azure VM の間には、目標復旧時間に違いがある 2 つの異なる構成があります。 一般に、自動フェールオーバーのないシナリオは、1 つの Azure リージョン内のシナリオにはあまり関係がありません。 Azure インフラストラクチャのほとんどのエラー状況では、Azure サービスの復旧は別のホスト上のプライマリ VM を再起動するためです。 そのような構成が障害シナリオで役に立つのは、いくつかの特別なケースだけです。 または、ユーザーが特に効率を実現したい一部のケースです。

#### <a name="using-hana-system-replication-without-auto-failover-and-without-data-pre-load"></a>自動フェールオーバーとデータ事前読み込みを伴わない HANA システム レプリケーションの使用 
これは、同期的にデータを移動して目標復旧時点 (RPO) を 0 にすることを目的に SAP HANA システム レプリケーションを使うシナリオです。 一方で、目標復旧時間 (RTO) は十分に長いので、フェールオーバーまたは HANA インスタンス キャッシュへのデータの事前読み込みは必要ありません。 このような場合は、以下の方法によって構成の経済性を向上できる可能性があります。

- 仮想マシンのメモリのほとんどを使用する別の SAP HANA インスタンスを 2 番目の VM で実行できます。 通常、このようなインスタンスは、第 2 の VM へのフェールオーバー時にシャットダウンするインスタンスです。 そのため、第 2 の VM の対象 HANA インスタンスのキャッシュにレプリケートされたデータを読み込むことができます。
- 第 2 の VM として小さい VM サイズを使用できます。 フェールオーバーの場合、ソース VM のサイズに VM のサイズを変更するステップが手動フェールオーバーの前にあります。 シナリオは次のようになります。

![ストレージ レプリケーションのある 2 つの VM](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> HANA システム レプリケーション ターゲットでデータの事前読み込みを行わない場合であっても、少なくとも 64 GB のメモリが必要であり、ターゲット インスタンスのメモリ内の行ストア データを保持するにはさらに多くのメモリが必要です。

#### <a name="using-hana-system-replication-without-auto-failover-and-with-data-pre-load"></a>自動フェールオーバーとデータ事前読み込みを伴う HANA システム レプリケーションの使用
前に説明したシナリオとの違いは、第 2 の VM の HANA インスタンスにレプリケートされるデータが事前に読み込まれることです。 これにより、データの事前読み込みがないシナリオでの 2 つの利点がなくなります。 この場合、第 2 の VM で別の SAP HANA システムを実行することはできません。 また、小さい VM サイズを使うこともできません。 そのため、これはほとんどの顧客が実装しないシナリオです。


### <a name="using-sap-hana-system-replication-with-automatic-failover"></a>自動フェールオーバーを伴う SAP HANA システム レプリケーションの使用

ほとんどのユーザーが SAP HANA で実装する、単一 Azure リージョン内の標準的な可用性の構成は、SLES Linux を実行する 2 つの Azure 仮想マシンでフェールオーバー クラスターを定義する構成です。 SLES の Linux クラスターは、[Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) フレームワークと [STONITH](http://linux-ha.org/wiki/STONITH) デバイスの組み合わせが基になっています。 SAP HANA の観点からは、使われるレプリケーション モードは同期であり、自動フェールオーバーが構成されます。 第 2 の VM では、SAP HANA インスタンスはホット スタンバイ ノードとして機能し、プライマリ SAP HANA インスタンスから変更レコードの同期ストリームを受け取ります。 HANA プライマリ ノードでアプリケーションによってトランザクションがコミットされると、プライマリ HANA ノードは、セカンダリ SAP HANA ノードがコミット レコードの受信を確認するまで、アプリケーションへのコミットの確認を待機します。 SAP HANA には 2 つの異なる同期レプリケーション モードがあります。 詳細およびこれら 2 つの同期レプリケーション モードの違いについては、「[Replication modes for SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)」(SAP HANA システム レプリケーションのレプリケーション モード) をご覧ください

全体的な構成は次のようになります。

![ストレージ レプリケーションとフェールオーバーのある 2 つの VM](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

このソリューションを選択する理由は、RPO = 0 と非常に短い RTO 時間を実現できることです。 SAP HANA クライアントが仮想 IP アドレスを使って HANA システム レプリケーション構成に接続するように、SAP HANA クライアント接続を構成します。 これにより、セカンダリ ノードへのフェールオーバーが発生した場合にアプリケーションを再構成する必要がなくなります。 このソリューションでは、プライマリとセカンダリの Azure VM SKU は同じである必要があります。  


## <a name="next-steps"></a>次の手順
Azure でこのような構成を設定する方法の詳細な手順については、以下の記事をご覧ください。

- [Azure VM での SAP HANA システム レプリケーションのセットアップ](sap-hana-high-availability.md)
- [SAP on Azure – パート 4 – システム レプリケーションを使用する SAP HANA の高可用性](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Azure リージョン間での SAP HANA の可用性について詳しくは、以下をご覧ください。

- [Azure リージョンの枠を越えた SAP HANA の可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

