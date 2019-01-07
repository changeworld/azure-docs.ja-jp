---
title: データベース移行シナリオの状態 | Microsoft Docs
description: Azure Database Migration Service によってサポートされる移行シナリオの状態について学習します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 9eea3c28751ecaec0ffdb27e08f84cad1bf62acd
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53267868"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Azure Database Migration Service によってサポートされる移行シナリオの状態
Azure Database Migration Service は、オフライン (1 回限り) とオンライン (継続的同期) 両方のさまざまな移行シナリオ (ソース/ターゲットのペア) をサポートするように設計されています。 Azure Database Migration Service が対応するシナリオの範囲は、徐々に広がっています。 定期的に新しいシナリオが追加されています。 この記事では、Azure Database Migration Service で現在サポートされている移行シナリオと、各シナリオの状態 (プライベート プレビュー、パブリック プレビュー、一般公開) を示します。

## <a name="offline-versus-online-migrations"></a>オフライン移行とオンライン移行
Azure Database Migration Service を使用してデータベースを Azure に移行するときは、オフライン移行またはオンライン移行を実行できます。 "*オフライン*" 移行では、移行開始と同時にアプリケーションのダウンタイムが始まります。 "*オンライン*" 移行のダウンタイムは、移行が完了して新しい環境への切り替えに必要な時間に限定されます。 オフライン移行をテストして、ダウンタイムが許容可能かどうかを判断することをお勧めします。許容できない場合は、オンライン移行を実行します。

## <a name="migration-scenario-status"></a>データベース移行シナリオの状態
Azure Database Migration Service によってサポートされる各移行シナリオの状態は、時間とともに変化します。 一般に、シナリオは最初に**プライベート プレビュー**でリリースされ、機能を利用するには、お客様は [DMS プレビュー サイト](https://aka.ms/dms-preview)で申請を送信する必要があります。 プライベート プレビューが完了すると、シナリオの状態は**パブリック プレビュー**に変わります。 Azure Database Migration Service のすべてのユーザーが、パブリック プレビューで使用可能な移行シナリオを利用できます。 ただし、リージョンによっては移行シナリオを使用できない場合があり、最終リリースまでは機能の変更がさらに行われる可能性があります。 移行シナリオが最後のリリース済み状態である**一般公開**になると、機能は完成し、Azure Database Migration Service のすべてのユーザーがアクセスできるようになります。 

## <a name="migration-scenario-support"></a>移行シナリオのサポート

次の表では、Azure Database Migration Service を使用する場合にサポートされる移行シナリオを示します。

> [!NOTE]
> 以下の一覧でサポートされるようになっているシナリオが、ユーザー インターフェイスに表示されない場合は、[データ移行チーム](mailto:datamigrationteam@microsoft.com)に詳細をお問い合わせください。

### <a name="offline-one-time-migration-support"></a>オフライン (1 回限り) の移行のサポート
次の表では、オフライン移行に対する Azure Database Migration Service のサポートを示します。

| ターゲット  | ソース | サポート |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS SQL  |  ✔ |
|   | Oracle  |   |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS SQL  | ✔ |
|   | Oracle  | ✔  |
| **Azure SQL MV**  | SQL Server  | ✔ |
|   | Oracle  |   |
| **Cosmos DB**  | MongoDB  | ✔ |
| **Azure DB for MySQL**  | MySLQ  |  |
|   | RDS MySQL  |  |
| **Azure DB for PostgresSQL**  | PostgreSQL |  |
|  | RDS PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>オンライン (継続的同期) の移行のサポート
次の表では、オンライン移行に対する Azure Database Migration Service のサポートを示します。

| ターゲット  | ソース | サポート |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS SQL  |   |
|   | Oracle  |  ✔ |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS SQL  |  |
|   | Oracle  | ✔  |
| **Azure SQL MV**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **Cosmos DB**  | MongoDB  | ✔ |
| **Azure DB for MySQL**  | MySLQ  | ✔ |
|   | RDS MySQL  | ✔ |
| **Azure DB for PostgresSQL**  | PostgreSQL | ✔ |
|  | RDS PostgreSQL  | ✔ |

## <a name="next-steps"></a>次の手順
Azure Database Migration Service の概要と、リージョンごとの利用可能性については、「[Azure Database Migration Service とは](dms-overview.md)」という記事をご覧ください。 
