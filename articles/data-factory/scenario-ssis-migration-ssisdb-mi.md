---
title: データベース ワークロードの宛先として Azure SQL マネージド インスタンスを使用した SSIS の移行
description: データベース ワークロードの宛先として Azure SQL マネージド インスタンスを使用した SSIS の移行。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116308"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>データベース ワークロードの宛先として Azure SQL マネージド インスタンスを使用した SSIS の移行

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

SQL Server インスタンスから Azure SQL Database マネージド インスタンスにデータベース ワークロードを移行する場合は、[Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) (DMS) と、[DMS を使用した SQL マネージド インスタンスの移行のネットワーク トポロジ](https://docs.microsoft.com/azure/dms/resource-network-topologies)を理解している必要があります。

この記事では、SSIS カタログ (SSISDB) に格納されている SQL Server Integration Service (SSIS) パッケージと SSIS パッケージの実行をスケジュールする SQL Server エージェント ジョブの移行に焦点を当てています。

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS カタログ (SSISDB) を移行する

SSISDB の移行は、次の記事で説明されているように、DMS を使用して行うことができます。「[SSIS パッケージを SQL マネージド インスタンスに移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)」。

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS ジョブを SQL Database Managed Instance エージェントに移行する

SQL マネージド インスタンスには、オンプレミスの SQL Server エージェントと同様に、ネイティブの第一級のスケジューラがあります。  SSIS ジョブの移行ツールはまだ使用できないため、スクリプト/手動コピーを使用して、オンプレミスの SQL Server エージェントから SQL マネージド インスタンス エージェントに移行する必要があります。

## <a name="additional-resources"></a>その他のリソース

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [DMS を使用した SQL マネージド インスタンスの移行のためのネットワーク トポロジ](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS パッケージを SQL マネージド インスタンスに移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>次のステップ

- [Azure での SSISDB への接続](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure にデプロイされた SSIS パッケージを実行する](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
