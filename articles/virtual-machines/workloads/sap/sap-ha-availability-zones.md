---
title: Azure Availability Zones での SAP ワークロードの構成 | Microsoft Docs
description: Azure Availability Zones を使用した SAP NetWeaver のための高可用性のアーキテクチャとシナリオ
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 409a304296d3fdff897a203177e2c150162755c6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746219"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Azure Availability Zones での SAP ワークロードの構成

Azure での SAP ワークロードの全体的な可用性を向上させるために Azure で提供されている高可用性機能の 1 つは、[Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) です。 Availability Zones は既にいくつかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)で利用可能になっています。 今後、さらに多くのリージョンで利用できるようになります。 

SAP の高可用性の基本的なアーキテクチャの説明を次の図に示します。

![標準的な HA の構成](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP のアプリケーション レイヤーは、1 つの Azure [可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)にデプロイされます。 SAP セントラル サービスを高可用性にするには、異なる可用性セットに 2 つの VM をデプロイし、Windows フェールオーバー クラスター サービスまたは Pacemaker (Linux) を使用して、インフラストラクチャまたはソフトウェアの問題が発生すると自動的にフェールオーバーする高可用性フレームワークをデプロイします。 このようなデプロイについて詳しくは、以下のドキュメントをご覧ください。

- [クラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [ファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

同様のアーキテクチャが、SAP NetWeaver、S/4HANA、または Hybris システムの DBMS レイヤーにも適用されます。 フェールオーバー クラスター ソリューションを使用して、アクティブ/パッシブ モードで DBMS レイヤーをデプロイします。このソリューションでは、DBMS 固有のフェールオーバー フレームワーク、Windows フェールオーバー クラスター サービス、または Pacemaker を使用して、インフラストラクチャやソフトウェアの障害が発生するとフェールオーバー アクティビティが開始されます。 

Azure Availability Zones を使用して同じアーキテクチャをデプロイするには、説明されているアーキテクチャに対していくつかの変更を行う必要があります。 これらの変更については、このドキュメントの別の部分で説明します。

## <a name="considerations-deploying-across-availability-zones"></a>Availability Zones へのデプロイに関する考慮事項

Availability Zones を使用する際には、考慮すべき点がいくつかあります。 考慮事項を次に示します。

- Azure Availability Zones は、1 つの Azure リージョン内の異なるゾーン間が一定の距離になるように保証するものではありません
- Azure Availability Zones は、理想的な DR ソリューションではありません。 大規模な自然災害のためにいくつかのリージョンで広範囲の被害が発生するような場合 (電源インフラストラクチャの大きな損傷など)、異なるゾーン間の距離が、適切な DR ソリューションとして認められるには十分ではない可能性があります
- 異なる Azure リージョン内の異なる Azure 可用性ゾーン間のネットワーク待ち時間は、Azure リージョンごとに異なります。 1 つのゾーンからアクティブな DBMS VM までのネットワーク待ち時間がビジネス プロセスへの影響から依然として許容できるようなケースでは、異なるゾーンをまたがってデプロイされた SAP アプリケーション レイヤーを実行できる場合があります。 しかし、Azure リージョンによっては、あるゾーン内のアクティブな DBMS VM と別のゾーンにある SAP アプリケーション インスタンスとの間の待ち時間が大きすぎて、SAP ビジネス プロセスにとって許容できない場合があります。 そのため、ゾーンをまたがる待ち時間が長すぎる場合は、デプロイ アーキテクチャをアプリケーションのアクティブ/アクティブ アーキテクチャまたはアクティブ/パッシブ アーキテクチャとは異なるものにする必要があります。
- Azure Availability Zones を使用できる構成を決定します。 異なるゾーン間で測定するネットワーク待ち時間に基づきます。 ネットワーク待ち時間は、2 つの点で重要です。
    - 同期レプリケーションを確立する必要がある 2 つの DBMS インスタンス間の待ち時間。 ネットワークの待ち時間が長いほど、ワークロードのスケーラビリティに影響する可能性が高くなります
    - アクティブな DBMS インスタンスと同じゾーンで SAP ダイアログ インスタンスを実行する VM と、別のゾーンの同様の VM の間の、ネットワーク待ち時間の違い。 この違いが大きいほど、DBMS と同じゾーン内または別のゾーン内のどちらで実行しているかに応じて、ビジネス プロセスとバッチ ジョブに対する実行時の影響が大きくなります


Azure の機能の使用に関しては、ゾーンをまたいで Azure VM をデプロイし、同じ Azure リージョン内の異なる可用性ゾーン間にフェールオーバー ソリューションを確立するときに使用できる機能に制限があります。 このような制限は次のとおりです。

- Azure 可用性ゾーンにデプロイするには、[Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)の使用が必須です 
- 物理ゾーンに対するゾーン列挙のマッピングは、Azure サブスクリプションごとに固定されています。 別のサブスクリプションを使用して SAP システムをデプロイする場合は、サブスクリプションごとに個別に最適なゾーンを定義する必要があります
- Azure 可用性ゾーン内に Azure 可用性セットをデプロイすることはできません。 仮想マシンのデプロイ フレームワークとして、Azure 可用性ゾーンまたは Azure 可用性セットのどちらかを選択します。
- [Azure Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) を使用して Windows フェールオーバー クラスター サービスまたは Linux Pacemaker に基づくフェールオーバー クラスター ソリューションを作成することはできません。 代わりに、[Azure Standard Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) を使用する必要があります



## <a name="ideal-zone-combination"></a>理想的なゾーンの組み合わせ
可用性ゾーンの利用方法を決定するには、次のことを調査する必要があります。

- Azure リージョンの 3 つの異なるゾーン間のネットワーク待ち時間。 クロス ゾーン ネットワーク トラフィックのネットワーク待ち時間が最も短いゾーンを選択できるようにするため
- 任意の 1 つのゾーン内の VM 間の待ち時間と、任意の 2 つのゾーン間のネットワーク待ち時間の差
- 任意の可用性ゾーン間にデプロイする必要がある VM の種類を、任意の 2 つのゾーンで使用可能かどうか。 特に M シリーズ仮想マシンでは、異なる M シリーズ VM SKU を 3 つのゾーンの 2 つでしか使用できない状況が発生します

### <a name="network-latency-between-zones-and-within-zone"></a>ゾーン間とゾーン内のネットワーク待ち時間
異なるゾーン間の待ち時間を調べるには、以下のことを行う必要があります。

- DBMS インスタンスに使用する VM SKU を 3 つのゾーンすべてにデプロイします。 この測定を実行するときは、[Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が有効になっていることを確認します
- ネットワーク待ち時間が最も短い 2 つのゾーンがわかったら、アプリケーション レイヤー VM として使用する VM SKU の別の 3 つの VM を 3 つの可用性ゾーンにデプロイします。 任意の 2 つの異なる "DBMS" ゾーン内の 2 つの "DBMS VM" に対して、ネットワーク待ち時間を測定します。 
- 測定ツールとしては、**niping** を使用します。 SAP 提供のこのツールの説明は、SAP サポート ノート [#500235](https://launchpad.support.sap.com/#/notes/500235) と [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) をご覧ください。 待ち時間測定用のコマンドに注目してください。 **ping** は Azure 高速ネットワーク コード パスでは動作しないので、**ping** の使用は推奨されません。

測定値と、異なるゾーンでの VM SKU の使用可能性に基づいて、決定する必要があります。

- DBMS レイヤーの理想的なゾーンを定義する
- ゾーン内またはゾーン間のネットワーク待ち時間の違いに基づいて、アクティブな SAP アプリケーション層を配置できるゾーンは 1 つか、2 つか、または 3 つ全部かを判断します
- アプリケーションの観点から、アクティブ/パッシブまたはアクティブ/アクティブのどちらの構成をデプロイするかを決定します (後で説明)

これらの決定を行うときは、SAP ノート [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) に記載されている SAP のネットワーク待ち時間に関する推奨事項も考慮します。

### <a name="be-aware-of"></a>注意事項

> [!IMPORTANT]
> 測定と決定は、測定に使用した Azure サブスクリプションに対して有効です。 サブスクリプションに依存するゾーンの列挙のマッピングはサブスクリプションによって異なる場合があるので、別の Azure サブスクリプションを使用するときは、再度測定を行う必要があります


> [!IMPORTANT]
> 上で実行した測定の結果は、[Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) をサポートするすべての Azure リージョンで異なことが予想されます。 ネットワーク待ち時間の要件が変わらなくても、ゾーン間のネットワーク待ち時間は異なる可能性があるため、異なる Azure リージョンでは異なるデプロイ戦略を採用することが必要な場合があります。 一部の Azure リージョンでは、異なる 3 つのゾーン間のネットワーク待ち時間が大きく異なることが想定されます。 他のリージョンでは、3 つの異なるゾーン間のネットワーク待ち時間の違いはもっと小さくなります。 **常に**ゾーン間のネットワーク待ち時間が 1 ～ 2 ミリ秒であることを求めるのは、**間違っています**。 Azure リージョン内の可用性ゾーン間のネットワーク待ち時間を一般化することはできません。


## <a name="activeactive-deployment"></a>アクティブ/アクティブのデプロイ
このデプロイ アーキテクチャがアクティブ/アクティブと呼ばれるのは、2 つまたは 3 つのゾーンにアクティブな SAP ダイアログ インスタンスをデプロイするためです。 エンキュー レプリケーションを使用する SAP セントラル サービスは、2 つのゾーン間にデプロイされます。 DBMS レイヤーについても同じで、SAP セントラル サービスと同じゾーンにデプロイされます。

そのような構成を検討するには、リージョン内で、ワークロードと同期 DBMS レプリケーションについて許容されるクロス ゾーン ネットワーク待ち時間を提供する 2 つの可用性ゾーンを見つける必要があります。 さらに、選択したゾーン内のネットワーク待ち時間とゾーン間のネットワーク待ち時間の差が、大きすぎないようにする必要があります。 後者の理由は、ジョブが DBMS サーバーと同じゾーンまたは異なるゾーンのどちらで実行しているかに依存するビジネス プロセスまたはバッチ プロセスの実行時間の変動が、大きくなりすぎないようにするためです。 ある程度の変動は許容されますが、相違の要因ではありません。

2 つのゾーン間のアクティブ/アクティブ デプロイの簡略化されたスキーマは次のようになります

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

この構成には、次の考慮事項が適用されます。

- 可用性セットは Azure 可用性ゾーン内にデプロイできないため、Azure 可用性ゾーンをすべての VM に対する障害ドメインおよび更新ドメインとして扱います
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターに使用する Azure ロード バランサーは、[Standard SKU のロード バランサー](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)にする必要があります。 Basic ロード バランサーは、ゾーンをまたいでは機能しません
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークとそのサブネットは、ゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける**必要はありません**
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用します。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません
- Azure Premium Storage または [Ultra SSD ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 その結果、重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 そのような共有ディスクまたは共有をゾーン間でレプリケートするテクノロジを使用する必要があります。 現時点では、次のテクノロジがサポートされています。
    - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)」に記載されているように、SIOS Datakeeper を使用するクラスター ソリューションはゾーンをまたいでサポートされます
    - SUSE linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」に記載されているように構築された NFS 共有がサポートされています
    - 現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)」に記載されているように、Windows スケールアウト ファイル サービス (SOFS) を使用するソリューションは**ゾーン間ではサポートされていません**
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)または追加のアプリケーション インスタンスをビルドする場合に、SBD デバイスをホストするために使用されます
- 一部の重要なビジネス トランザクションやジョブの実行時の一貫性を実現するため。 SAP バッチ サーバー グループ、ログオン グループ、または RFC グループを使用して、アクティブな DBMS インスタンスを含むゾーン内の特定のアプリケーション インスタンスに、特定のバッチ ジョブとユーザーを直接送ることができます。 ただし、ゾーン ベースのフェールオーバーの場合は、これらのグループを残っているゾーン内のダイアログ インスタンスに手動で移動する必要があります 
- アプリケーション インスタンスの一部をデプロイしたゾーンがサービスから外れる場合に、各ゾーン内の休止中のダイアログ インスタンスをデプロイして以前のリソース容量をすぐに取得できるようにするかどうかを決定します


## <a name="activepassive-deployment"></a>アクティブ/パッシブ デプロイ
ゾーン内およびゾーン間のネットワーク トラフィックのネットワーク待ち時間の間に許容できる差が見つからない場合、デプロイできるアーキテクチャは、SAP アプリケーション レイヤーの観点からアクティブ/パッシブの特徴を持ちます。 "アクティブ" なゾーンを定義します。これは、完全なアプリケーション レイヤーをデプロイするゾーンであり、アクティブな DBMS インスタンスとアクティブな SAP セントラル サービス インスタンスを実行するゾーンです。 このような構成では、ビジネス トランザクションとバッチ ジョブで実行時間に極端な違いがないことを確認します。これは、ジョブがアクティブな DBMS インスタンスと同じゾーンで実行するかどうかに依存します。

このようなアーキテクチャの基本的なレイアウトを次に示します。

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

この構成には、次の考慮事項が適用されます。

- 可用性セットを Azure 可用性ゾーンにデプロイすることはできません。 そのため、この場合は、アプリケーション レイヤーに対して 1 つの更新ドメインと障害ドメインを作成します。 理由は、1 つのゾーンだけにデプロイされるためです。 この構成は、Azure 可用性セットにアプリケーション レイヤーをデプロイすることを予測する参照アーキテクチャと比較するとわずかな後退です。
- このようなアーキテクチャを運用するには、注意深く監視し、デプロイしたアプリケーション レイヤーと同じゾーン内にアクティブな DBMS と SAP セントラル サービスのインスタンスを維持する必要があります。 SAP セントラル サービスまたは DBMS インスタンスのフェールオーバーの場合は、できるだけ早く SAP アプリケーション レイヤーがデプロイされたゾーンに手動でフェールオーバーできることを確認します
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターに使用する Azure ロード バランサーは、[Standard SKU のロード バランサー](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)にする必要があります。 Basic ロード バランサーは、ゾーンをまたいでは機能しません
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークとそのサブネットは、ゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける**必要はありません**
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません
- Azure Premium Storage または [Ultra SSD ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 その結果、重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 そのような共有ディスクまたは共有をゾーン間でレプリケートするテクノロジを使用する必要があります。 現時点では、次のテクノロジがサポートされています。
    - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)」に記載されているように、SIOS Datakeeper を使用するクラスター ソリューションはゾーンをまたいでサポートされます
    - SUSE linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」に記載されているように構築された NFS 共有がサポートされています
    - 現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)」に記載されているように、Windows スケールアウト ファイル サービス (SOFS) を使用するソリューションは**ゾーン間ではサポートされていません**
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)または追加のアプリケーション インスタンスをビルドする場合に、SBD デバイスをホストするために使用されます
- ゾーンで障害が発生した場合にアプリケーション リソースを開始できるよう、(DBMS の観点から) パッシブ ゾーンに休止中の VM をデプロイします
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) を使用して、ゾーン間で休止中の VM にアクティブな VM をレプリケートすることはできません。 現在、Azure Site Recovery ではそのような機能を満たすことはできません
- ゾーン障害が発生した場合に 2 番目のゾーンで SAP アプリケーション レイヤーを自動的に開始できるオートメーションに投資します

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>高可用性とディザスター リカバリーの構成の組み合わせ
Microsoft は特定の Azure リージョン内で異なる Azure 可用性ゾーンをホストしている施設間の地理的距離に関する情報を提供していませんが、一部の顧客は、ゾーンを利用して HA と DR の構成を組み合わせることで、回復ポイントの目標 (**R****P****O**) をゼロにすることを約束しています。 つまり、ディザスター リカバリーの場合であっても、コミットされたデータベース トランザクションが失われることはありません。 

> [!NOTE]
> このような構成は、特定の状況に対してのみ推奨されます。 たとえば、セキュリティとコンプライアンスの理由のためデータが Azure リージョンから外に出られないような場合です。 

次の図ではこのような構成を示します。

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

この構成には、次の考慮事項が適用されます。

- 可用性ゾーンをホストしている施設の間に大きな距離があるものと想定します。 または、特定の Azure リージョンに留まることを強制されています。 可用性セットを Azure 可用性ゾーンにデプロイすることはできません。 そのため、この場合は、アプリケーション レイヤーに対して 1 つの更新ドメインと障害ドメインを作成します。 理由は、1 つのゾーンだけにデプロイされるためです。 これは、Azure 可用性セットにアプリケーション レイヤーをデプロイすることを予測する参照アーキテクチャと比較すると後退です。
- このようなアーキテクチャを運用するには、注意深く監視し、デプロイしたアプリケーション レイヤーと同じゾーン内にアクティブな DBMS と SAP セントラル サービスのインスタンスを維持する必要があります。 SAP セントラル サービスまたは DBMS インスタンスのフェールオーバーの場合は、できるだけ早く SAP アプリケーション レイヤーがデプロイされたゾーンに手動でフェールオーバーできることを確認します
- アクティブな QA アプリケーション インスタンスを実行する VM に、運用アプリケーションのインスタンスがプレインストールされています。
- ゾーンの障害が発生した場合は、QA アプリケーションのインスタンスをシャットダウンし、代わりに運用インスタンスを開始します。 これを行うには、アプリケーション インスタンスの仮想名を使用する必要があることに注意してください
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターに使用する Azure ロード バランサーは、[Standard SKU のロード バランサー](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)にする必要があります。 Basic ロード バランサーは、ゾーンをまたいでは機能しません
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークとそのサブネットは、ゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける**必要はありません**
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません
- Azure Premium Storage または [Ultra SSD ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 その結果、重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 そのような共有ディスクまたは共有をゾーン間でレプリケートするテクノロジを使用する必要があります。 現時点では、次のテクノロジがサポートされています。
    - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)」に記載されているように、SIOS Datakeeper を使用するクラスター ソリューションはゾーンをまたいでサポートされます
    - SUSE linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」に記載されているように構築された NFS 共有がサポートされています
    - 現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)」に記載されているように、Windows スケールアウト ファイル サービス (SOFS) を使用するソリューションは**ゾーン間ではサポートされていません**
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)または追加のアプリケーション インスタンスをビルドする場合に、SBD デバイスをホストするために使用されます





## <a name="next-steps"></a>次の手順
Azure 可用性ゾーンにデプロイする次の手順を確認してください。

- [Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






