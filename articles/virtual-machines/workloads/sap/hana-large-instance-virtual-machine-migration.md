---
title: SAP HANA on Azure (Large Instances) を Azure Virtual Machines に移行する | Microsoft Docs
description: SAP HANA on Azure (Large Instances) を Azure Virtual Machines に移行する方法
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154786"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA on Azure Large Instance の Azure Virtual Machines への移行
この記事では、Azure Large Instance で使用できるデプロイ シナリオと、移行時のダウンタイムが最小になる計画と移行の方法について説明します

## <a name="overview"></a>概要
2016 年 9 月の Azure Large Instances for SAP HANA (HLI) の発表以降、多くのお客様が、このハードウェアをメモリ内コンピューティング プラットフォーム用のサービスとして導入されています。  近年では、Azure VM のスケーラビリティ拡張機能と HANA スケールアウト デプロイ モデルのサポートの組み合わせは、ほとんどの企業ユーザーが求める ERP データベース容量を超えています。  SAP HANA ワークロードを物理サーバーから Azure VM に移行することを希望するお客様が増えています。
このガイドでは、構成の詳細な手順ではなく、一般的なデプロイ モデルについて説明し、移行時のダウンタイムが最小限になるように準備するために必要な考慮事項を示すことを目的とするプランと移行のアドバイスを提供します。

## <a name="assumptions"></a>前提条件
この記事は以下を前提としています。
- 検討対象となるのは、ソフトウェアの大規模なアップグレードや修正を必要としない、Hana Large Instance (HLI) から Azure VM への同種の HANA データベース コンピューティング サービスの移行だけです。 このようなマイナーな更新には、関連する SAP ノートでサポート対象として明示されている、より新しい OS バージョンや HANA バージョンの使用が含まれます。 
- 必要に応じて、移行の前または後に実行する必要があるすべての更新とアップグレードのアクティビティ。  たとえば、SAP HANA MCOS の MDC デプロイへの変換などです。 
- ダウンタイムを最小限に抑える移行アプローチは、SAP HANA システム レプリケーションです。 他の移行方法については、このドキュメントでは説明しません。
- これは、HLI の Rev3 と Rev4 両方の SKU に適用されます。
- HANA のデプロイ アーキテクチャが移行の間に変更されることは基本的にありません。  つまり、1 つの単一インスタンスの DR を備えたシステムは、移行先でも同じように維持されます。
- お客様は、ターゲット (移行後の) アーキテクチャのサービス レベル アグリーメント (SLA) を確認し、理解しています。 
- HLI と VM の間の商業条件の違いにより、お客様は、コスト管理のために VM の使用状況を監視する必要があります。
- お客様は、HLI が専用のコンピューティング プラットフォームであることを理解し、それに同意しています。 ただし、VM は、セキュリティで保護されて他のテナントから分離された共有インフラストラクチャ上で実行されます。
- お客様は、ターゲット VM で目的のアーキテクチャがサポートされていることを確認しています。 SAP HANA のデプロイについて認定されているサポート対象のすべての VM SKU を確認するには、[SAP HANA ハードウェア ディレクトリ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)をご確認ください。
- お客様は、設計と移行計画の検証が済んでいます。
- プライマリ サイトと共にディザスター リカバリー ノードを計画します。  お客様は、移行後、VM で実行されているプライマリ サイトの HLI DR ノードを使用することはできません。
- お客様は、ビジネスの復旧可能性とコンプライアンス要件に基づいて、必要なバックアップ ファイルをターゲット VM にコピーしてあります。 これは、移行期間中の特定の時点への復旧のニーズに対応するためのものです。
- HSR HA の場合、お客様は、[SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) および [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) に関する SAP HANA HA ガイドに従って、STONITH デバイスを設定し、構成する必要があります。  HLI の場合のように事前に構成されることはありません。
- この移行アプローチでは、Optane 構成の HLI SKU については説明しません。

## <a name="deployment-scenarios"></a>デプロイメント シナリオ
次の表は、HLI のお客様による一般的なデプロイ モデルをまとめたものです。  すべての HLI シナリオで Azure VM への移行が可能です。  いくつかのシナリオでは、現在の Azure サービス内容の利点が得られるよう、小規模なアーキテクチャの変更が必要になる場合があります。

| シナリオ ID | HLI シナリオ | そのまま VM に移行できるか? | 注記 |
| --- | --- | --- | --- |
| 1 | [1 つの SID を持つ単一ノード](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | はい | - |
| 2 | [MCOS の単一ノード](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | はい | - |
| 3 | [ストレージ レプリケーションを使用する DR を備えた単一ノード](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | いいえ | ストレージ レプリケーションは、Azure 仮想プラットフォームでは使用できません。現在の DR ソリューションを、HSR またはバックアップと復元のいずれかに変更します |
| 4 | [ストレージ レプリケーションを使用する DR (多目的) を備えた単一ノード](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | いいえ | ストレージ レプリケーションは、Azure 仮想プラットフォームでは使用できません。現在の DR ソリューションを、HSR またはバックアップと復元のいずれかに変更します |
| 5 | [高可用性のための STONITH を使用する HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | はい | ターゲット VM に対して構成済みの SBD はありません。  STONITH ソリューションを選択してデプロイします。  可能なオプション: Azure フェンス エージェント ([RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) と [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) の両方についてサポートされます)、SBD |
| 6 | [HSR による HA、ストレージ レプリケーションによる DR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | いいえ | DR のためのストレージ レプリケーションを、HSR またはバックアップと復元のいずれかに置き換えます |
| 7 | [ホストの自動フェールオーバー (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | はい | Azure VM での共有ストレージには ANF を使用します |
| 8 | [スタンバイのあるスケールアウト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | はい | ストレージに対してのみ ANF を使用する M128s、M416s、M416ms の VM での BW/4HANA |
| 9 | [スタンバイのないスケールアウト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | はい | M128s、M416s、M416ms の VM での BW/4HANA (ストレージ用に ANF を使用する場合と、使用しない場合) |
| 10 | [ストレージ レプリケーションを使用しする DR を備えたスケールアウト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | いいえ | DR のためのストレージ レプリケーションを、HSR またはバックアップと復元のいずれかに置き換えます |
| 11 | [HSR を使用する DR を備えた単一ノード](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | はい | - |
| 12 | [単一ノードの HSR から DR (コスト最適化)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | はい | - |
| 13 | [HSR を使用する HA と DR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | はい | - |
| 14 | [HSR を使用する HA と DR (コスト最適化)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | はい | - |
| 15 | [HSR を使用する DR を備えたスケールアウト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | はい | M128s での BW/4HANA。 M416s、M416ms VM (ストレージ用に ANF を使用する場合と、使用しない場合) |


## <a name="source-hli-planning"></a>ソース (HLI) の計画
HLI サーバーをオンボードするときは、Microsoft のサービス管理とお客様の双方が、SAP HANA データベースを実行するためのコンピューティング、ネットワーク、ストレージ、OS 固有の設定を計画しました。  Azure VM への移行についても、同様の計画を行う必要があります。

### <a name="sap-hana-housekeeping"></a>SAP HANA のハウスキープ処理 
HANA データベースを移行する前に、データベースの内容を整理し、不要なデータや古いログが新しいデータベースに移行されないようにすることをお勧めします。  一般に、ハウスキープ処理では、古いデータ、期限切れのデータ、または非アクティブなデータを削除またはアーカイブします。  そのため、これらの "データ検疫" アクションを、運用環境で使用する前に非運用システムでテストし、データのトリムの有効性を検証する必要があります。

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>新しい VM と仮想ネットワークにネットワーク接続できるようにする 
お客様の HLI デプロイでは、「[SAP HANA (L インスタンス) のネットワーク アーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)」で説明されている情報に基づいて、Azure 仮想ネットワークからのネットワーク接続が確立されています。 また、ネットワーク トラフィックのルーティングは、「Azure でのルーティング」で説明されている方法で行われます。
1. 新しい VM を移行の HSR ターゲットとして設定するために、新しい Azure VM を、HLI ノードに接続するためのアクセス許可が既にある IP アドレス範囲を持っている既存の仮想ネットワークに配置する場合は、HLI の接続をそれ以上更新する必要はありません
2. 新しい Azure VM を新しい仮想ネットワーク (別のリージョンに存在する場合があります) に配置し、既存の仮想ネットワークとピアリングする場合は、元の HLI プロビジョニングの ExpressRoute サービス キーとリソース ID を使用して、この新しい仮想ネットワーク IP 範囲にアクセスできます。  Microsoft のサービス管理と協力して、仮想ネットワークが HLI に接続できるようにします。  注:アプリケーション層とデータベース層の間のネットワーク待機時間を最小限にするには、アプリケーション層とデータベース層の両方を同じ仮想ネットワーク上に配置する必要があります。  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>既存のアプリ層可用性セット、Availability Zones、近接通信配置グループ
現在のデプロイ モデルは、特定のサービス レベル目標を達成するために行われています。  この移動では、ターゲット インフラストラクチャが設定されている目標を満たすか、それを超えることが保証されます。  
おそらく、お客様の SAP アプリケーション サーバーは可用性セットに配置されているでしょう。  現在のデプロイ サービス レベルが十分であり 
- SAP HANA サーバーの置き換えが、DB ホスト名の IP を交換することによる名前解決によって行われる場合は、それ以上何も行う必要はありません。  
- PPG を使用していない場合は、すべてのアプリケーション サーバーと DB サーバーを同じゾーンに配置して、ネットワーク待機時間を最小限に抑えるようにしてください。
- PPG を使用している場合は、このドキュメントの移行先の計画、可用性セット、Availability Zones、近接通信配置グループ (PPG) のセクションを参照してください

### <a name="storage-replication-discontinuance-process-if-used"></a>ストレージ レプリケーション停止プロセス (使用されている場合)
DR ソリューションとしてストレージ レプリケーションを使用している場合は、SAP アプリケーションをシャットダウンし、最後の SAP HANA カタログ、ログ ファイル、データ バックアップがリモート ストレージ ボリュームにレプリケートされた後で、レプリケーションを終了する (スケジュールされていない状態にする) 必要があります。  これは、物理環境から Azure VM への移行中に障害が発生した場合に備えた、DR HLI 上の現在のデータベースに対する予防措置です。

### <a name="data-backups-preservation-consideration"></a>データ バックアップの保持に関する考慮事項
Azure VM 上の SAP HANA に切り替えた後、HLI 上のスナップショット ベースのすべてのデータ バックアップまたはログ バックアップは、必要になっても、簡単にアクセスしたり、VM に復元したりすることはできません。 移行の初期段階で、Azure ベースのバックアップによって特定時点への復旧の要件を満たすのに十分な履歴が構築される前は、HLI のスナップショットだけでなく、ファイル レベルのバックアップを実行することをお勧めします (切り替えの数日または数週前)。  これらのバックアップを、新しい SAP HANA VM からアクセスできる Azure ストレージ アカウントにコピーします。 HLI コンテンツをバックアップするだけでなく、ロールバックが必要な場合に備えて、SAP ランドスケープの完全バックアップにすぐにアクセスできるようにすることもお勧めします。

### <a name="adjusting-system-monitoring"></a>システム監視を調整する 
SAP ランドスケープ内のシステムの監視とアラート通知の送信には、さまざまなツールが使用されています。  この項目は、新しい VM を追加するときに適切な操作を行うための簡単な注意であり、必要に応じて監視を調整し、アラート通知の受信者を更新します。

### <a name="microsoft-operations-team-involvement"></a>Microsoft 運用チームの関与 
既存の HLI インスタンスで Azure portal ベースからチケットを開きます。  サポート チケットが作成されると、サポート エンジニアからメールで連絡があります。  

### <a name="engage-microsoft-account-team"></a>Microsoft アカウント チームを参加させる
コンピューティング リソースに対する不要な費用を最小限に抑えるために、HLI コントラクトの更新日の近くに移行を計画します。 HLI ブレードを使用停止にするには、コントラクトの終了と、ユニットの実際のシャットダウンを調整する必要があります。

## <a name="destination-planning"></a>移行先の計画
既存のインフラストラクチャの代わりに新しいインフラストラクチャを立ち上げるには、新しい追加機能が大きな構造に確実に適合するよう考慮する必要があります。  以下では重要な考慮ポイントを示します。

### <a name="resource-availability-in-the-target-region"></a>ターゲット リージョンでのリソースの可用性 
現在の SAP アプリケーション サーバーのデプロイ リージョンは、通常、関連付けられている HLI と近接しています。  ただし、HLI は、利用可能な Azure リージョンより少い場所でしか提供されていません。  物理 HLI から Azure VM への移行では、パフォーマンスを最適化するために、関連するすべてのサービスの近さを "微調整" することができます。  その場合の重要な考慮事項は、選択したリージョンに余分なリソースを確保することです。  たとえば、特定の VM ファミリや、高可用性のための Azure ゾーンのオファリングがあるかどうかです。

### <a name="virtual-network"></a>仮想ネットワーク 
インフラストラクチャ設計者は、新しい HANA データベースを、既存の SAP アプリケーション仮想ネットワーク内で実行するか、新しいネットワークを作成するかを、選択する必要があります。  主な決定要因は、SAP ランドスケープの現在のネットワーク レイアウトです。  たとえば、1 ゾーンを 2 ゾーンのデプロイにし、PPG を利用するために、デプロイ インフラストラクチャの変更を考慮する場合などです。 この可用性の強化により、アーキテクチャの変更が生じます。 詳しくは、「[SAP アプリケーションで最適なネットワーク待ち時間を実現するための Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)」をご覧ください。   

### <a name="security"></a>Security
新しい SAP HANA VM のデプロイに新規と既存どちらの仮想ネットワークとサブネットを使用するかにかかわらず、保護を必要とする新しいビジネス クリティカルなサービスを表します。  この新しいサービス クラスに対する会社の情報セキュリティ ポリシーに準拠した適切なアクセス制御を評価してデプロイする必要があります。 

### <a name="vm-sizing-recommendation"></a>VM のサイズ設定に関する推奨事項
この移行は、HANA コンピューティング エンジンを適切なサイズに変更する機会でもあります。  Hana [システム ビュー](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)を HANA Studio と組み合わせて利用して、システム リソースの消費量を把握できます。これにより、適切なサイズに調整して支出の効率を向上させることができます。

### <a name="storage"></a>ストレージ 
ストレージのパフォーマンスは、SAP アプリケーションのユーザー エクスペリエンスに影響する要因の 1 つです。  特定の VM SKU に基づく最小ストレージ レイアウトの推奨事項については、「[SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)」を参照してください。 これらの最小仕様を確認し、既存の HLI システムの統計と比較して、新しい HANA VM の適切な IO 容量とパフォーマンスを確保することをお勧めします。

新しい HANA VM とそれに関連するサーバーに対して PPG を構成する場合は、ストレージと HANA VM の併置を調べて確保するため、サポート チケットを提出します。  
バックアップ ソリューションの変更が必要な場合があるため、運用費を見て驚かないよう、ストレージ コストも再検討する必要があります。 

### <a name="storage-replication-for-disaster-recovery"></a>ディザスター リカバリーのためのストレージ レプリケーション
HLI では、HANA データベースのディザスター リカバリー レプリケーションの既定のオプションとして、ストレージ レプリケーションが提供されていました。 この機能は、Azure VM では既定のオプションではありません。 HSR、バックアップと復元、または他のサポートされているソリューションを使用して、ビジネス ニーズを満たすことを検討します。

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>可用性セット、Availability Zones、近接通信配置グループ 
ネットワークの待機時間を最小限に抑えるため、アプリケーション層と SAP HANA の距離を最小にすることを考慮して、新しいデータベース VM と現在の SAP アプリケーション サーバーを近接通信配置グループ (PPG) に置く必要があります。 SAP デプロイでの Azure 可用性セットおよび Availability Zones と PPG の連携方法については、[近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)に関する記事をご覧ください。
ターゲット HANA システムのメンバーが複数の Azure ゾーンにデプロイされている場合は、選択したゾーンの待機時間プロファイルを明確に把握する必要があります。 SAP システム コンポーネントの配置は、SAP アプリケーションとデータベースの間の距離が短い場合に最適です。  パブリック ドメインの[可用性ゾーン待機時間テスト ツール](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)を使用すると、簡単に測定することができます。  


### <a name="backup-strategy"></a>バックアップ戦略
多くのお客様は既に、HLI の SAP HANA 用にサードパーティ製のバックアップ ソリューションを使用しています。  その場合は、VM と HANA データベースに追加の保護を構成するだけで十分です。  移行後にマシンが使用停止になる場合、進行中の HLI バックアップ ジョブはスケジュールされなくなります。
現在、VM の SAP HANA に対して Azure バックアップを一般に利用できます。  詳細については次のリンクを参照してください: Azure VM での SAP HANA バックアップの[バックアップ](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)、[復元](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)、[管理](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)。

### <a name="dr-strategy"></a>DR 戦略
サービス レベルの目標が、BLOB ストレージへの簡単なバックアップとインプレースまたは新規 VM への復元の長い復旧時間に対応することである場合、それは最もシンプルでコストのかからない DR 戦略です。  
HANA DR が通常 HSR で実行される大規模なインスタンス プラットフォームと同様に、Azure VM では、HSR は最も自然でネイティブな SAP HANA DR ソリューションでもあります。  移行元デプロイが、単一インスタンスか、自動フェールオーバーが有効または無効なクラスターか、またはマルチノードのスケールアウト HANA かにかかわらず、DR リージョンには移行元インフラストラクチャのターゲット HSR レプリカが必要です。 この HSR ターゲット DR レプリカは、プライマリ HLI から VM への移行が完了した後で確立されます。  DR HANA インスタンスは、セカンダリ レプリケーション サイトとして、VM インスタンス上のプライマリ SAP HANA に登録されます。  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP アプリケーション サーバーの接続先の変更
HSR 移行アプローチにより、新しい HANA DB ホストが作成され、したがってアプリケーション層の新しい DB ホスト名が作成されるので、新しいホスト名を反映するように SAP プロファイルを変更する必要があります。  ホスト名を維持する名前解決によって切り替えが行われる場合は、プロファイルを変更する必要はありません。

### <a name="operating-system"></a>オペレーティング システム
HLI と VM のオペレーティング システム イメージは、同じリリース レベルの場合でも (SLES 11 SP4 など)、同一ではありません。 お客様は、HLI で必要なパッケージ、修正プログラム、パッチ、カーネル、セキュリティ修正プログラムを検証して、ターゲット VM のオペレーティング システムにそれらをインストールできるようにする必要があります。  さらに、お客様は、SAP HSR 用のターゲット VM に、より新しい OS バージョンを構成するのが一般的です。  それは、[SAP ノート 2763388](https://launchpad.support.sap.com/#/notes/2763388) に従ってサポートされます。

### <a name="new-sap-license-request"></a>新しい SAP ライセンスの要求
VM に移行されたので、新しい HANA システムの新しい SAP ライセンスを要求する必要があることについての簡単な注意です。

### <a name="service-level-agreement-sla-differences"></a>サービス レベル アグリーメント (SLA) の違い
HLI と Azure VM の間の可用性 SLA の違いについての注意です。  たとえば、クラスター化された HLI HA ペアの可用性は 99.99% です。 同じ SLA を実現するには、可用性ゾーンに VM をデプロイする必要があります。 こちらの[記事](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)では関連するデプロイ アーキテクチャでの可用性について説明されており、お客様はそれに応じてターゲット インフラストラクチャを計画できます。


## <a name="migration-strategy"></a>移行戦略
このドキュメントでは、HLI から Azure VM への移行に対する HANA システム レプリケーション アプローチについてのみ説明します。  デプロイするターゲット ストレージ ソリューションによっては、プロセスが若干異なります。 以下ではおおまかな手順を説明します。

### <a name="vm-with-premiumultra-disks-for-data"></a>データ用に Premium または Ultra ディスクを使用する VM
Premium または Ultra ディスクを使用してデプロイされた VM の場合、標準の SAP HANA システム レプリケーション構成が適用され、HSR のセットアップに使用できます。  参照されている [SAP ヘルプ記事](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)では、2 つのシステム間のシステム レプリケーションの設定、セカンダリ システムへのフェール オーバー、プライマリ システムへのフェールバック、システム レプリケーションの無効化に関する手順の概要が説明されています。  移行のために必要なのは、HLI ソースの手順のうち、システム レプリケーションの設定、引き継ぎ、無効化だけです。  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>データとログボリュームに ANF を使用する VM
大まかに言えば、完全なデータ ボリュームとログ ボリュームの最新の HLI ストレージ スナップショットを、ターゲットの HANA VM によってアクセス可能で復旧可能な Azure Storage にコピーする必要があります。  コピー処理は、任意のネイティブ Linux コピー ツールで実行できます。  

>[!Important]
> HANA データベースのサイズとネットワーク帯域幅によっては、コピーとデータ転送に時間がかかることがあります。  コピー処理の大部分は、プライマリ HANA DB のダウンタイムより前に行っておく必要があります。

### <a name="mcos-to-mdc-conversion"></a>MCOS から MDC への変換
HLI の一部のお客様は、1 つのシステムに複数のコンポーネントが含まれる (MCOS) デプロイ モデルを選択しています。  それは、SAP HANA の初期のバージョンにあった複数データベース コンテナー (MDC) のストレージ スナップショットに関する制限を回避するためでした。  MCOS モデルでは、複数の独立した SAP HANA インスタンスが、1 つの HLI ブレードに積み重ねられます。  移行に HSR を使用すると、それぞれが 1 つのテナント DB を備える複数の HANA VM になります。  この最終状態は、お客様が慣れているものよりビジーな状況になります。  MDC を使用する SAP HANA 2.0 の既定のデプロイでの有効な代替策は、HSR の移行の後で [HANA テナントの移動](https://launchpad.support.sap.com/#/notes/2472478)を実行することです。  このプロセスにより、これらの独立した HANA データベースが、1 つの HANA コンテナー内の共同テナントに統合されます。  

### <a name="application-layer-consideration"></a>アプリケーション層についての考慮事項
DB サーバーが SAP システムの中心と見なされます。  すべてのアプリケーション サーバーを、SAP HANA DB の近くに配置する必要があります。  PPG を新しく使用するのが望ましい場合は、HANA VM が存在する PPG への既存アプリケーション サーバーの再配置が必要になる場合があります。  既に展開テンプレートを使用している場合は、新しいアプリケーション サーバーの構築が簡単になる可能性があります。  
既存のアプリケーション サーバーと新しい HANA VM を理想的に配置した場合、追加容量が必要でない限り、新しいアプリケーション サーバーを構築する必要はありません。  
サービスの可用性を高めるために新しいインフラストラクチャを構築する場合は、既存のアプリケーション サーバーが不要になり、シャットダウンして削除することが必要になる可能性があります。
ターゲット VM のホスト名を変更し、HLI のホスト名と異なる場合は、新しいホストを参照するように、SAP アプリケーション サーバーのプロファイルを調整する必要があります。  HANA DB の IP アドレスのみを変更した場合は、着信接続を新しい HANA VM に誘導するために、DNS レコードの更新が必要になります。

### <a name="acceptance-test"></a>受け入れテスト
異種の移行とは異なり、HLI から VM への移行では、データベースの内容に対する重要な変更はありませんが、新しいセットアップの主要な機能とパフォーマンスの面を検証することをお勧めします。  

### <a name="cutover-plan"></a>切り替え計画
この移行は簡単なものですが、既存の DB の使用停止が関係します。  フォールバックが必要な場合のため、ソース システムとそれに関連するコンテンツおよびバックアップ イメージが保持されるように慎重に計画することが不可欠です。  適切に計画すれば、より迅速に元に戻すことができます。   


## <a name="post-migration"></a>移行後の処理
データの整合性を保証するため、HLI に依存するサービスまたは接続を安全に分離するまでは、移行ジョブを行いません。  また、不要なサービスをシャットダウンします。  ここでは、いくつかの重要な項目について説明します。

### <a name="decommissioning-the-hli"></a>HLI の使用停止
HANA DB から Azure VM への移行が成功した後、HLI DB で運用ビジネス トランザクションが実行されることはないはずです。  ただし、ローカル バックアップ データの保持期間に合わせて HLI を稼働状態にしておくことは、必要に応じてデータを迅速に復旧するための安全な方法です。  その後でのみ、HLI ブレードの使用を停止する必要があります。  技術的な取り組みに加えて、お客様にとって最もよいのは、Microsoft と契約して HLI のコミットメントを終了することです。  Microsoft の担当者に連絡し、HLI 使用停止プロセスを進める必要があります。

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>HLI 用に構成されたプロキシを削除する (例: Iptable、BIGIP) 
IPTable のようなプロキシ サービスを使用して HLI との間のオンプレミス トラフィックをルーティングしている場合、VM への移行が成功した後、そのサービスは不要になります。  ただし、使用停止のトピックで説明されているように、HLI ブレードがまだスタンバイになっている限り、この接続サービスを保持する必要があります。  HLI ブレードの使用を完全に停止した後で、このサービスをシャットダウンできます。 

### <a name="remove-global-reach-for-hli"></a>HLI の Global Reach の削除 
Global Reach は、通常、お客様の ExpressRoute ゲートウェイを HLI ExpressRoute ゲートウェイと接続し、お客様のオンプレミスのトラフィックが、プロキシサービスを必要とせずに、HLI テナントに直接到達できるようにするために使用されます。  IPTable プロキシ サービスの場合と同様に、HLI ブレードの使用を完全に使用するまで、Global Reach を保持する必要があります。

### <a name="operating-system-subscription--movereuse"></a>オペレーティング システムのサブスクリプション - 移動と再利用
VM サーバーを稼働させ、HLI ブレードの使用を停止したら、OS ライセンスの二重払いを防ぐため、OS サブスクリプションを置き換えたり再利用したりすることができます。



## <a name="next-steps"></a>次のステップ
次の記事を参照してください。
- [Azure における SAP HANA インフラストラクチャの構成と運用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure での SAP ワークロードの計画とデプロイに関するチェックリスト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)。
