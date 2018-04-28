---
title: 1 つの Azure リージョン内での SAP HANA の可用性 | Microsoft Docs
description: 1 つの Azure リージョンにある Azure ネイティブ VM 上での SAP HANA の運用について説明します。
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
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47dba73a6c22d11953485a69435000d3d2fe6f55
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>1 つの Azure リージョン内での SAP HANA の可用性
この記事では、1 つの Azure リージョン内での複数の可用性シナリオについて説明します。 Azure には多くのリージョンがあり、世界中に分散しています。 Azure リージョンの一覧については、「[Azure リージョン](https://azure.microsoft.com/regions/)」をご覧ください。 1 つの Azure リージョン内の VM に SAP HANA をデプロイする場合は、Microsoft から単一の VM と HANA インスタンスのデプロイが提供されています。 可用性を高める場合は、可用性のために HANA システム レプリケーションを使う 2 つの VM と 2 つの HANA インスタンスを [Azure 可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)内にデプロイできます。 

現在、Azure では、[Azure 可用性ゾーン (プレビュー)](https://docs.microsoft.com/azure/availability-zones/az-overview) のパブリック プレビューを提供しています。 この記事では、可用性ゾーンの詳細については説明しません。 ただし、可用性セットと可用性ゾーンの使用に関する一般的な議論については取り上げています。

Azure の可用性セットと可用性ゾーンの相違点は何でしょうか。 可用性ゾーンが提供される Azure リージョンには複数のデータ センターがあります。 データセンターは、電源、冷却装置、およびネットワークの供給からは独立しています。 1 つの Azure リージョン内で異なるゾーンを提供する理由は、提供されている 2 つまたは 3 つの可用性ゾーンにまたがってアプリケーションをデプロイできるためです。 電源やネットワークの問題により 1 つの可用性ゾーンのインフラストラクチャだけが影響を受けた場合でも、可用性ゾーンを使用していれば、Azure リージョン内のアプリケーションのデプロイは、引き続き完全に機能します。 容量は一部、減少する可能性があります。 たとえば、1 つのゾーン内の VM は失われる可能性がありますが、他の 2 つのゾーン内の VM は引き続き稼働して実行されます。 
 
Azure 可用性セットは、論理グループ作成機能であり、この機能によって、可用性セット内に配置された VM リソースは、Azure データセンター内にデプロイされるときに、確実に互いに障害分離されます。 Azure では、可用性セット内に配置された VM は、複数の物理サーバー、コンピューティング ラック、ストレージ ユニット、およびネットワーク スイッチ間で実行されます。 一部の Azure ドキュメントでは、この構成を異なる[更新ドメインと障害ドメイン](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)への配置と呼んでいます。 通常は、1 つの Azure データ センター内に配置されます。 アプリケーションがデプロイされているデータ センターが電源やネットワークの問題の影響を受けた場合、1 つの Azure リージョン内のすべての容量が影響を受けます。

Azure 可用性ゾーンを構成するデータ センターをどのように配置するかは、ほとんどのアプリケーションが許容できる異なるゾーンにデプロイされたサービス間のネットワーク待機時間と、データ センター間の距離を比較検討して決めます。 理想は、自然災害によって、1 つのリージョン内のすべての可用性ゾーンの電源、ネットワーク、インフラストラクチャが影響を受けないことです。 ただし、非常に大きな自然災害が発生した場合、可用性ゾーンは、1 つのリージョン内で必要とする可用性を必ずしも提供できない可能性があります。 2017 年 9 月 20 日にプエルトリコ島に上陸したハリケーン Maria を考えてみてください。 ハリケーンによって、幅 90 マイルの島でほぼ 100% の停電が発生しました。

## <a name="single-vm-scenario"></a>単一の VM のシナリオ

単一の VM のシナリオでは、SAP HANA インスタンス用に Azure VM を作成します。 Azure Premium Storage を使って、オペレーティング システム ディスクとすべてのデータ ディスクをホストします。 Azure の稼働時間 99.9% の SLA と他の Azure コンポーネントの SLA は、顧客の可用性 SLA を満たすのに十分です。 このシナリオでは、DBMS レイヤーを実行する VM 用に Azure 可用性セットを利用する必要はありません。 このシナリオでは、次の 2 つの異なる機能を使用します。

- Azure VM 自動再起動 (Azure サービス復旧と呼ぶこともあります)
- SAP HANA 自動再起動

Azure VM 自動再起動または "サービス復旧" は、次の 2 つのレベルで動作する Azure の機能です。

- Azure サーバー ホストが、サーバー ホストでホストされている VM の正常性を確認する。
- Azure ファブリック コントローラーが、サーバー ホストの正常性と可用性を監視する。

正常性チェック機能が、Azure サーバー ホストでホストされているすべての VM の正常性を監視します。 VM で異常が発生すると、VM の正常性をチェックする Azure ホスト エージェントが VM の再起動を開始できます。 ファブリック コントローラーは、ホスト ハードウェアの問題を示すことができる多数のさまざまなパラメーターをチェックして、ホストの正常性を確認します。 また、ネットワーク経由でホストのアクセシビリティも確認します。 ホストに問題があると、次のようなイベントが発生する可能性があります。

- ホストが異常を示した場合、ホストの再起動およびホスト上で実行されている VM の再起動。
- 再起動後にホストが異常な場合、ホストの再起動と、正常なホストでもともとホストされていた VM の再起動。 この場合、ホストは異常とマークされます。 クリアされるか交換されるまで、それ以上デプロイに使われることはありません。
- 異常なホストの再起動プロセスに問題がある場合、正常なホストでの VM の即時再起動。 

Azure によって提供されるホストと VM の監視により、ホストの問題が発生している Azure VM は正常な Azure ホストで自動的に再起動します。 

このようなシナリオで使用する 2 番目の機能では、再起動された VM で実行される HANA サービスが、VM の再起動後に自動的に開始されます。 [HANA サービスの自動再起動](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)は、さまざまな HANA サービスのウォッチドッグ サービス経由で設定できます。

この単一 VM のシナリオは、SAP HANA 構成にコールド フェールオーバー ノードを追加することで、改善できます。 SAP HANA のドキュメントでは、この設定は[ホスト自動フェールオーバー](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html)と呼ばれています。 この構成は、サーバー ハードウェアが制限され、一連の運用ホストに対するホスト自動フェールオーバー ノードとして 1 つのサーバー ノードを専用に使う、オンプレミス デプロイの場合に有効です。 ただし、Azure の基盤インフラストラクチャが VM の正常な再起動用に正常な対象サーバーを提供する場合には、Azure では、SAP HANA ホスト自動フェールオーバーのデプロイは有効ではありません。 このため、HANA ホスト自動フェールオーバーのスタンバイ ノードを予測する参照アーキテクチャは用意されていません。 これは、SAP HANA スケールアウト構成にも該当します。

## <a name="availability-scenarios-for-two-different-vms"></a>2 つの異なる VM の可用性シナリオ

Azure 可用性セット内の 2 つの Azure VM を使うと、VM が 1 つの Azure リージョン内の Azure 可用性セットに配置されている場合、これらの 2 つの VM の間で稼働時間を増やすことができます。 Azure の基本設定は、次のようになります。

![すべてのレイヤーを備えた 2 つの VM の図](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

異なる可用性シナリオを示すため、図のレイヤーのいくつかは省略されています。 図では VM、ホスト、可用性セット、および Azure リージョンを表現しているレイヤーのみを示しています。 Azure Virtual Network インスタンス、リソース グループ、およびサブスクリプションは、このセクションで説明されるシナリオでは、役割を担いません。

### <a name="replicate-backups-to-a-second-virtual-machine"></a>第 2 の仮想マシンへのバックアップのレプリケート

最も基本的な設定の 1 つは、バックアップの使用です。 特に、1 つの VM から別の Azure VM へ配信されるトランザクション ログのバックアップを保持することができます。 Azure Storage タイプは、選択可能です。 この設定では、第 1 の VM で行われた第 2 の VM に対するスケジュール バックアップのコピーのスクリプト作成を行う必要があります。 第 2 の VM インスタンスを使用する必要がある場合は、必ず完全、増分/差分、およびトランザクション ログのバックアップを必要な時点に復元します。 

アーキテクチャは次のようになります。

![ストレージ レプリケーションを示した 2 つの VM の図](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

この設定は、優れた RPO (Recovery Point Objective) および RTO (Recovery Time Objective) 時間の実現には、あまり適していません。 特に RTO 時間は、コピーされたバックアップを使用して、データベース全体を完全に復元する必要があるため、困難が生じます。 ただし、このセットアップは、メイン インスタンスでの意図しないデータ削除からの復旧には役立ちます。 この設定では、特定時点への復元、データ抽出、メイン インスタンスへの削除されたデータのインポートが、いつでも可能です。 そのため、バックアップ コピーの手法を他の高可用性機能と組み合わせて使用すると、有効な場合があります。 

バックアップをコピーするときは、SAP HANA インスタンスが実行されているメインの VM よりも、小さい VM を使用することもできます。 VM が小さいと、接続できる VHD の数は少なくなることに留意してください。 個別の VM タイプの制限については、「[Azure の Linux 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)」をご覧ください。

### <a name="sap-hana-system-replication-without-automatic-failover"></a>自動フェールオーバーを伴わない SAP HANA システム レプリケーション

このセクションで説明したシナリオでは、SAP HANA システム レプリケーションを使用します。 SAP ドキュメントについては、「[System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)」(システム レプリケーション) をご覧ください。 単一の Azure リージョン内の 2 つの Azure VM は構成が異なるため、RTO にいくつかの違いがあります。 一般に、自動フェールオーバーのないシナリオは、1 つの Azure リージョン内の VM には特に該当しない可能性があります。 これは、Azure インフラストラクチャのほとんどのエラーでは、Azure サービスの復旧によって、別のホスト上のプライマリ VM が再起動されるためです。 そのような構成が障害シナリオで役立つのは、一部の特別なケースだけです。 または、顧客が効率の向上を実現したいと考えているケースもあります。

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>自動フェールオーバーとデータの事前読み込みを伴わない SAP HANA システム レプリケーション

このシナリオでは、同期的な方法でデータを移動して RPO の 0 を達成するために、SAP HANA システム レプリケーションを使用します。 一方で、RTO は十分に長いので、フェールオーバーや HANA インスタンス キャッシュへのデータの事前読み込みは必要ありません。 この場合は、以下の方法によって、構成の経済性を向上させることが可能です。

- 第 2 の VM で別の SAP HANA インスタンスを実行します。 第 2 の VM の SAP HANA インスタンスは、仮想マシンのメモリのほとんどを使用します。 通常、これは、第 2 の VM へのフェールオーバーが発生するケースです。 レプリケートされたデータを第 2 の VM の対象 HANA インスタンスのキャッシュに読み込むことができるように、第 2 の VM をシャットダウンできます。
- 第 2 の VM 上でより小さい VM サイズを使用できます。 フェールオーバーが発生した場合、手動フェールオーバーの前に追加の手順を行います。 この手順では、VM サイズをソース VM のサイズに変更します。 シナリオは次のようになります。

![ストレージ レプリケーションを示した 2 つの VM の図](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> HANA システム レプリケーション ターゲットでデータの事前読み込みを使用しない場合でも、少なくとも 64 GB のメモリが必要です。 64 GB に加えて、ターゲット インスタンスのメモリに行ストア データを保持するための十分なメモリも必要です。

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>自動フェールオーバーがなく、データの事前読み込みを伴う SAP HANA システム レプリケーション

このシナリオでは、第 2 の VM の HANA インスタンスにレプリケートされるデータが事前に読み込まれます。 これにより、データの事前読み込みがない場合の 2 つの利点が失われます。 この場合、第 2 の VM で別の SAP HANA システムを実行することはできません。 また、小さい VM サイズを使うこともできません。 そのため、顧客がこのシナリオを実装することは、ほとんどありません。

### <a name="sap-hana-system-replication-with-automatic-failover"></a>自動フェールオーバーを伴う SAP HANA システム レプリケーション

1 つの Azure リージョン内の標準的かつ最も一般的な可用性の構成では、SLES Linux を実行する 2 つの Azure VM でフェールオーバー クラスターが定義されています。 SLES Linux クラスターは、[Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) フレームワークと [STONITH](http://linux-ha.org/wiki/STONITH) デバイスの組み合わせが基になっています。 

SAP HANA の観点から、使用されるレプリケーション モードは同期され、自動フェールオーバーが構成されます。 第 2 の VM では、SAP HANA インスタンスはホット スタンバイ ノードとして機能します。 スタンバイ ノードは、プライマリ SAP HANA インスタンスから変更レコードの同期ストリームを受け取ります。 HANA プライマリ ノードでアプリケーションによってトランザクションがコミットされると、プライマリ HANA ノードは、セカンダリ SAP HANA ノードがコミット レコードを受信したことを確認するまで、アプリケーションへのコミットの確認を待機します。 SAP HANA では 2 つの同期レプリケーション モードを提供しています。 これら 2 つの同期レプリケーション モード間の違いに関する詳細および説明については、SAP の記事「[Replication modes for SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)」(SAP HANA システム レプリケーションのレプリケーション モード) をご覧ください。

全体の構成は次のようになります。

![ストレージ レプリケーションとフェールオーバーを伴う 2 つの VM の図](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

RPO = 0 と非常に短い RTO を実現できるため、このソリューションが選択される場合があります。 SAP HANA クライアントが仮想 IP アドレスを使って HANA システム レプリケーション構成に接続するように、SAP HANA クライアント接続を構成します。 これにより、セカンダリ ノードへのフェールオーバーが発生した場合にアプリケーションを再構成する必要がなくなります。 このシナリオでは、プライマリおよびセカンダリ VM の Azure VM SKU を同じにする必要があります。

## <a name="next-steps"></a>次の手順

Azure でのこれらの構成の設定手順については、以下をご覧ください。

- [Azure VM での SAP HANA システム レプリケーションのセットアップ](sap-hana-high-availability.md)
- [システム レプリケーションを使用する SAP HANA の高可用性](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

複数の Azure リージョン間での SAP HANA の可用性の詳細については、以下をご覧ください。

- [Azure リージョンの枠を越えた SAP HANA の可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

