---
title: Azure Germany データベース サービス | Microsoft Docs
description: この記事では、Azure Germany の SQL データベース サービスを比較します。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 6316c381a601d1a28c3f6ecf529b3d31526bfd45
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029108"
---
# <a name="azure-germany-database-services"></a>Azure Germany データベース サービス

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="sql-database"></a>SQL Database
Azure Germany では、Azure SQL Database と Azure SQL Managed Instance V12 が一般提供されています。 メタデータの可視性構成と保護のベスト プラクティスのガイダンスについては、[SQL Server データベース エンジンの Microsoft セキュリティ センター](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database)に関するページのほか、[SQL Database グローバル ドキュメント](../azure-sql/database/index.yml)に関するページ、[SQL Managed Instance グローバル ドキュメント](../azure-sql/managed-instance/index.yml)に関するページを参照してください。

### <a name="variations"></a>バリエーション
Azure Germany での SQL Database のアドレスは、グローバル Azure でのアドレスと異なります。

| サービスの種類 | グローバル Azure | Azure Germany |
| --- | --- | --- |
| SQL Database | *.database.windows.net | *.database.cloudapi.de |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis の詳細とその使用方法については、[Azure Cache for Redis グローバル ドキュメント](../azure-cache-for-redis/index.yml)に関するページを参照してください。

### <a name="variations"></a>バリエーション
Azure Germany での Azure Cache for Redis のアクセスと管理を行うための URL は、グローバル Azure での URL と異なります。

| サービスの種類 | グローバル Azure | Azure Germany |
| --- | --- | --- |
| キャッシュのエンドポイント | *.redis.cache.windows.net | *.redis.cache.cloudapi.de |
| Azure portal | https://portal.azure.com | https://portal.microsoftazure.de |

> [!NOTE]
> スクリプトやコードでは必ず、適切なエンドポイントと環境を考慮する必要があります。 詳細については、「[Azure PowerShell を使用した Azure Cache for Redis の管理](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md)」の「Microsoft Azure Germany に接続するには」を参照してください。
>
>


## <a name="next-steps"></a>次のステップ
補足情報と更新情報については、[Azure Germany のブログ](/archive/blogs/azuregermany/)を参照してください。