---
title: Azure Germany データベース サービス | Microsoft Docs
description: Azure Germany のデータベース サービスを比較します
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 7c4b2888075ab3df277d66383003ff8f6c1fef27
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2018
ms.locfileid: "58408436"
---
# <a name="azure-germany-database-services"></a>Azure Germany データベース サービス
## <a name="sql-database"></a>SQL Database
Azure SQL Database V12 は、Azure Germany で一般提供されています。 メタデータの可視性構成と保護のベスト プラクティスのガイダンスについては、[SQL Server データベース エンジンの Microsoft セキュリティ センター](https://msdn.microsoft.com/library/bb510589.aspx)に関するページと [SQL Database グローバル ドキュメント](../sql-database/index.yml)に関するページを参照してください。

### <a name="variations"></a>バリエーション
Azure Germany での SQL Database のアドレスは、グローバル Azure でのアドレスと異なります。

| サービスの種類 | グローバル Azure | Azure Germany |
| --- | --- | --- |
| SQL Database | *.database.windows.net | *.database.cloudapi.de |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis の詳細とその使用方法については、[Azure Cache for Redis グローバル ドキュメント](../azure-cache-for-redis/index.md)に関するページを参照してください。

### <a name="variations"></a>バリエーション
Azure Germany での Azure Cache for Redis のアクセスと管理を行うための URL は、グローバル Azure での URL と異なります。

| サービスの種類 | グローバル Azure | Azure Germany |
| --- | --- | --- |
| キャッシュのエンドポイント | *.redis.cache.windows.net | *.redis.cache.cloudapi.de |
| Azure ポータル | https://portal.azure.com | https://portal.microsoftazure.de |

> [!NOTE]
> スクリプトやコードでは必ず、適切なエンドポイントと環境を考慮する必要があります。 詳細については、「[Azure PowerShell を使用した Azure Cache for Redis の管理](../azure-cache-for-redis/cache-howto-manage-redis-cache-powershell.md)」の「Microsoft Azure Germany に接続するには」を参照してください。
>
>


## <a name="next-steps"></a>次の手順
補足情報と最新情報については、[Azure Germany ブログ](https://blogs.msdn.microsoft.com/azuregermany/)をご覧ください。
