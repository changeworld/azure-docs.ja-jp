---
title: Azure Security Center でのデータ サービスの脅威検出 | Microsoft Docs
description: この記事では、Azure Security Center で使用可能なデータ サービスのアラートについて説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665736"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure Security Center でのデータ サービスの脅威検出

 Azure Security Center では、データ ストレージ サービスのログが分析され、データ リソースへの脅威が検出されると、アラートがトリガーされます。 この記事では、Security Center が次のサービスに対して生成するアラートの一覧を示します。

* [Azure SQL Database と Azure SQL Data Warehouse](#data-sql)
* [Azure ストレージ](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database と SQL Data Warehouse <a name="data-sql"></a>

Advanced Threat Protection for Azure SQL Database では、データベースへのアクセスや悪用を試みる、通常とは異なる、害を及ぼす可能性がある異常なアクティビティが検出されます。

不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセスやクエリのパターンが見つかった場合に、アラートが表示されます。

Advanced Threat Protection for Azure SQL Database and SQL は、高度な SQL セキュリティ機能を提供する [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 統合パッケージに含まれており、Azure SQL データベース、Azure SQL Database マネージド インスタンス、Azure SQL Data Warehouse データベース、Azure Virtual Machines 上の SQL サーバーに対応しています。

詳細については、次を参照してください。

* [Advanced Threat Protection for Azure SQL Database を有効にする方法](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Azure Virtual Machines 上の SQL サーバーに対して Advanced Threat Protection を有効にする方法](security-center-iaas-advanced-data.md)
* [SQL Database および SQL Data Warehouse 向け脅威保護アラートの一覧](alerts-reference.md#alerts-sql-db-and-warehouse)

## Azure Storage <a name="azure-storage"></a>

Advanced Threat Protection for Storage (現在、Blob Storage でのみ利用可能) では、ストレージ アカウントへのアクセスや悪用を試みる、通常とは異なる、害を及ぼす可能性がある試行が検出されます。 この保護層により、セキュリティの専門家でなくても脅威に対処し、セキュリティ監視システムを管理できます。

>[!NOTE]
>Advanced Threat Protection for Storage は、現在、Azure Government およびソブリン クラウドのリージョンでは使用できません。

詳細については、次を参照してください。

* [Advanced Threat Protection for Azure Storage を有効にする方法](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure Storage 向け脅威保護アラートの一覧](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Azure Cosmos DB アラートは、通常とは異なる、害を及ぼす可能性がある、Azure Cosmos DB アカウントへのアクセスや悪用が試行された場合に生成されます。

詳細については、次を参照してください。

* [Azure Cosmos DB の Advanced Threat Protection (プレビュー)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 向け脅威保護アラートの一覧 (プレビュー)](alerts-reference.md#alerts-azurecosmos)
