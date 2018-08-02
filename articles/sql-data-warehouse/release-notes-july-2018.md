---
title: Azure SQL Data Warehouse リリース ノート 2018 年 6 月 | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216710"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Azure SQL Data Warehouse の新機能 2018 年 7 月
Azure SQL Data Warehouse では、継続的に機能強化を図っています。 この記事では、2018 年 7 月に導入された新しい機能と変更点について説明します。


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>リージョンやサーバーの垣根を越えた復元で、さらに細かい粒度を実現
24 時間おきに作成される geo 冗長バックアップを選択する代わりに、任意の復元ポイントを使い、リージョンやサーバーの垣根を越えて復元できるようになりました。 リージョンやサーバーの垣根を越えた復元は、ユーザー定義の復元ポイントと自動の復元ポイントのどちらでもサポートされるので、粒度が細かくなりデータ保護の強化につながります。 利用できる復元ポイントが増えることで、リージョンをまたいで復元する際にデータ ウェアハウスの論理的整合性が保たれることへの安心感が得られます。

![復元コマンド - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![復元オプション - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

詳細については、[高速で柔軟な復元ポイント](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)に関するブログ記事を参照してください。

## <a name="20-minute-restorations"></a>20 分での復元
SQL Data Warehouse であらゆるデータ ウェアハウスの復元にかかる時間が短縮され、同じリージョン内であれば、データベース サイズに関係なく **20 分未満**で復元できるようになりました。 同じリージョン内であれば、復元先の論理サーバーが同じであるかどうかに関係なく、この復元時間が当てはまります。 また、復元ポイントの作成にかかる時間を短縮するために、スナップショットの処理も改善されています。 低パフォーマンス レベル (低 DWU 設定) では、スナップショットの作成時間が "*1/2 に短縮*" されます。

詳細については、[高速で柔軟な復元ポイント](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)に関するブログ記事を参照してください。

## <a name="custom-restoration-configurations"></a>カスタム復元構成
復元時のパフォーマンス レベル (DWU) を Azure portal で変更できるようになりました。 アップグレードされた Gen2 データ ウェアハウスに復元することもできます。 Gen 2 インスタンスを復元先とすることによって、[Gen1 データ ウェアハウスをアップグレード](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation)する前に Gen2 の効果を評価できるようになりました。

![カスタム復元構成 - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
[sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) ストアド プロシージャは、Transact-SQL バッチ内のパラメーターに関するメタデータをツールで取得する際によく使用されます。 このプロシージャは、バッチ内の各パラメーターにつき 1 行を、その推定される型情報と共に返します。 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

この結果セットには、`@id` パラメーターに関するメタデータが含まれています。以下に示したのは結果の抜粋です。
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```