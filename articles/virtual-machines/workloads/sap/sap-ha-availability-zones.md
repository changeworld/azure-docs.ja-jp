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
ms.openlocfilehash: e319fb5bc1e0ed3354a09b685405656c7f3131bc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858807"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Azure Availability Zones での SAP ワークロードの構成

[Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) は、Azure で提供されている高可用性機能の 1 つです。 可用性ゾーンにより、Azure での SAP ワークロードの全体的な可用性が向上します。 可用性ゾーンは、いくつかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)で既に提供されています。 今後、さらに多くのリージョンで利用できるようになります。 

SAP の高可用性の基本的なアーキテクチャの説明を次の図に示します。

![標準的な HA の構成](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP のアプリケーション レイヤーは、1 つの Azure [可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)にデプロイされます。 SAP セントラル サービスの高可用性を確保するには、別々の可用性セットに 2 つの VM をデプロイします。 インフラストラクチャーやソフトウェアの問題に備える自動フェールオーバーの高可用性フレームワークとして Windows フェールオーバー クラスター サービスまたは Pacemaker (Linux) を使用します。 そのようなデプロイの詳細については、以下のドキュメントを参照してください。

- [クラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [ファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

同様のアーキテクチャが、SAP NetWeaver、S/4HANA、または Hybris システムの DBMS レイヤーにも適用されます。 インフラストラクチャーまたはソフトウェアの障害から保護するために、フェールオーバー クラスター ソリューションを使用して、アクティブ/パッシブ モードで DBMS レイヤーをデプロイします。 このフェールオーバー クラスター ソリューションは、DBMS 固有のフェールオーバー フレームワーク、Windows フェールオーバー クラスター サービス、または Pacemaker の場合があります。 

Azure Availability Zones を使用して同じアーキテクチャをデプロイするには、前述のアーキテクチャに対していくつかの変更を行う必要があります。 これらの変更については、このドキュメントで説明します。

## <a name="considerations-deploying-across-availability-zones"></a>Availability Zones へのデプロイに関する考慮事項

Availability Zones を使用する際、考慮すべき点がいくつかあります。

- Azure Availability Zones は、1 つの Azure リージョン内の異なるゾーン間が一定の距離になることを保証しません。
- Azure Availability Zones は、理想的な DR ソリューションではありません。 自然災害により、電力設備に対する大きな損害を含め、世界の一部のリージョンで広範囲にわたる損害が発生する場合があります。 異なるゾーン間の距離が、適切な DR ソリューションと見なすのに十分でない可能性があります。
- 可用性ゾーンをまたいだネットワーク待ち時間は、Azure リージョンごとに異なります。 あるゾーンからアクティブ DBMS VM へのネットワーク待ち時間がビジネス プロセスの影響から許容可能なものであるため、SAP アプリケーション レイヤーをさまざまなゾーンにわたってデプロイして実行できる場合があります。 しかし、一部の Azure リージョンでは、異なるゾーンにデプロイされたアクティブ DBMS VM と SAP アプリケーション インスタンスとの間の待ち時間が、SAP ビジネス プロセスにとってあまりにも煩わしく、許容できないものである場合があります。 そのため、ゾーンをまたがる待ち時間が長すぎる場合は、デプロイ アーキテクチャをアプリケーションのアクティブ/アクティブ アーキテクチャまたはアクティブ/パッシブ アーキテクチャとは異なるものにする必要があります。
- 異なるゾーン間で測定したネットワーク待ち時間に基づいて、Azure Availability Zones を使用できる構成を決定します。 ネットワーク待ち時間は、次の 2 つの点で重要な役割を果たします。
    - 同期レプリケーションを確立する必要がある 2 つの DBMS インスタンス間の待ち時間。 ネットワークの待ち時間が長いほど、ワークロードのスケーラビリティに影響する可能性が高くなります
    - アクティブな DBMS インスタンスを含むゾーンで SAP ダイアログ インスタンスを実行している VM と、別のゾーンの同様の VM との間のネットワーク待ち時間の違い。 この違いが大きいほど、DBMS を含むゾーン、または別のゾーンのどちらで実行しているかに応じて、ビジネス プロセスとバッチ ジョブの実行時間への影響が大きくなります。


Azure VM を可用性ゾーンをまたいでデプロイして、同じ Azure リージョン内でフェールオーバー ソリューションを確立する場合、いくつかの制限があります。 次の一覧で、これらの制限を示します。

- Azure 可用性ゾーンにデプロイするには、[Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)の使用が必須です 
- 物理ゾーンに対するゾーン列挙のマッピングは、Azure サブスクリプションごとに固定されています。 さまざまなサブスクリプションを使用して SAP システムをデプロイする場合は、サブスクリプションごとに最適なゾーンを定義する必要があります。
- Azure 可用性ゾーン内に Azure 可用性セットをデプロイすることはできません。 仮想マシンのデプロイ フレームワークとして、Azure 可用性ゾーンまたは Azure 可用性セットのどちらかを選択します。
- [Azure Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) を使用して Windows フェールオーバー クラスター サービスまたは Linux Pacemaker に基づくフェールオーバー クラスター ソリューションを作成することはできません。 代わりに、[Azure Standard Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) を使用する必要があります



## <a name="ideal-availability-zones-combination"></a>可用性ゾーンの最適な組み合わせ
可用性ゾーンの利用方法を決定する前に、調査を実行して以下のことを確認する必要があります。

- Azure リージョンの 3 つの異なるゾーン間のネットワーク待ち時間。 それにより、ゾーンをまたいだネットワーク トラフィックでネットワーク待ち時間が最も短いゾーンを選択することができます。
- 任意の 1 つのゾーン内の VM 間の待ち時間と、任意の 2 つのゾーン間のネットワーク待ち時間の差
- デプロイする必要がある VM の種類が、選択した 2 つのゾーンで使用可能かどうかを示すステートメント。 特に M シリーズの仮想マシンでは、異なる M シリーズ VM SKU が 3 つのゾーンのうち 2 つのゾーンでしか使用できない状況が発生します

### <a name="network-latency-between-zones-and-within-zone"></a>ゾーン間とゾーン内のネットワーク待ち時間
異なるゾーン間の待ち時間を調べるには、以下のことを行う必要があります。

- DBMS インスタンスに使用する VM SKU を 3 つのゾーンすべてにデプロイします。 この測定を実行するときは、[Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が有効になっていることを確認します。
- ネットワーク待ち時間が最も短い 2 つのゾーンがわかったら、アプリケーション レイヤー VM として使用する VM SKU の別の 3 つの VM を 3 つの可用性ゾーンにデプロイします。 任意の 2 つの異なる "DBMS" ゾーン内の 2 つの "DBMS VM" に対して、ネットワーク待ち時間を測定します。 
- 測定ツールとしては、**niping** を使用します。 SAP 提供のこのツールの説明は、SAP サポート ノート [#500235](https://launchpad.support.sap.com/#/notes/500235) と [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) をご覧ください。 待ち時間測定用のコマンドに注目してください。 **ping** は Azure 高速ネットワーク コード パスでは機能しないので、**ping** の使用は推奨されません。

測定値と可用性ゾーンでの VM SKU の可用性に基づいて、以下の決定を行う必要があります。

- DBMS レイヤーの理想的なゾーンを定義する
- ゾーン内とゾーンをまたいだ場合のネットワーク待ち時間の違いに基づいて、アクティブ SAP アプリケーション レイヤーを 1 つのゾーン、2 つのゾーン、または 3 つ全部のゾーンのいずれに分散するかを決定する
- アプリケーションの観点から、アクティブ/パッシブまたはアクティブ/アクティブのどちらの構成をデプロイするかを決定する (後で説明)

これらの決定を行うときは、SAP ノート [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) に記載されている SAP のネットワーク待ち時間に関する推奨事項も考慮します。

### <a name="be-aware-of"></a>注意事項

> [!IMPORTANT]
> 測定と決定は、測定に使用した Azure サブスクリプションに対して有効です。 列挙されたゾーンのマッピングは、別の Azure サブスクリプションでは異なることがあるので、別の Azure サブスクリプションを使用する場合は、測定を繰り返す必要があります。


> [!IMPORTANT]
> 上で実行した測定の結果は、[Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) をサポートするすべての Azure リージョンで異なことが予想されます。 ネットワーク待ち時間の要件が同じでも、ゾーン間でネットワーク待ち時間は異なる可能性があるため、異なる Azure リージョンでは異なるデプロイ戦略の採用が必要になる場合があります。 ある Azure リージョンでは、3 つの異なるゾーン間のネットワーク待ち時間が大きく異なる可能性があることが想定されます。 一方、別のリージョンでは、3 つの異なるゾーン間のネットワーク待ち時間の差は、もっと均一になります。 **常に**ゾーン間のネットワーク待ち時間が 1 ～ 2 ミリ秒であることを求めるのは、**間違っています**。 Azure リージョン内の可用性ゾーン間のネットワーク待ち時間を一般化することはできません。


## <a name="activeactive-deployment"></a>アクティブ/アクティブのデプロイ
このデプロイ アーキテクチャがアクティブ/アクティブと呼ばれるのは、2 つまたは 3 つのゾーンにアクティブな SAP ダイアログ インスタンスをデプロイするためです。 エンキュー レプリケーションを使用する SAP セントラル サービスは、2 つのゾーン間にデプロイされます。 DBMS レイヤーについても同じで、SAP セントラル サービスと同じゾーンにデプロイされます。

そのような構成を検討するには、リージョン内で、ワークロードと同期 DBMS レプリケーションについて許容されるクロス ゾーン ネットワーク待ち時間を提供する 2 つの可用性ゾーンを見つける必要があります。 さらに、選択したゾーン内のネットワーク待ち時間とゾーン間のネットワーク待ち時間の差が、大きすぎないようにする必要があります。 後者の理由は、ジョブが DBMS サーバーを含むゾーン内で実行されているか、ゾーンをまたいで実行されているかによって、ビジネス プロセスやバッチ ジョブの実行時間の変動率があまり大きくならないようにするためです。 ある程度の変動は許容されますが、相違の要因ではありません。

2 つのゾーン間のアクティブ/アクティブ デプロイの簡略化されたスキーマは次のようになります

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

この構成には、次の考慮事項が適用されます。

- 可用性セットは Azure 可用性ゾーン内にデプロイできないため、Azure 可用性ゾーンをすべての VM に対する障害ドメインおよび更新ドメインとして扱います
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターに使用する Azure Load Balancer は、[Standard SKU Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) にする必要があります。 Basic ロード バランサーは、ゾーンをまたいでは機能しません
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークとそのサブネットは、ゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける**必要はありません**
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用します。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません
- Azure Premium Storage または [Ultra SSD ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 その結果、重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 そのような共有ディスクまたは共有をゾーン間でレプリケートするテクノロジを使用する必要があります。 現時点では、次のテクノロジがサポートされています。
    - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)」に記載されているように、SIOS Datakeeper を使用するクラスター ソリューションはゾーンをまたいでサポートされます
    - SUSE Linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」に記載されているように構築された NFS 共有がサポートされます
    - 現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)」に記載されているように、Windows スケールアウト ファイル サービス (SOFS) を使用するソリューションは **、ゾーンをまたいでサポートされません**
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)または追加のアプリケーション インスタンスをビルドする場合に、SBD デバイスをホストするために使用されます
- 重要なビジネス プロセスの実行時間の整合性を達成するには、SAP バッチ サーバー グループ、ログオン グループ、または RFC グループを使用して、アクティブな DBMS インスタンスを含むゾーン内のアプリケーション インスタンスに、特定のバッチ ジョブとユーザーを直接送ることができます。 ただし、ゾーン ベースのフェールオーバーの場合は、アクティブ DB VM を含むゾーン内の VM で実行されているインスタンスにこれらのグループを手動で移動する必要があります。  
- アプリケーション インスタンスの一部で使用されているゾーンが稼働していない場合に、以前のリソース容量をすぐに取得できるようにするために、各ゾーン内の休止中のダイアログ インスタンスをデプロイするかどうかを決定します


## <a name="activepassive-deployment"></a>アクティブ/パッシブ デプロイ
1 つのゾーン内とゾーンをまたいだネットワーク トラフィックのネットワーク待ち時間の間に許容できる差が見つからない場合、デプロイできるアーキテクチャは、SAP アプリケーション レイヤーの観点からアクティブ/パッシブの特徴を持ちます。 "アクティブ" なゾーンを定義します。これは、完全なアプリケーション レイヤーをデプロイし、アクティブな DBMS インスタンスと SAP セントラル サービス インスタンスの両方を実行するゾーンです。 このような構成では、ジョブがアクティブな DBMS インスタンスを含むゾーンで実行されるかどうかによって、ビジネス トランザクションとバッチ ジョブで実行時間に極端な違いが出ないようにします。

このようなアーキテクチャの基本的なレイアウトを次に示します。

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

この構成には、次の考慮事項が適用されます。

- 可用性セットを Azure 可用性ゾーンにデプロイすることはできません。 したがって、この場合、アプリケーション レイヤーの更新ドメインと障害ドメインは 1 つになります。 理由は、1 つのゾーンだけにデプロイされるためです。 この構成は、Azure 可用性セットにアプリケーション レイヤーをデプロイすることを予測する参照アーキテクチャと比較するとわずかな後退です。
- このようなアーキテクチャを運用するには、注意深く監視し、デプロイしたアプリケーション レイヤーと同じゾーン内にアクティブな DBMS と SAP セントラル サービスのインスタンスを維持する必要があります。 SAP セントラル サービスまたは DBMS インスタンスのフェールオーバーの場合は、できるだけ早く SAP アプリケーション レイヤーがデプロイされたゾーンに手動でフェールオーバーできることを確認します
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターに使用する Azure Load Balancer は、[Standard SKU Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) にする必要があります。 Basic ロード バランサーは、ゾーンをまたいでは機能しません
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークとそのサブネットは、ゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける**必要はありません**
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません
- Azure Premium Storage または [Ultra SSD ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 その結果、重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 そのような共有ディスクまたは共有をゾーン間でレプリケートするテクノロジを使用する必要があります。 現時点では、次のテクノロジがサポートされています。
    - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)」に記載されているように、SIOS Datakeeper を使用するクラスター ソリューションはゾーンをまたいでサポートされます
    - SUSE Linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」に記載されているように構築された NFS 共有がサポートされます
    - 現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)」に記載されているように、Windows スケールアウト ファイル サービス (SOFS) を使用するソリューションは **、ゾーンをまたいでサポートされません**
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)または追加のアプリケーション インスタンスをビルドする場合に、SBD デバイスをホストするために使用されます
- ゾーンで障害が発生した場合にアプリケーション リソースを開始できるよう、(DBMS の観点から) パッシブ ゾーンに休止中の VM をデプロイします
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) を使用して、ゾーン間で休止中の VM にアクティブな VM をレプリケートすることはできません。 現在、Azure Site Recovery ではそのような機能を満たすことはできません
- ゾーン障害が発生した場合に 2 番目のゾーンで SAP アプリケーション レイヤーを自動的に開始できるオートメーションに投資します

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>高可用性とディザスター リカバリーの構成の組み合わせ
Microsoft は、Azure リージョン内のさまざまな Azure 可用性ゾーンをホストする施設間の地理的距離に関する情報を共有していません。 この事実にもかかわらず、一部のお客様は、ゾーンを利用して HA と DR の構成を組み合わせることで、回復ポイントの目標 (**R****P****O**) をゼロにすることを約束しています。 つまり、ディザスター リカバリーの場合であっても、コミットされたデータベース トランザクションが失われることはありません。 

> [!NOTE]
> このような構成は、特定の状況に対してのみ推奨されます。 たとえば、セキュリティとコンプライアンスの理由のためデータが Azure リージョンから外に出られないような場合です。 

次の図ではこのような構成を示します。

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

この構成には、次の考慮事項が適用されます。

- 可用性ゾーンをホストしている施設の間に大きな距離があるものと想定します。 または、特定の Azure リージョンに留まることを強制されています。 可用性セットを Azure 可用性ゾーンにデプロイすることはできません。 そのため、この場合は、アプリケーション レイヤーに対して 1 つの更新ドメインと障害ドメインを作成します。 理由は、1 つのゾーンだけにデプロイされるためです。 これは、Azure 可用性セットにアプリケーション レイヤーをデプロイすることを予測する参照アーキテクチャと比較すると後退です。
- このようなアーキテクチャを運用するには、注意深く監視し、デプロイしたアプリケーション レイヤーと同じゾーン内にアクティブな DBMS と SAP セントラル サービスのインスタンスを維持する必要があります。 SAP セントラル サービスまたは DBMS インスタンスのフェールオーバーの場合は、できるだけ早く SAP アプリケーション レイヤーがデプロイされたゾーンに手動でフェールオーバーできることを確認します
- アクティブな QA アプリケーション インスタンスを実行する VM に、運用アプリケーションのインスタンスがプレインストールされています。
- ゾーンの障害が発生した場合は、QA アプリケーションのインスタンスをシャットダウンし、代わりに運用インスタンスを開始します。 これを行うには、アプリケーション インスタンスの仮想名を使用する必要があることに注意してください
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターに使用する Azure Load Balancer は、[Standard SKU Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) にする必要があります。 Basic ロード バランサーは、ゾーンをまたいでは機能しません
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークとそのサブネットは、ゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける**必要はありません**
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません
- Azure Premium Storage または [Ultra SSD ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 その結果、重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 そのような共有ディスクまたは共有をゾーン間でレプリケートするテクノロジを使用する必要があります。 現時点では、次のテクノロジがサポートされています。
    - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)」に記載されているように、SIOS Datakeeper を使用するクラスター ソリューションはゾーンをまたいでサポートされます
    - SUSE Linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」に記載されているように構築された NFS 共有がサポートされます
    - 現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)」に記載されているように、Windows スケールアウト ファイル サービス (SOFS) を使用するソリューションは**ゾーン間ではサポートされていません**
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)または追加のアプリケーション インスタンスをビルドする場合に、SBD デバイスをホストするために使用されます





## <a name="next-steps"></a>次の手順
Azure 可用性ゾーンにデプロイする次の手順を確認してください。

- [Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






