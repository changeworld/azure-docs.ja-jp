---
title: Azure Migrate Server Migration についてよく寄せられる質問
description: Azure Migrate Server Migration についてよく寄せられる質問の回答を示します。
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77065998"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server Migration:一般的な質問

この記事では、Azure Migrate についてよくある質問の回答を示します。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 この記事の内容についてさらに質問がある場合は、[Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)に投稿してください。 その他の質問については、次の記事を確認してください：

- Azure Migrate に関する[一般的な質問](resources-faq.md)。
- Azure Migrate アプライアンスに関する[一般的な質問](common-questions-appliance.md)。
- 検出、評価、依存関係の視覚化に関する[質問](common-questions-discovery-assessment.md)


## <a name="how-does-agentless-vmware-replication-work"></a>エージェントレス VMware レプリケーションどのようなしくみになっていますか?

VMware のエージェントレス レプリケーション方式では、VMware スナップショット、および VMware 変更ブロック追跡 (CBT) が使用されます。 初期レプリケーション サイクルは、ユーザーがレプリケーションを開始したときにスケジュールされます。 初期レプリケーション サイクルでは、VM のスナップショットが作成され、このスナップショットを使用して VM VMDK (ディスク) がレプリケートされます。 初期レプリケーション サイクルが完了すると、差分レプリケーション サイクルが定期的にスケジュールされます。 差分レプリケーション サイクルでは、スナップショットが作成され、前のレプリケーション サイクル以降に変更されたデータ ブロックがレプリケートされます。 VMware 変更ブロック追跡を使用して、最後のサイクル以降に変更されたブロックが特定されます。
定期的なレプリケーション サイクルの頻度は、同じデータストアから同時にレプリケートされている他の VM またはディスクの数に応じて、サービスによって自動的に管理されます。理想的な条件では、この頻度は最終的に VM ごとに 1 時間あたり 1 サイクルに収束されます。

移行時には、VM によって残りのすべてのデータがキャプチャされるように、オンデマンドのレプリケーション サイクルがスケジュールされます。 移行の一環として VM をシャットダウンして、データの損失をなくし、アプリケーションの整合性を確保することができます。

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>エージェントレス スタックで再同期オプションが公開されていないのはなぜですか?

エージェントレス スタックでは、すべての差分サイクルにおいて、現在のスナップショットと、前に取得したスナップショットとの差分を転送します。 これは常にスナップショット間の差分であるため、データの折りたたみの利点があります (つまり、スナップショットとスナップショットの間に特定のセクターが "n" 回書き込まれた場合、必要なのは最後の同期のみであるため、最後の書き込みを転送するだけで済みます)。 これは、すべての書き込みを追跡して適用するエージェントベースのスタックとは異なります。 つまり、すべての差分サイクルは再同期です。 そのため、再同期オプションは公開されていません。 

障害が原因でディスクが同期されていない場合は、次回のサイクルで修正されます。 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>エージェントレス レプリケーションを使用する場合、チャーン率にはどのような影響がありますか?

スタックが、折りたたまれているデータに依存しているため、このスタックでは、チャーン率よりもチャーン パターンが重要となります。 ファイルが繰り返し書き込まれるパターンは、あまり大きな影響を与えません。 ただし、セクターが 1 つおきに書き込まれるパターンでは、次回のサイクルでチャーンが高くなります。 転送するデータの量を最小限に抑えるため、次回のサイクルをスケジュールする前に、できるだけ多くのデータを折りたためるようにします。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>レプリケーション サイクルはどのくらいの頻度でスケジュールされますか?

次回のレプリケーション サイクルをスケジュールする数式は、次のとおりです。(前のサイクル時間 / 2) または 1 時間のうち、いずれか大きい方。 たとえば、VM で差分サイクルに 4 時間かかった場合、次回のサイクルは、次の 1 時間ではなく 2 時間後にスケジュールします。 これは、サイクルが IR の直後である場合は異なり、最初の差分サイクルをすぐにスケジュールします。

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>エージェントレス レプリケーションの使用中、vCenter Server または ESXi ホストのパフォーマンスにどのような影響がありますか?

エージェントレス レプリケーションではスナップショットが使用されるため、ストレージで IOP が使用され、お客様のストレージに IOP ヘッドルームが必要になります。 ストレージまたは IOP の制約付き環境でこのスタックを使用することはお勧めできません。

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>エージェントレス移行スタックでは、Azure Gen 2 VM への UEFI VM の移行はサポートされていますか?

いいえ。これらの VM を Gen 2 Azure VM に移行するには、Azure Site Recovery を使用する必要があります。 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>移行時に VM を Azure Availability Zones にピン留めすることはできますか?

いいえ、Azure Availability Zones はサポートされていません。

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>レプリケーション時に Azure Migrate で使用されるトランスポート プロトコルは何ですか?

Azure Migrate では、SSL 暗号化に Network Block Device (NBD) プロトコルが使用されます。

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>移行に必要な最低限の vCenter Server バージョンは何ですか?

vCenter Server 5.5 および VMware vSphere ESXi ホスト バージョン 5.5 以上が必要です。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>お客様は VM をアンマネージド ディスクに移行できますか?

いいえ。 Azure Migrate では、マネージド ディスク (Standard HDD、Premium SSD) への移行のみがサポートされています。

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>エージェントレス VMware スタックを使用して同時にレプリケートできる VM は何個ですか?

現在、お客様は vCenter Server ごとに 100 個の VM を同時に移行できます。 これは、10 個の VM のバッチで実行できます。




