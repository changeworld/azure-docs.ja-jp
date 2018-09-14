---
title: Azure Database Migration Service の概要 | Microsoft Docs
description: 多数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を提供する、Azure Database Migration Service の概要です。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 09/01/2018
ms.openlocfilehash: d59850b0234912b02b003f4fc8089d76130151ba
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666231"
---
# <a name="what-is-the-azure-database-migration-service"></a>Azure Database Migration Service とは
Azure Database Migration Service は、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できるように設計された、フル マネージドのサービスです。

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>使い慣れたツールによる Azure へのデータベースの移行
Azure Database Migration Service では、Microsoft の既存のツールやサービスの一部の機能が統合されています。 これにより、お客様には包括的な高可用性ソリューションが提供されます。 このサービスでは、[Data Migration Assistant](http://aka.ms/dma) を使用して評価レポートを生成します。評価レポートには、移行を実行する前に必要な変更について推奨される手順が記載されています。 必要な修正を実行するかどうかは、お客様の判断に委ねられます。 移行プロセスを開始する準備ができたら、Azure Database Migration Service によって、必要な手順がすべて実行されます。 プロセスは Microsoft によって決定されたベスト プラクティスを利用して実行されるので、お客様は安心して移行プロジェクトの完了を待つことができます。

## <a name="regional-availability"></a>リージョン別の提供状況
現在、Azure Database Migration Service は次のリージョンで使用できます。

![Azure Database Migration Service のリージョン別の提供状況](media\overview\dms-regional-availability.png)

> [!NOTE]
> オンラインでの移行と SKU のレコメンデーション機能は、現時点では**米国中部**、**米国東部 2**、および**西ヨーロッパ** リージョンでのみ利用可能です。

Azure Database Migration Service のリージョン別の提供状況に関する最新情報については、Azure グローバル インフラストラクチャ サイトの「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。

## <a name="next-steps"></a>次の手順
- [Azure Portal を使用して Azure Database Migration Service のインスタンスを作成します](quickstart-create-data-migration-service-portal.md)。
- [SQL Server を Azure SQL Database に移行する](tutorial-sql-server-to-azure-sql.md)
- [Azure Database Migration Service を使用するための前提条件の概要](pre-reqs.md)
- [Azure Database Migration Service の使用に関する FAQ](faq.md)
