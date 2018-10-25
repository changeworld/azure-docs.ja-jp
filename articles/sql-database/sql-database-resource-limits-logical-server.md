---
title: Azure SQL Database のリソース制限 - 論理サーバー | Microsoft Docs
description: この記事では、単一データベースとエラスティック プールを使用してプールされたデータベースに関する Azure SQL Database リソース制限の概要を示します。 また、これらのリソース制限に達した場合、または制限を超えた場合の動作に関する情報も提供します。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: b48c090cc67d4557140b5734f1a5e1f763b271ab
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829565"
---
# <a name="sql-database-resource-limits-for-single-and-pooled-databases-on-a-logical-server"></a>論理サーバー上の単一データベースとプールされたデータベースに関する SQL Database のリソース制限 

この記事では、論理サーバー上の単一データベースとプールされたデータベースに関する SQL Database リソース制限の概要を示します。 また、これらのリソース制限に達した場合、または制限を超えた場合の動作に関する情報も提供します。

> [!NOTE]
> Managed Instance の制限については、[マネージド インスタンスに関する SQL Database のリソース制限](sql-database-managed-instance-resource-limits.md)に関するページを参照してください。

## <a name="maximum-resource-limits"></a>最大リソース制限

| リソース | 制限 |
| :--- | :--- |
| サーバーあたりのデータベース数 | 5000 |
| 任意のリージョンにおけるサブスクリプションあたりの既定のサーバー数 | 20 |
| 任意のリージョンにおけるサブスクリプションあたりの最大サーバー数 | 200 |  
| サーバーあたりの DTU/eDTU クォータ | 54,000 |  
| サーバー/インスタンスあたりの仮想コア クォータ | 540 |
| サーバーあたりの最大プール | DTU または仮想コアの数によって制限される |
||||

> [!NOTE]
> 既定量よりも多い DTU/eDTU クオータ、仮想コア クラスター、またはサーバーを取得する場合は、Azure portal で、目的のサブスクリプションに対して、発行の種類を [クオータ] として新しいサポート要求を送信できます。 サーバーあたりの DTU/eDTU クオータとデータベース制限には、サーバーあたりのエラスティック プールの数が含まれます。 

> [!IMPORTANT]
> データベースの数が論理サーバーあたりの制限に近づくと、次の状況が発生する可能性があります。
> - マスター データベースに対して実行するクエリの待機時間が増えます。  これには、リソース使用率統計情報のビューも含まれます (sys.resource_stats など)。
> - サーバー内のデータベースの列挙を要する、管理操作やポータル ビュー ポイント表示の待機時間が長くなります。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>データベース リソースが制限に達したときの影響

### <a name="compute-dtus-and-edtus--vcores"></a>コンピューティング (DTU と eDTU/vCores)

(DTU と、eDTU または vCores によって測定された) データベース コンピューティングの使用率が高くなると、クエリの待機時間が増加し、タイムアウトすることさえあります。このような状況下では、クエリはサービスによってキューに格納されることがあり、リソースが空くと実行用のリソースを提供されます。
高いコンピューティング使用率が発生する場合、次のような軽減オプションがあります。

- データベースまたエラスティック プールのコンピューティング サイズを上げて、より多くのコンピューティング リソースをデータベースに提供します。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- クエリを最適化して、各クエリのリソース使用率を引き下げます。 詳しくは、「[クエリの調整とヒント](sql-database-performance-guidance.md#query-tuning-and-hinting)」をご覧ください。

### <a name="storage"></a>Storage

使用済みのデータベース容量が最大サイズの上限に達すると、データのサイズが増えるデータベースの挿入および更新は失敗し、クライアントは[エラー メッセージ](sql-database-develop-error-messages.md)を受け取ります。 データベースの SELECTS と DELETES は引き続き成功します。

高い容量使用率が発生する場合、次のような軽減オプションがあります。

- データベースまたはエラスティック プールの最大サイズを増やすか、より多くの記憶域を追加します。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- データベースがエラスティック プール内にある場合は、もう 1 つの方法として、データベースをプールの外に移動し、ストレージ領域が他のデータベースと共有されないようにすることもできます。
- 未使用領域を再利用できるようにデータベースを縮小します。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="sessions-and-workers-requests"></a>セッションとワーカー (要求) 

セッションおよびワーカーの最大数は、サービス レベルとコンピューティング サイズ (DTU と eDTU) によって決まります。 セッションまたはワーカーが上限に達した場合、新しい要求は拒否され、クライアントはエラー メッセージを受け取ります。 利用可能な接続の数はアプリケーションで制御できますが、同時ワーカーの数は推定または制御が困難なことがよくあります。 データベース リソースが上限に達し、クエリを長時間実行したためにワーカーが滞留するピーク負荷期間は特にそうです。 

セッションまたはワーカーの使用率が高い場合は、次のような軽減オプションがあります。
- データベースまたはエラスティック プールのサービス レベルまたはコンピューティング サイズを高くします。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- ワーカー使用率上昇の原因がコンピューティング リソースの競合である場合は、クエリを最適化して各クエリのリソース使用率を下げます。 詳しくは、「[クエリの調整とヒント](sql-database-performance-guidance.md#query-tuning-and-hinting)」をご覧ください。

## <a name="next-steps"></a>次の手順

- よく寄せられる質問の回答については、「[SQL Database に関する FAQ](sql-database-faq.md)」を参照してください。
- Azure の一般的な制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。
- DTU と eDTU については、「[データベース トランザクション ユニット (DTU) とエラスティック データベース トランザクション ユニット (eDTU) の説明](sql-database-service-tiers.md#dtu-based-purchasing-model)」をご覧ください。
- tempdb のサイズ制限については、「[SQL Database の Tempdb データベース](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)」をご覧ください。
