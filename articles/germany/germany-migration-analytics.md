---
title: Azure 分析リソースを移行する、Azure Germany からグローバル Azure
description: この記事では、Azure Germany からグローバル Azure への Azure 分析リソースの移行に関する情報を提供します。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 93630f536127971e922ddceaf5d1e5fa82ea3ad4
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "117029118"
---
# <a name="migrate-analytics-resources-to-global-azure"></a>分析リソースをグローバル Azure に移行する

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]


この記事には、Azure Germany からグローバル Azure への Azure 分析リソースの移行に役立つ情報が含まれています。
  
## <a name="event-hubs"></a>Event Hubs

Azure Germany からグローバル Azure に Azure Event Hubs リソースを直接移行することはできません。 Event Hubs サービスには、データのエクスポート機能またはインポート機能がありません。 ただし、Event Hubs リソースを[テンプレートとして](../azure-resource-manager/templates/export-template-portal.md)エクスポートすることはできます。 エクスポートしたテンプレートをグローバル Azure に合わせて調整し、リソースを再作成します。

> [!NOTE]
> Event Hubs のテンプレートをエクスポートしてもデータ (メッセージなど) はコピーされません。 テンプレートをエクスポートした場合、Event Hubs メタデータの再作成のみが行われます。

> [!IMPORTANT]
> 新しいリージョンと一貫性を維持できるように、場所、Azure Key Vault シークレット、証明書、およびその他の GUID を変更します。

### <a name="event-hubs-metadata"></a>Event Hubs のメタデータ

Event Hubs テンプレートをエクスポートすると、次のメタデータ要素が再作成されます。

- 名前空間
- Event Hubs
- コンシューマー グループ
- 承認規則

詳細情報:

- [Event Hubs の概要](../event-hubs/event-hubs-about.md)を確認してください。
- [Event Hubs のチュートリアル](../event-hubs/index.yml)を完了して、新しい知識を得ましょう。
- [Azure Service Bus](./germany-migration-integration.md#service-bus) 用の移行手順を確認してください。
- [Azure Resource Manager テンプレートをエクスポート](../azure-resource-manager/templates/export-template-portal.md)する方法についてよく理解する、または [Azure Resource Manager](../azure-resource-manager/management/overview.md) の概要を参照してください。

## <a name="hdinsight"></a>HDInsight

Azure HDInsight クラスターを Azure Germany からグローバル Azure に移行するには: 

1. HDInsight クラスターを停止します。
2. AzCopy または同様のツールを使用して、Azure Storage アカウント内のデータを新しいリージョンに移行します。
3. グローバル Azure 内で新しいコンピューティング リソースを作成してから、移行されたストレージ リソースをプライマリ接続ストレージとしてアタッチします。

実行時間の長い特殊なクラスター (Kafka、Spark ストリーミング、Storm、または HBase) については、新しいリージョンへのワークロードの切り替えを調整することをお勧めします。

詳細情報:

- [Azure HDInsight のドキュメント](../hdinsight/index.yml)を確認してください。
- [HDInsight のチュートリアル](../hdinsight/index.yml)を完了して、新しい知識を得ましょう。
- [HDInsight クラスターのスケーリング](../hdinsight/hdinsight-administer-use-powershell.md#scale-clusters)のヘルプについては、[PowerShell を使用した HDInsight の管理](../hdinsight/hdinsight-administer-use-powershell.md)に関するページを参照してください。
- [AzCopy](../storage/common/storage-use-azcopy-v10.md) を使用する方法を学習します。

## <a name="stream-analytics"></a>Stream Analytics

Azure Stream Analytics サービスを Azure Germany からグローバル Azure に移行するには、Azure portal または PowerShell のいずれかを使用して、グローバル Azure リージョン内の設定全体を手動で再作成します。 Stream Analytics ジョブ用のイングレス ソースとエグレス ソースは、どのリージョンに存在してもかまいません。

詳細情報:

- [Stream Analytics のチュートリアル](../stream-analytics/stream-analytics-real-time-fraud-detection.md)を完了して、新しい知識を得ましょう。
- [Stream Analytics の概要](../stream-analytics/stream-analytics-introduction.md)を確認してください。
- [PowerShell を使用して Stream Analytics ジョブを作成](../stream-analytics/stream-analytics-quick-create-powershell.md)する方法を学習してください。

## <a name="sql-database"></a>SQL Database

より小さな Azure SQL Database ワークロードを移行するには、エクスポート関数を使用して BACPAC ファイルを作成します。 BACPAC ファイルは、メタデータと SQL Server データベースからのデータを含む圧縮 (zip 形式) ファイルです。 BACPAC ファイルを作成した後では、(たとえば、AzCopy を使用して) ターゲット環境にファイルをコピーし、インポート関数を使用してデータベースを再構築することができます。 次の考慮事項に注意してください。

- エクスポートでトランザクション上の一貫性が保たれるようにするには、次のいずれかの条件に当てはまることを確認します。
  - エクスポート中に、書き込み操作は行われません。
  - ご利用の SQL データベースのトランザクション上一貫性のあるコピーからエクスポートします。
- Azure Blob ストレージにエクスポートする場合、BACPAC ファイルのサイズは最大 200 GB に制限されています。 これより大きな BACPAC ファイルについては、ローカル ストレージにエクスポートします。
- SQL Database からのエクスポート操作にかかる時間が 20 時間を超える場合、操作はキャンセルされる可能性があります。 パフォーマンスを向上する方法のヒントについては、次の記事を確認してください。

> [!NOTE]
> エクスポート中にサーバーの DNS 名が変更されるため、接続文字列はエクスポート操作の後に変更されます。

詳細情報:

- [BACPAC ファイルにデータベースをエクスポート](../azure-sql/database/database-export.md)する方法を学習してください。
- [BACPAC ファイルをデータベースにインポート](../azure-sql/database/database-import.md)する方法を学習してください。
- [Azure SQL Database のドキュメント](/azure/sql-database/)を確認してください。

## <a name="analysis-services"></a>Analysis Services

Azure Analysis Services モデルを Azure Germany からグローバル Azure に移行するには、[バックアップ操作と復元操作](../analysis-services/analysis-services-backup.md)を使用します。

モデルのメタデータのみを移行し、データを移行の対象としない場合、別の方法として [SQL Server Data Tools からモデルを再デプロイ](../analysis-services/analysis-services-deploy.md)することができます。

詳細情報:

- [Analysis Services のバックアップと復元](../analysis-services/analysis-services-backup.md)について学習してください。
- [Azure Analysis Services の概要](../analysis-services/analysis-services-overview.md)を確認してください。

## <a name="next-steps"></a>次のステップ

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)