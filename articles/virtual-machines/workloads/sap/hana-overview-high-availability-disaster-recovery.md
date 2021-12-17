---
title: SAP HANA on Azure (L インスタンス) の高可用性とディザスター リカバリー | Microsoft Docs
description: SAP HANA on Azure (Large Instances) の高可用性を確立し、ディザスター リカバリーを計画する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/8/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: df7d33a08b9d3eb868cecdec7753744782590b51
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111809870"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure での SAP HANA L インスタンスの高可用性とディザスター リカバリー 

>[!IMPORTANT]
>このドキュメントは、SAP HANA の管理ドキュメントまたは SAP Note に代わるものではありません。 SAP HANA の管理と操作 (特にバックアップ、復元、高可用性、ディザスター リカバリーのトピック) について、読者が専門知識を有していることを前提としています。

この記事では、SAP HANA on Azure Large Instances (BareMetal インフラストラクチャとも呼ばれます) の高可用性 (HA) とディザスター リカバリー (DR) について、概要を説明します。 また、HA と DR に関連した要件と考慮事項のいくつかについても詳しく説明します。

このドキュメントで説明するプロセスの一部は簡略化されています。 操作ハンドブックに含まれる詳細な手順としては意図されていません。 お使いの構成に対応した操作ハンドブックを作成するには、お使いの特定の HANA バージョンとリリースでプロセスを実行してテストします。 その後、お使いの構成に固有のプロセスを文書化できます。

## <a name="ha-and-dr"></a>HA と DR

高可用性とディザスター リカバリーは、ミッション クリティカルな SAP HANA on Azure (Large Instances) サーバーを実行する場合のきわめて重要な要素です。 適切な高可用性とディザスター リカバリー戦略をきちんと策定、実装するには、SAP、システム インテグレーター、Microsoft と協力することが重要です。 また、環境に固有の回復ポイントの目標 (RPO) と回復時刻の目標 (RTO) を検討します。

Microsoft では、HANA L インスタンスでいくつかの SAP HANA 高可用性手法をサポートしています。 次のような機能があります。

- **ストレージ レプリケーション**: すべてのデータを別の Azure リージョンの別の HANA L インスタンス スタンプにレプリケートする、ストレージ システムの機能です。 SAP HANA は、この手法と関係なく動作します。 この機能は、HANA L インスタンスに対して提供されている既定のディザスター リカバリー メカニズムです。
- **HANA システム レプリケーション**: 別の SAP HANA システムへの [SAP HANA の全データのレプリケーション](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)です。 一定間隔でのデータのレプリケーションによって、RTO が最小化されます。 SAP HANA では、非同期、メモリ内同期、および同期 の各モードがサポートされています。 同期モードは、同じデータ センター内または 100 km 未満の距離のデータ センター内の SAP HANA システムにのみ使用されます。 HANA L インスタンス スタンプの現在の設計では、HANA システム レプリケーションは 1 つのリージョンだけの高可用性を実現するために利用することができます。 HANA システム レプリケーションには、別の Azure リージョンへのディザスター リカバリー構成に対応するサード パーティのリバース プロキシ コンポーネントまたはルーティング コンポーネントが必要です。 
- **ホストの自動フェールオーバー**: SAP HANA が HANA システム レプリケーションの代わりに使用するローカル障害復旧ソリューションです。 プライマリ ノードが使用できなくなった場合、1 つ以上のスタンバイ SAP HANA ノードをスケールアウト モードで構成していれば、SAP HANA がスタンバイ ノードに自動的にフェールオーバーされます。

SAP HANA on Azure (Large Instances) は、4 つの地政学的地域 (米国、オーストラリア、ヨーロッパ、日本) を対象とする 2 つの Azure リージョンで提供されます。 HANA Large Instances (HLI) スタンプをホストする地政学的地域内の 2 つのリージョンは、個別の専用ネットワーク回線に接続されます。 これらの HLI は、ストレージのスナップショットをレプリケートしてディザスター リカバリーの手段を提供するために使用されます。 レプリケーションは既定では設定されず、ディザスター リカバリー機能を指定したお客様に対してのみ設定されます。 ストレージ レプリケーションは、HANA L インスタンスのストレージ スナップショットの使用に依存します。 別の地政学的領域内の Azure リージョンを DR リージョンとして選択することはできません。

## <a name="currently-supported-options"></a>現在サポートされているオプション

現在サポートされている高可用性とディザスター リカバリーの手段およびその組み合わせを次の表に示します。

| HANA L インスタンスでサポートされるシナリオ | 高可用性オプション | ディザスター リカバリー オプション | 説明 |
| --- | --- | --- | --- |
| 単一ノード | 使用できません。 | 専用 DR セットアップ。<br /> 多目的 DR セットアップ。 | |
| ホストの自動フェールオーバー: スケールアウト (スタンバイあり、またはなし)<br /> (1 + 1 を含む) | アクティブ ロールを担うスタンバイで可能。<br /> HANA がロールの切り替えを制御。 | 専用 DR セットアップ。<br /> 多目的 DR セットアップ。<br /> ストレージ レプリケーションを使用した DR 同期。 | HANA ボリューム セットはすべてのノードに接続。<br /> DR サイトに同じ数のノードが必要。 |
| HANA システム レプリケーション | プライマリまたはセカンダリ セットアップで可能。<br /> フェールオーバーが発生した場合にセカンダリがプライマリ ロールに移行。<br /> HANA システム レプリケーションと OS 制御のフェールオーバー。 | 専用 DR セットアップ。<br /> 多目的 DR セットアップ。<br /> ストレージ レプリケーションを使用した DR 同期。<br /> HANA システム レプリケーションを使用した DR はサード パーティのコンポーネントがないとまだ不可能。 | 各ノードに接続された個別のディスク ボリューム セット。<br /> DR の場所にレプリケートされるのは、実稼働サイトのセカンダリ レプリカのディスク ボリュームのみ。<br /> DR サイトにボリューム セットが 1 つ必要。 | 

専用 DR セットアップは、DR サイトの HANA Large Instance ユニットが他のワークロードや非実稼働システムの実行には使用されないセットアップです。 ユニットはパッシブであり、障害時のフェールオーバーが実行されたときにのみデプロイされます。 ほとんどのお客様には、このセットアップはお勧めできません。

お使いのアーキテクチャのストレージ レイアウトとイーサネットの詳細については、[HLI のサポートされるシナリオ](hana-supported-scenario.md)に関するページを参照してください。

> [!NOTE]
> オーバーレイ シナリオとしての [SAP HANA の MCOD デプロイ](https://launchpad.support.sap.com/#/notes/1681092) (1 つのユニットに複数の HANA Instances) は、表に一覧で示されている HA と DR の方法で機能します。 例外は、Pacemaker に基づく自動フェールオーバー クラスターを備えた HANA システム レプリケーションを使う場合です。 このような場合は、ユニットごとにサポートされる HANA インスタンスは 1 つのみです。 [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) デプロイでは、複数のテナントがデプロイされる場合、非ストレージ ベースの HA および DR の方法のみが機能します。 1 つのテナントがデプロイされる場合は、一覧のすべての方法が有効です。  

多目的 DR セットアップは、DR サイト上の HANA L インスタンス ユニットが非実稼働ワークロードを実行するセットアップです。 障害発生時には、非実稼働システムをシャットダウンし、ストレージでレプリケートされた (追加された) ボリューム セットをマウントしてから、HANA 実稼働インスタンスを起動します。 HANA L インスタンスのディザスター リカバリー機能を使用するお客様の多くが、この構成を使用しています。 

SAP HANA の高可用性の詳細については、SAP の次の記事を参照してください。 

- [SAP HANA の高可用性のホワイトペーパー](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 管理ガイド](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA システム レプリケーションに関する SAP HANA Academy ビデオ](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP サポート ノート #1999880 - SAP HANA システム レプリケーションに関する FAQ](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP サポート ノート #2165547 - SAP HANA システム レプリケーション環境での SAP HANA のバックアップと復元](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP サポート ノート #1984882 - 最小/ゼロ ダウンタイムでのハードウェア交換のための SAP HANA システム レプリケーションの使用](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>HANA L インスタンスでのディザスター リカバリーのネットワークに関する考慮事項

HANA L インスタンスのディザスター リカバリー機能を利用するには、2 つの Azure リージョンへのネットワーク接続を設計する必要があります。 また、オンプレミスからメイン Azure リージョンへの Azure ExpressRoute 回線接続のほか、オンプレミスからディザスター リカバリーを行うリージョンへの回線接続が必要になります。 この方法では、Microsoft Enterprise Edge Router (MSEE) の場所を含め、Azure リージョンで問題が発生した状況に対処できます。

また、1 つのリージョンの SAP HANA on Azure (Large Instances) に接続するすべての Azure 仮想ネットワークを、もう一方のリージョンの HANA Large Instances に接続する ExpressRoute 回線に接続することもできます。 この *相互接続* により、リージョン 1 の Azure Virtual Network で実行されているサービスがリージョン 2 の HANA L インスタンス ユニットに接続することができ、その逆も可能になります。 この方法では、オンプレミスの場所を Azure と接続する MSEE の場所のいずれか 1 つのみがオフラインになった場合に対処できます。

次の図では、ディザスター リカバリーの場合の回復性に優れた構成を示します。

![障害復旧に最適な構成](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)


## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>ディザスター リカバリーに HANA L インスタンスのストレージ レプリケーションを使用する際のその他の要件

- 製品 SKU と同じサイズの SAP HANA on Azure L インスタンス SKU を指定し、ディザスター リカバリー リージョンにデプロイする必要があります。 現在の顧客のデプロイでは、これらのインスタンスを使用して、非実稼働環境の HANA インスタンスを実行します。 これらの構成は、"*多目的 DR セットアップ*" と呼ばれます。   
- ディザスター リカバリー サイトで復旧する SAP HANA on Azure (Large Instances) SKU ごとに、DR サイトに追加のストレージを指定する必要があります。 追加のストレージを購入すると、ストレージ ボリュームを割り当てることができます。 運用 Azure リージョンからディザスター リカバリー Azure リージョンへのストレージ レプリケーションのターゲットとなるボリュームを割り当てることができます。
- DR サイトへのプライマリおよびストレージベースのレプリケーションに SAP HANA システム レプリケーションを設定することができます。 その後、プライマリとセカンダリの両方のノードのデータを DR サイトにレプリケートするために、DR サイトでさらに多くのストレージを購入する必要があります。

## <a name="next-steps"></a>次の手順

HANA Large Instances での SAP HANA のバックアップと復元の詳細について確認します。

> [!div class="nextstepaction"]
> [HANA Large Instances での SAP HANA のバックアップと復元](hana-backup-restore.md)
