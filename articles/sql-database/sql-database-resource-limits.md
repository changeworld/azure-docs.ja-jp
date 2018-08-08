---
title: Azure SQL Database のリソース制限の概要 | Microsoft Docs
description: このページでは、Azure SQL Database のシングルトンに対するいくつかの一般的な DTU ベースのリソース制限について説明します。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 6f6fa1ebc086530f138d32ee5a9c799b5bfbbdeb
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412112"
---
# <a name="overview-azure-sql-database-resource-limits"></a>Azure SQL Database のリソース制限の概要 

この記事では、Azure SQL Database のリソース制限の概要について説明し、それらのリソース制限への到達時または超過時の動作に関する情報を提供を提供します。

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>サーバーとデータベースの最大数

| 最大値 | 値 |
| :--- | :--- |
| サーバーあたりのデータベース数 | 5000 |
| 任意のリージョンにおけるサブスクリプションあたりの既定のサーバー数 | 20 |
| 任意のリージョンにおけるサブスクリプションあたりの最大サーバー数 | 200 |
| サーバーあたりの DTU/eDTU クォータ | 54,000 |
| サーバーあたりの仮想コア クォータ | 540 |
| サーバーあたりの最大プール | DTU または仮想コアの数によって制限される |
|||

> [!NOTE]
> 既定量よりも多い DTU/eDTU クオータ、仮想コア クラスター、またはサーバーを取得する場合は、Azure portal で、目的のサブスクリプションに対して、発行の種類を [クオータ] として新しいサポート要求を送信できます。 サーバーあたりの DTU/eDTU クオータとデータベース制限には、サーバーあたりのエラスティック プールの数が含まれます。 

> [!IMPORTANT]
> データベースの数がサーバーあたりの制限に近づくと、次の状況が発生します。
> - マスター データベースに対して実行するクエリの待機時間が増えます。  これには、リソース使用率統計情報のビューも含まれます (sys.resource_stats など)。
> - サーバー内のデータベースの列挙を要する、管理操作やポータル ビュー ポイント表示の待機時間が長くなります。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>データベース リソースが制限に達したときの影響

### <a name="compute-dtus-and-edtus--vcores"></a>コンピューティング (DTU と eDTU/vCores)

(DTU と、eDTU または vCores によって測定された) データベース コンピューティングの使用率が高くなると、クエリの待機時間が増加し、タイムアウトすることさえあります。このような状況下では、クエリはサービスによってキューに格納されることがあり、リソースが空くと実行用のリソースを提供されます。
高いコンピューティング使用率が発生する場合、次のような軽減オプションがあります。

- データベースまたエラスティック プールのパフォーマンス レベルを上げて、より多くのコンピューティング リソースをデータベースに提供します。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- クエリを最適化して、各クエリのリソース使用率を引き下げます。 詳しくは、「[クエリの調整とヒント](sql-database-performance-guidance.md#query-tuning-and-hinting)」をご覧ください。

### <a name="storage"></a>Storage

使用済みのデータベース容量が最大サイズの上限に達すると、データのサイズが増えるデータベースの挿入および更新は失敗し、クライアントは[エラー メッセージ](sql-database-develop-error-messages.md)を受け取ります。 データベースの SELECTS と DELETES は引き続き成功します。

高い容量使用率が発生する場合、次のような軽減オプションがあります。

- データベースまたはエラスティック プールの最大サイズを増やすか、より多くの記憶域を追加します。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- データベースがエラスティック プール内にある場合は、もう 1 つの方法として、データベースをプールの外に移動し、ストレージ領域が他のデータベースと共有されないようにすることもできます。
- 未使用領域を再利用できるようにデータベースを縮小します。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="sessions-and-workers-requests"></a>セッションとワーカー (要求) 

セッションおよびワーカーの最大数は、サービス レベルとパフォーマンス レベル (DTU と eDTU) によって決まります。 セッションまたはワーカーが上限に達した場合、新しい要求は拒否され、クライアントはエラー メッセージを受け取ります。 利用可能な接続の数はアプリケーションで制御できますが、同時ワーカーの数は推定または制御が困難なことがよくあります。 データベース リソースが上限に達し、クエリを長時間実行したためにワーカーが滞留するピーク負荷期間は特にそうです。 

セッションまたはワーカーの使用率が高い場合は、次のような軽減オプションがあります。
- データベースまたはエラスティック プールのサービス レベルまたはパフォーマンス レベルを高くします。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- ワーカー使用率上昇の原因がコンピューティング リソースの競合である場合は、クエリを最適化して各クエリのリソース使用率を下げます。 詳しくは、「[クエリの調整とヒント](sql-database-performance-guidance.md#query-tuning-and-hinting)」をご覧ください。

## <a name="next-steps"></a>次の手順

- よく寄せられる質問の回答については、「[SQL Database に関する FAQ](sql-database-faq.md)」を参照してください。
- Azure の一般的な制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。
- DTU と eDTU については、「[データベース トランザクション ユニット (DTU) とエラスティック データベース トランザクション ユニット (eDTU) の説明](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)」をご覧ください。
- tempdb のサイズ制限については、https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database を参照してください。
