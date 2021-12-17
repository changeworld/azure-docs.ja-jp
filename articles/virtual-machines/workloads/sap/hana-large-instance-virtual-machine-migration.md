---
title: SAP HANA on Azure (Large Instances) を Azure Virtual Machines に移行する | Microsoft Docs
description: SAP HANA on Azure (Large Instances) を Azure Virtual Machines に移行する方法
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2021
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e401ee0a8760033bfada5f054ad4c61904a62a6a
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136851"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA on Azure Large Instance の Azure Virtual Machines への移行
この記事では、Azure Large Instance で使用できるデプロイ シナリオと、移行時のダウンタイムが最小になる計画と移行の方法について説明します。

## <a name="overview"></a>概要
Azure Large Instances for SAP HANA (HLI) は、2016 年 9 月に最初に発表されました。 それ以来、多くの人が、このハードウェアをメモリ内コンピューティング プラットフォーム用のサービスとして導入しています。 しかし、近年、Azure 仮想マシン (VM) のサイズ拡張機能と HANA スケールアウト デプロイのサポートは、ほとんどの企業ユーザーが求める ERP データベース容量を超えています。 多くの人が、SAP HANA ワークロードを物理サーバーから Azure VM へ移行することに関心を示しています。

この記事は、構成の詳細な手順に関するドキュメントではありません。 ここでは、一般的なデプロイ モデルについて説明し、計画と移行に関するアドバイスを提示しています。 目的は、移行のダウンタイムを最小限に抑えるために必要な考慮事項を示すことです。

## <a name="assumptions"></a>外部からの影響
この記事は以下を前提としています。
- ここでは、ソフトウェアの大規模なアップグレードや修正プログラムの適用を必要としない、Hana Large Instance (HLI) から Azure VM への同種の HANA データベース コンピューティング サービスの移行のみを検討します。 このようなマイナーな更新には、関連する SAP ノートでサポート対象として明示されている、より新しいオペレーティング システム (OS) バージョンや HANA バージョンの使用が含まれます。 
- 更新とアップグレードのすべてのアクティビティを移行の前または後に行います。  たとえば、SAP HANA MCOS の MDC デプロイへの変換などです。 
- ダウンタイムを最小限に抑える移行アプローチは、SAP HANA システム レプリケーションです。 他の移行方法については、このドキュメントでは説明しません。
- このガイダンスは、HLI の Rev3 と Rev4 両方の SKU に適用されます。
- HANA のデプロイ アーキテクチャが移行の間に変更されることは基本的にありません。  つまり、単一インスタンスのディザスター リカバリー (DR) を備えたシステムは、移行先でも同様に維持されます。
- ターゲット (移行後の) アーキテクチャのサービス レベル アグリーメント (SLA) を確認し、理解しています。 
- HLI と VM では商用使用条件が異なります。 VM の使用状況を監視して、コスト管理を行います。
- HLI は専用のコンピューティング プラットフォームであり、VM は共有されていても分離されたインフラストラクチャ上で動作していることを理解しています。
- ターゲット VM で目的のアーキテクチャがサポートされていることを確認しました。 SAP HANA のデプロイについて認定されているサポート対象の VM SKU を確認するには、[SAP HANA ハードウェア ディレクトリ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)を参照してください。
- 設計と移行計画の検証が済んでいます。
- プライマリ サイトと共にディザスター リカバリー VM を計画します。  移行後、VM で実行されているプライマリ サイトの DR ノードとして HLI を使用することはできません。
- ビジネスの復旧可能性とコンプライアンス要件に基づいて、必要なバックアップ ファイルをターゲット VM にコピーしてあります。 VM がアクセス可能なバックアップを使用すると、移行期間中に特定の時点への復旧が可能になります。
- SAP HANA システム レプリケーション (HSR) 高可用性 (HA) の場合、[SLES](./high-availability-guide-suse-pacemaker.md) と [RHEL](./high-availability-guide-rhel-pacemaker.md) に関する SAP HANA HA ガイドに従って、STONITH デバイスを設定し、構成する必要があります。  HLI の場合のように事前に構成されることはありません。
- この移行アプローチでは、Optane 構成の HLI SKU については説明しません。

## <a name="deployment-scenarios"></a>デプロイメント シナリオ
すべての HLI シナリオで Azure VM への移行が可能です。 次の表は、HLI の一般的なデプロイ モデルをまとめたものです。 Azure の補完的なサービスを利用するには、アーキテクチャの小規模な変更が必要になることがあります。

| シナリオ ID | HLI シナリオ | そのまま VM に移行できるか? | 注記 |
| --- | --- | --- | --- |
| 1 | [SID が 1 つの単一ノード](./hana-supported-scenario.md#single-node-with-one-sid) | はい | - |
| 2 | [1 つのシステムに複数のコンポーネントがある単一ノード (MCOS)](./hana-supported-scenario.md#single-node-mcos) | はい | - |
| 3 | [ストレージ レプリケーションを使用する DR を備えた単一ノード](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | いいえ | ストレージ レプリケーションは、Azure 仮想プラットフォームの場合使用できません。現在の DR ソリューションを、HSR またはバックアップと復元のいずれかに変更します。 |
| 4 | [ストレージ レプリケーションを使用する DR (多目的) を備えた単一ノード](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | いいえ | ストレージ レプリケーションは、Azure 仮想プラットフォームの場合使用できません。現在の DR ソリューションを、HSR またはバックアップと復元のいずれかに変更します。 |
| 5 | [HSR と STONITH を使用した高可用性](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | はい | ターゲット VM に対して構成済みの SBD はありません。  STONITH ソリューションを選択してデプロイします。  使用可能なオプション: Azure フェンス エージェント ([RHEL](./high-availability-guide-rhel-pacemaker.md) と [SLES](./high-availability-guide-suse-pacemaker.md) の両方でサポートされます)、STONITH ブロック デバイス (SBD)。 |
| 6 | [HSR による HA、ストレージ レプリケーションによる DR](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | いいえ | DR のためのストレージ レプリケーションを、HSR またはバックアップと復元のいずれかに置き換えます。 |
| 7 | [ホストの自動フェールオーバー (1+1)](./hana-supported-scenario.md#host-auto-failover-11) | はい | Azure VM での共有ストレージには Azure NetApp Files (ANF) を使用します。 |
| 8 | [スタンバイありのスケールアウト](./hana-supported-scenario.md#scale-out-with-standby) | はい | ストレージに対してのみ ANF を使用する M128s、M416s、M416ms の VM での BW/4HANA。 |
| 9 | [スタンバイなしのスケールアウト](./hana-supported-scenario.md#scale-out-without-standby) | はい | M128s、M416s、M416ms の VM での BW/4HANA (ストレージ用に ANF を使用する場合と、使用しない場合)。 |
| 10 | [ストレージ レプリケーションを使用しする DR を備えたスケールアウト](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | いいえ | DR のためのストレージ レプリケーションを、HSR またはバックアップと復元のいずれかに置き換えます。 |
| 11 | [HSR を使用する DR を備えた単一ノード](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | はい | - |
| 12 | [単一ノードの HSR から DR (コスト最適化)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | はい | - |
| 13 | [HSR を使用する HA と DR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | はい | - |
| 14 | [HSR を使用する HA と DR (コスト最適化)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | はい | - |
| 15 | [HSR を使用する DR を備えたスケールアウト](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | はい | M128s での BW/4HANA。 M416s、M416ms VM (ストレージ用に ANF を使用する場合と、使用しない場合)。 |


## <a name="source-hli-planning"></a>ソース (HLI) の計画
HLI サーバーをオンボードするときに、お客様と Microsoft のサービス管理で、SAP HANA データベースを実行するためのコンピューティング、ネットワーク、ストレージ、OS 固有の設定を計画しました。 Azure VM への移行についても、同様の計画を行う必要があります。

### <a name="sap-hana-housekeeping"></a>SAP HANA のハウスキープ処理 
データベースの内容を整理し、不要なデータや古いログが新しいデータベースに移行されないように運用することをお勧めします。  一般に、ハウスキープ処理では、古いデータ、期限切れのデータ、または非アクティブなデータを削除またはアーカイブします。  この "データ検疫" を、運用環境で使用する前に非運用システムでテストし、データのトリミングの有効性を検証する必要があります。

### <a name="allow-network-connectivity-for-new-vms-and-virtual-network"></a>新しい VM と仮想ネットワークにネットワーク接続できるようにする 
HLI デプロイの場合、「[SAP HANA (Large Instances) のネットワーク アーキテクチャ](./hana-network-architecture.md)」で説明されている情報に基づいて、ネットワークが設定されました。 また、ネットワーク トラフィックのルーティングは、「[Azure でのルーティング](./hana-network-architecture.md#routing-in-azure)」で説明されている方法で行われます。
- 新しい VM の移行ターゲットは、HLI に接続するためのアクセス許可が既にある IP アドレス範囲を持つ既存の仮想ネットワークに配置されていますか? その場合、接続をそれ以上更新する必要はありません。
- 新しい Azure VM は、おそらく別のリージョンにある新しい Microsoft Azure Virtual Network に配置され、既存の仮想ネットワークとピアリングされていますか? その場合、元の HLI プロビジョニングの ExpressRoute サービス キーとリソース ID を使用して、この新しい仮想ネットワーク IP 範囲にアクセスできます。 Microsoft のサービス管理と協力して、仮想ネットワークが HLI に接続できるようにします。  
    > [!NOTE]
    > アプリケーション層とデータベース層の間のネットワーク待機時間を最小限にするには、アプリケーション層とデータベース層の両方を同じ仮想ネットワーク上に配置する必要があります。  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>既存のアプリ層の可用性セット、可用性ゾーン、近接配置グループ (PPG)
現在のデプロイ モデルは、特定のサービス レベル目標を達成するために設計されています。 この移動では、ターゲット インフラストラクチャが設定されている目標を満たすか、それを超えることが保証されます。  
おそらく、SAP アプリケーション サーバーは可用性セットに配置されているでしょう。 現在のデプロイ サービス レベルが十分であり、ターゲット VM で HLI 論理名のホスト名が想定されている場合、ドメイン ネーム サービス (DNS) の VM の IP を指しているアドレス解決は、SAP プロファイルの更新をしなくても更新されます。
- PPG を使用していない場合は、すべてのアプリケーション サーバーと DB サーバーを同じゾーンに配置して、ネットワーク待機時間を最小限に抑えるようにしてください。
- PPG を使用している場合は、この記事の後のセクション「[可用性セット、可用性ゾーン、近接配置グループ](#availability-sets-availability-zones-and-proximity-placement-groups)」を参照してください。

### <a name="storage-replication-discontinuance-process-if-used"></a>ストレージ レプリケーション停止プロセス (使用されている場合)
ストレージ レプリケーションを DR ソリューションとして使用した場合は、SAP アプリケーションをシャットダウンした後で終了させます。 その前に、最新の SAP HANA カタログ、ログ ファイル、およびデータ バックアップが、リモート DR HLI ストレージ ボリュームにレプリケートされている必要があります。 このレプリケーションは、物理サーバーから Azure VM への移行中に障害が発生した場合に重要です。

### <a name="data-backups-preservation-consideration"></a>データ バックアップの保持に関する考慮事項
Azure VM 上の SAP HANA に切り替えた後、HLI 上のスナップショット ベースのデータまたはログ バックアップは、簡単にアクセスしたり、VM に復元したりすることはできません。 カットオーバーの数週間前でも、HLI でファイル レベルのバックアップとスナップショットを作成することをお勧めします。 これらのバックアップを、新しい SAP HANA VM からアクセスできる Azure ストレージ アカウントにコピーします。 移行初期においても、Azure ベースのバックアップによって、ポイントインタイム リストアの要件を満たすのに十分な履歴が構築される前に、ファイル レベルのバックアップを作成します。 

HLI コンテンツのバックアップは非常に重要です。 また、ロールバックが必要な場合に備えて、SAP ランドスケープの完全バックアップにすぐにアクセスできるようにすることをお勧めします。

### <a name="adjusting-system-monitoring"></a>システム監視を調整する 
SAP ランドスケープ内のシステムの監視とアラート通知の送信には、さまざまなツールが使用できます。 監視のための変更を組み込み、必要に応じてアラート通知の受信者を更新するように、適切な対応をとる必要があります。

### <a name="microsoft-operations-team-involvement"></a>Microsoft 運用チームの関与 
既存の HLI インスタンスで Azure portal ベースからチケットを開きます。  サポート チケットが作成されると、サポート エンジニアからメールで連絡があります。  

### <a name="engage-microsoft-account-team"></a>Microsoft アカウント チームを参加させる
コンピューティング リソースに対する不要な費用を最小限に抑えるために、HLI コントラクトの更新日の近くに移行を計画します。 HLI を使用停止にするには、コントラクトの終了と、ユニットのシャットダウンを調整します。

## <a name="destination-planning"></a>移行先の計画
既存のものを置き換える新しいインフラストラクチャをデプロイするには、慎重な計画が不可欠です。 新規追加することで、より大きな構成でのニーズが満たされることを確認してください。 考慮すべき点をいくつか以下に示します。

### <a name="resource-availability-in-the-target-region"></a>ターゲット リージョンでのリソースの可用性 
現在の SAP アプリケーション サーバーのデプロイ リージョンは、通常、関連付けられている HLI の近くにあります。 ただし、HLI は、利用可能な Azure リージョンより少い場所でしか提供されていません。 物理 HLI を Azure VM に移行するときは、パフォーマンスを最適化するために、関連するすべてのサービスの近さを微調整するのに適したタイミングでもあります。  その際、選択したリージョンに必要なリソースがすべて揃っていることを確認します。 たとえば、高可用性のセットアップを用意する特定の VM ファミリまたは Azure Zones の可用性の確認が必要になる場合があります。

### <a name="virtual-network"></a>仮想ネットワーク 
既存の仮想ネットワークで新規の HANA データベースを実行しますか、それとも新しく作成して実行しますか。 主な決定要因は、SAP ランドスケープの現在のネットワーク レイアウトです。 また、インフラストラクチャが 1 つのゾーンから 2 つのゾーンのデプロイになり、PPG を使用している場合は、アーキテクチャの変更が必要になります。 詳しくは、「[SAP アプリケーションで最適なネットワーク待ち時間を実現するための Azure 近接通信配置グループ](./sap-proximity-placement-scenarios.md)」をご覧ください。   

### <a name="security"></a>セキュリティ
新しい SAP HANA VM は、その動作が新規の vnet/サブネット上でも、既存のでも、ビジネスに欠かせない新しいサービスです。 それには保護する必要があります。 会社のセキュリティ ポリシーに準拠したアクセス制御を確認します。

### <a name="vm-sizing-recommendation"></a>VM のサイズ設定に関する推奨事項
この移行は、HANA コンピューティング エンジンを適切なサイズに変更する機会でもあります。 HANA [システム ビュー](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)を HANA Studio と使用すると、システム リソースの消費量を把握できます。これにより、適切なサイズ設定に調整して支出の効率を向上させることができます。

### <a name="storage"></a>記憶域 
ストレージのパフォーマンスは、SAP アプリケーションのユーザー エクスペリエンスに影響する要因の 1 つです。 指定された VM SKU に対して公開される最小ストレージ レイアウトがあります。 詳細については「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」を参照してください。 これらの仕様を確認し、既存の HLI システムの統計と比較して、新しい HANA VM の適切な IO 容量とパフォーマンスを確保することをお勧めします。

新しい HANA VM とその関連サーバー用に PPG を構成しますか。 その場合、サポート チケットを送信し、ストレージと VM のコロケーションを検査して確認します。 バックアップ ソリューションの変更が必要な場合があるため、運用費を見て驚かないよう、ストレージ コストも再検討します。

### <a name="storage-replication-for-disaster-recovery"></a>ディザスター リカバリーのためのストレージ レプリケーション
HLI の場合、ストレージ レプリケーションはディザスター リカバリーの既定のオプションでした。 この機能は、Azure VM での SAP HANA に対する既定のオプションではありません。 HSR、バックアップと復元、または他のサポートされているソリューションを使用して、ビジネス ニーズを満たすことを検討します。

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>可用性セット、可用性ゾーン、近接配置グループ 
アプリケーション レイヤーと SAP HANA の間の距離を短縮することで、ネットワーク待機時間を最小限に抑えることができます。 新しいデータベース VM と現在の SAP アプリケーション サーバーを PPG に配置します。 SAP デプロイでの Azure 可用性セットおよび可用性ゾーンと PPG の連携方法については、[近接配置グループ](./sap-proximity-placement-scenarios.md)に関する記事を参照してください。

HANA システムのメンバーが複数の Azure ゾーンにデプロイされている場合は、選択したゾーンの待機時間プロファイルに注意する必要があります。 SAP システム コンポーネントを配置して、SAP アプリケーションとデータベース間の距離を短くします。 パブリック ドメインの[可用性ゾーン待機時間テスト ツール](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)を使用すると、簡単に測定することができます。  


### <a name="backup-strategy"></a>バックアップ戦略
多くのお客様は既に、HLI の SAP HANA 用にサードパーティ製のバックアップ ソリューションを使用しています。  その場合は、追加の保護されている VM と HANA データベースのみ、構成の必要があります。 移行後にコンピューターを使用しなくなる場合、行われている HLI バックアップ ジョブのスケジュールを解除できます。

現在、VM の SAP HANA に対して Azure バックアップを一般に利用できます。  Azure VM での SAP HANA バックアップの詳細については、[バックアップ](../../../backup/backup-azure-sap-hana-database.md)、[復元](../../../backup/sap-hana-db-restore.md)、[管理](../../../backup/sap-hana-db-manage.md)に関する記事を参照してください。

### <a name="dr-strategy"></a>DR 戦略
サービス レベルの目標がより長い復旧時間に対応している場合は、バックアップは簡単になります。 BLOB ストレージへのバックアップと特定の場所への復元、または新しい VM への復元は、最も単純で費用のかからない DR 戦略です。
 
大規模なインスタンス プラットフォームの場合、HANA DR は通常 HSR を使用して行われます。 また、HSR は、Azure VM で最も自然でネイティブな SAP HANA DR ソリューションです。 移行元のデプロイが単一インスタンスかクラスターかに関係なく、移行元インフラストラクチャのレプリカが DR リージョン内に必要です。
この DR レプリカは、プライマリ HLI から VM への移行が完了した後で構成されます。  DR HANA DB は、セカンダリ レプリケーション サイトとして、VM インスタンス上のプライマリ SAP HANA に登録されます。  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP アプリケーション サーバーの接続先の変更
HSR の移行により、HANA データベース ホストが新しくなり、アプリケーション層のデータベース ホスト名も新しくなります。 新しいホスト名を反映するように SAP プロファイルを変更します。 ホスト名を維持する名前解決によって切り替えが行われる場合は、プロファイルを変更する必要はありません。

### <a name="operating-system-os"></a>オペレーティング システム (OS)
HLI と VM の OS イメージは、同じリリース レベル (SLES 12 SP4 など) の場合でも、同一ではありません。 HLI で必要なパッケージ、修正プログラム、パッチ、カーネル、セキュリティ修正を検証します。 次に、同じパッケージをターゲットにインストールします。  HSR を使用して、古い OS から新しい OS バージョンの VM にレプリケートできます。  サポートされているバージョンについては、[SAP ノート 2763388](https://launchpad.support.sap.com/#/notes/2763388) を確認してください。

### <a name="new-sap-license-request"></a>新しい SAP ライセンスの要求
VM に移行されたので、新しい HANA システムの新しい SAP ライセンスを要求する必要があることについての簡単な注意です。

### <a name="service-level-agreement-sla-differences"></a>サービス レベル アグリーメント (SLA) の違い
HLI と Azure VM の間の可用性 SLA の違いについての注意です。  たとえば、クラスター化された HLI HA ペアの可用性は 99.99% です。 同じ SLA を実現するには、可用性ゾーンに VM をデプロイする必要があります。 「[Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)」では、お客様がターゲット インフラストラクチャを計画できるように、さまざまな VM 構成の可用性について説明します。


## <a name="migration-strategy"></a>移行戦略
このドキュメントでは、HLI から Azure VM への移行に対する HANA システム レプリケーション アプローチについてのみ説明します。  デプロイするターゲット ストレージ ソリューションによっては、プロセスが若干異なります。 以下ではおおまかな手順を説明します。

### <a name="vm-with-premiumultra-disks-for-data"></a>データ用に Premium または Ultra ディスクを使用する VM
Premium または Ultra ディスクを使用してデプロイされた VM の場合、標準の SAP HANA システム レプリケーション構成を HSR のセットアップに適用できます。 システム レプリケーションの設定手順の概要については、[SAP ヘルプ記事](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)を参照してください。 この記事では、セカンダリ システムの引き継ぎ、プライマリへのフェールバック、およびシステム レプリケーションの無効化についても説明します。 移行に必要なのは、セットアップ、引き継ぎ、レプリケーションの無効化の手順だけです。  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>データとログボリュームに ANF を使用する VM
大まかに言えば、完全なデータ ボリュームとログ ボリュームの最新の HLI ストレージ スナップショットを、Azure ストレージにコピーする必要があります。 そこから、ターゲットの HANA VM によってアクセスと回復ができます。  コピー処理は、任意のネイティブ Linux コピー ツールで実行できます。  

> [!IMPORTANT]
> HANA データベースのサイズとネットワーク帯域幅によっては、コピーとデータ転送に時間がかかることがあります。 コピー処理の大部分は、プライマリ HANA データベースのダウンタイムより前に行っておく必要があります。

### <a name="mcos-to-mdc-conversion"></a>MCOS から MDC への変換
HLI の一部のお客様は、1 つのシステムに複数のコンポーネントが含まれる (MCOS) デプロイ モデルを使用しています。 それは、SAP HANA の初期のバージョンにあった複数データベース コンテナー (MDC) のストレージ スナップショットに関する制限を回避するためでした。  MCOS モデルの場合、複数の独立した SAP HANA インスタンスが、1 つの HANA Large Instance に積み重ねられます。 HSR を使用した移行は問題なく機能しますが、それぞれが 1 つのテナント データベースを備える複数の HANA VM になります。 このモデルを使用すると、希望するものよりビジーな状況になります。 SAP HANA 2.0 の既定のデプロイは MDC です。 代替策は、HSR の移行の後で [HANA テナントの移動](https://launchpad.support.sap.com/#/notes/2472478)を実行することです。 HANA テナントの移行により、これらの独立した HANA データベースが、1 つの HANA コンテナー内の共同テナントに統合されます。  

### <a name="application-layer-consideration"></a>アプリケーション層についての考慮事項
データベース サーバーが SAP システムの中心と見なされます。  すべてのアプリケーション サーバーを、SAP HANA データベースの近くに配置する必要があります。 場合によっては、新しい PPG を使用するときに、HANA VM が配置されている PPG に既存のアプリケーション サーバーを移動することが必要になる場合があります。  既に展開テンプレートを使用している場合は、新しいアプリケーション サーバーの構築が簡単になる可能性があります。

既存のアプリケーション サーバーと新しい HANA VM を最適な場所に配置します。 容量を増やす必要がある場合を除き、新しいアプリケーション サーバーを構築する必要はありません。

サービスの可用性を向上させるために新しいインフラストラクチャを構築する場合、既存のアプリケーション サーバーが不要になることがあります。 それらはシャットダウンして削除できます。
ターゲット VM のホスト名を変更し、HLI のホスト名と異なる場合は、新しいホストを参照するように、SAP アプリケーション サーバーのプロファイルを調整します。 HANA データベースの IP アドレスのみを変更した場合は、着信接続を新しい HANA VM に誘導するために、DNS レコードを更新します。

### <a name="acceptance-test"></a>受け入れテスト
HLI から VM への移行の場合、異種の移行とは異なり、データベースの内容に対する重要な変更はありません。 ただし、新しいセットアップのパフォーマンスを確認することをお勧めします。  

### <a name="cutover-plan"></a>切り替え計画
この移行は簡単なものですが、既存のデータベースの使用停止を伴います。  フォールバックが必要な場合に備えて、ソース システムとそのコンテンツおよびバックアップ イメージが保持されるように慎重に計画することが不可欠です。 適切に計画すれば、より迅速に元に戻すことができます。   

## <a name="post-migration"></a>移行後の処理
データの整合性を保証するため、HLI に依存するサービスと接続を安全に分離するまでは、移行ジョブを行いません。 また、不要なサービスをシャットダウンすることをお勧めします。 このセクションでは、いくつかの重要な項目について説明します。

### <a name="decommissioning-the-hli"></a>HLI の使用停止
HANA データベースを Azure VM に正常に移行した後、HLI データベースでビジネス トランザクションが実行されていないことを確認します。  ただし、ローカル バックアップ保有期間の期間だけ HLI を実行させておくと、必要に応じてより迅速な復旧が可能になります。 ローカルのバックアップ保有期間が過ぎている場合にのみ、HANA Large Instance を使用停止にする必要があります。 そうであれば、Microsoft の担当者に連絡して、Microsoft との HLI コミットメントの契約を終了します。

### <a name="remove-any-proxy-for-example-iptables-bigip-configured-for-hli"></a>HLI 用に構成されたプロキシ (例: Iptables、BIGIP) を削除する 
IPTables のようなプロキシ サービスを使用して HLI との間のオンプレミス トラフィックをルーティングしている場合、VM に正常に移行した後、それは不要になります。  それでも、HLI がまだスタンバイになっている間は、この接続サービスを保持する必要があります。  HLI が完全に使用停止された場合のみ、サービスをシャットダウンします。

### <a name="remove-global-reach-for-hli"></a>HLI の Global Reach の削除 
Global Reach は、お客様の ExpressRoute ゲートウェイと HLI ExpressRoute ゲートウェイを接続するために使用されます。  プロキシ サービスを使用しなくても、お客様のオンプレミスのトラフィックは HLI テナントに直接到達することができます。 移行後は HLI ユニットが存在しないので、この接続はもう必要ありません。 それでも、IPTables プロキシ サービスと同様に、HLI が完全に使用停止になるまで、Global Reach も保持する必要があります。

### <a name="operating-system-subscription--movereuse"></a>オペレーティング システムのサブスクリプション - 移動と再利用
VM サーバーがデプロイされ、HLI が使用停止になれば、OS サブスクリプションを置き換えたり再利用したりできます。 OS ライセンスを二重に支払う必要はありません。

## <a name="next-steps"></a>次のステップ

SAP デプロイを計画する。

> [!div class="nextstepaction"]
> [Azure での SAP ワークロードの計画とデプロイに関するチェックリスト](sap-deployment-checklist.md)
