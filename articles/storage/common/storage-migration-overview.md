---
title: Azure Storage 移行ガイド
description: Storage の移行の概要ガイドでは、ストレージ移行の基本的なガイダンスについて説明します
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: 2d02399daf6a7ea56c41f936544697f9df91a62c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601275"
---
# <a name="azure-storage-migration-overview"></a>Azure Storage の移行の概要

この記事では、Azure へのストレージの移行に焦点を当て、次のストレージ移行シナリオに関するガイダンスを提供します。

- ファイルやオブジェクトなどの非構造化データの移行
- ディスクや記憶域ネットワーク (SAN) などのブロックベース デバイスの移行

## <a name="migration-of-unstructured-data"></a>非構造化データの移行

非構造化データの移行には、次のシナリオが含まれます。

- ネットワーク接続ストレージ (NAS) から Azure ファイル オファリングへのファイルの移行:
  - [Azure Files](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [独立系ソフトウェア ベンダー (ISV) ソリューション](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md)。
- オブジェクト ストレージ ソリューションから Azure オブジェクト ストレージ プラットフォームへのオブジェクトの移行:
  - [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)。

### <a name="migration-phases"></a>移行フェーズ

完全な移行は、検出、評価、移行といういくつかの異なるフェーズで構成されています。

| 探索 | 評価 | 移行 |
| --------- | ---------- | --------- |
| - 移行するソースを検出する | - 該当する対象のサービスを評価する <br> - 技術的およびコストに関する考慮事項 | - 初期移行 <br> - 再同期 <br> - 最終的な切り替え |

#### <a name="discovery-phase"></a>検出フェーズ

検出フェーズでは、SMB 共有、NFS エクスポート、またはオブジェクト名前空間など、移行が必要なすべてのソースを決定します。 このフェーズは手動で行うことも、自動ツールを使用することもできます。

#### <a name="assessment-phase"></a>評価フェーズ

評価フェーズは、移行に使用できるオプションを理解する上で重要です。 移行中のリスクを軽減し、一般的な落とし穴を回避するには、こちらの 3 つの手順を実行します。

| 評価フェーズの手順                     | オプション                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **ターゲット ストレージ サービスを選択する**            | - Azure Blob Storage および Data Lake Storage<br>- Azure Files<br>- Azure NetApp Files<br>- ISV ソリューション |
| **移行方式を選択する**                  | - オンライン<br>- オフライン<br> - 両方の組み合わせ                                  |
| **ジョブに最適な移行ツールを選択する** | - 商用ツール (Azure および ISV)<br> - オープンソース

評価フェーズに役立つ商用 (ISV) ツールがいくつかあります。 [比較表](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)を参照してください。

##### <a name="choose-a-target-storage-service"></a>ターゲット ストレージ サービスを選択する

ターゲット ストレージ サービスの選択は、データにアクセスするアプリケーションまたはユーザーによって異なります。 正しい選択は、技術と財務面の両方によって決まります。 まず、技術評価を行って、考えられるターゲットを評価し、要件を満たすサービスを特定します。 次に、財務評価を行って最適な選択肢を判断します。

移行するターゲット ストレージ サービスを選択するには、各サービスの次の側面を評価します。

- プロトコルのサポート
- パフォーマンス特性
- ターゲット ストレージ サービスの制限

次の図は、推奨される Azure ファイル サービスを見つけるのに役立つ単純なデシジョン ツリーです。 ネイティブの Azure サービスで要件が満たされない場合は、さまざまな[独立系ソフトウェア ベンダー (ISV) ソリューション](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md)があります。

技術評価を完了し、適切なターゲットを選択したら、コスト評価を行って、最もコスト効率の高いオプションを決定します。

![正しいファイル サービスを選択するための基本的なデシジョン ツリー](./media/storage-migration-overview/files-decision-tree.png)

デシジョン ツリーを単純にするために、ターゲット ストレージ サービスの制限はこの図に組み込まれていません。 現在の制限の詳細を確認し、それらに基づいて選択内容を変更する必要があるかどうかを判断するには、次を参照してください。

- [ストレージ アカウントの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)
- [Blob Storage の制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-blob-storage-limits)
- [Azure Files のスケーラビリティおよびパフォーマンスのターゲット](../files/storage-files-scale-targets.md)
- [Azure NetApp Files のリソース制限](../../azure-netapp-files/azure-netapp-files-resource-limits.md)

これらの制限のいずれかによってサービスの使用がブロックされる場合、Azure では、Azure Marketplace でソリューションを提供する複数のストレージ ベンダーがサポートされています。 ファイル サービスを提供する検証済みの ISV パートナーの詳細については、「[プライマリおよびセカンダリ ストレージの Azure Storage パートナー](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md)」を参照してください。

##### <a name="select-the-migration-method"></a>移行方式を選択する

ストレージの移行には、2 つの基本的な移行方式があります。

- **オンライン**。 オンライン方式では、データの移行にネットワークを使用します。 パブリック インターネットまたは [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) を使用できます。 サービスにパブリック エンドポイントがない場合は、パブリック インターネットで VPN を使用する必要があります。
- **オフライン**。 オフライン方式では、[Azure Data Box](https://azure.microsoft.com/services/databox/) デバイスのいずれかを使用します。

オンライン方式とオフライン方式のどちらを使用するかは、使用可能なネットワーク帯域幅によって決まります。 オンライン方式は、必要なタイムライン内に移行を実行するのに十分なネットワーク帯域幅がある場合に推奨されます。

最初の一括移行にはオフライン方式、変更の増分移行にはオンライン方式を使用するというように、両方の方式を組み合わせて使用することも可能です。 両方の方式を同時に使用することは、高度な調整が必要となるため、推奨されません。 両方の方式を使用する場合は、オンラインで移行されるデータ セットと、オフラインで移行されるデータ セットを分離します。

さまざまな移行方式とガイドラインの詳細については、「[データ転送用の Azure ソリューションを選択する](./storage-choose-data-transfer-solution.md)」と「[Azure ファイル共有への移行](../files/storage-files-migration-overview.md)」を参照してください。

##### <a name="choose-the-best-migration-tool-for-the-job"></a>ジョブに最適な移行ツールを選択する

移行を実行するために使用できるさまざまな移行ツールがあります。 AzCopy、robocopy、xcopy、rsync などのオープンソースのものもあれば、商用のものもあります。 使用可能な商用ツールとそれらの比較一覧は、[比較表](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)で確認できます。

オープンソース ツールは、小規模な移行に適しています。 Windows ファイル サーバーから Azure Files へ移行する場合、Microsoft では、Azure Files のネイティブ機能から開始し、[Azure File Sync](/windows-server/manage/windows-admin-center/azure/azure-file-sync) を使用することをお勧めしています。さまざまなソース、大容量、または帯域幅調整や監査機能を使用した詳細なレポートなどの特殊な要件から成る、より複雑な移行の場合は、商用ツールを選択することをお勧めします。 これらのツールを使用することで、移行が容易になり、リスクが大幅に軽減されます。 ほとんどの商用ツールでは検出も実行できます。これにより、評価に役立つ入力が提供されます。

#### <a name="migration-phase"></a>移行フェーズ

移行フェーズは、データの移動と移行を行う最終移行手順です。 通常は、より容易な切り替えを実現するために、移行フェーズを何度か実行します。 移行フェーズは、次の手順で構成されます。

1. **最初の移行**。 最初の移行手順では、ソースからターゲットにすべてのデータを移行します。 この手順で、移行する必要があるデータの大部分が移行されます。
2. **再同期**。 再同期操作では、最初の移行手順後に変更されたすべてのデータを移行します。 多数の変更がある場合は、この手順を複数回繰り返すことができます。 再同期操作を複数回実行する目的は、最終手順にかかる時間を短縮することです。 非アクティブなデータや、変更されていないデータ (バックアップやアーカイブ データなど) の場合は、この手順を省略できます。
3. **最終的な切り替え**。 最終的な切り替え手順では、データのアクティブ使用をソースからターゲットに切り替えて、ソースを廃止します。

非構造化データの移行期間は、いくつかの側面によって異なります。 選択した方式以外で最も重要な要因は、データの合計サイズとファイル サイズの分布です。 データ セットの合計が大きいほど、移行時間は長くなります。 平均ファイル サイズが小さいほど、移行時間は長くなります。 多数の小さいファイルがある場合は、移行の合計時間を減らすために、必要に応じてそれらを大きいファイル (.tar や .zip ファイルなど) にアーカイブすることを検討してください。

## <a name="migration-of-block-based-devices"></a>ブロックベース デバイスの移行

ブロックベース デバイスの移行は、通常、仮想マシンまたは物理ホストの移行の一部として行われます。 ブロック ストレージの決定を移行後まで遅らせるのは、よくある誤解です。 ワークロード要件を適切に考慮してこれらの決定を事前に行うことで、クラウドへの移行がスムーズになります。

移行するワークロードと実行方法について調べるには、[Azure Disk Storage に関するドキュメント](../../virtual-machines/disks-types.md)と、[Disk Storage の製品ページ](https://azure.microsoft.com/services/storage/disks/#resources)にあるリソースを参照してください。 要件に適したディスクと、[ディスク バースト](../../virtual-machines/disk-bursting.md)などの最新機能について確認できます。 ブロック ベースのデバイスの移行は、次の 2 つの方法で行うことができます。
- 基になるブロックベース デバイスと共に仮想マシン全体を移行する方法については、「[Azure Migrate のドキュメント](../../migrate/index.yml)」をご覧ください
- ブロック ベースのデバイスのみを移行する場合や、複雑なユース ケースの場合は、[Cirrus Migrate Cloud](../solution-integration/validated-partners/data-management/cirrus-data-migration-guide.md) を使用します。

## <a name="see-also"></a>関連項目

- [データ転送用の Azure ソリューションを選択する](./storage-choose-data-transfer-solution.md)
- [商用移行ツールの比較](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Azure ファイル共有への移行](../files/storage-files-migration-overview.md)
- [WANdisco LiveData Platform for Azure を使用して Data Lake Storage に移行する](../blobs/migrate-gen2-wandisco-live-data-platform.md)
- [AzCopy を使用して Azure Storage にデータをコピーまたは移動する](./storage-use-azcopy-v10.md)
- [AzReplicate を使用して大規模なデータセットを Azure Blob Storage に移行する](/samples/azure/azreplicate/azreplicate/)
