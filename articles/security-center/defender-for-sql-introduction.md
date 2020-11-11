---
title: Azure Defender for SQL - 利点と機能
description: Azure Defender for SQL の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 220f9fffd6c0f9b959230964a9ec79f4e209bd54
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372541"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Azure Defender for SQL の概要

Azure Defender for SQL には、Azure Security Center の[データ セキュリティ パッケージ](../azure-sql/database/azure-defender-for-sql.md)を拡張して、データベースの場所を問わず、データベースとそのデータをセキュリティで保護する 2 つの Azure Defender プランが含まれています。 

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|**Azure Defender for Azure SQL database servers** - 一般提供 (GA)<br>**Azure Defender for SQL servers on machines** - プレビュー<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|価格:|**Azure Defender for SQL** を構成する 2 つのプランの課金については、 [価格に関するページ](security-center-pricing.md)をご覧ください。|
|保護される SQL のバージョン:|Azure SQL データベース <br>Azure SQL Managed Instance<br>Azure Synapse Analytics (旧称 SQL DW)<br>SQL Server (サポートされているすべてのバージョン)|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov、その他の Gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Azure Defender for SQL の保護対象

**Azure Defender for SQL** は、2 つの異なる Azure Defender プランで構成されています。

- **Azure Defender for Azure SQL database servers** では、以下を保護します。
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Azure Defender for SQL servers on machines (プレビュー)** では、Azure ネイティブの SQL Server の保護を拡張してハイブリッド環境を完全にサポートし、Azure、他のクラウド環境、さらにはオンプレミスのマシンでホストされている SQL Server (サポートされているすべてのバージョン) を保護します。


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Azure Defender for SQL の利点

この 2 つのプランには、データベースの潜在的な脆弱性を特定して軽減する機能や、データベースに対する脅威を示している可能性がある異常なアクティビティを検出する機能が含まれています。

- [脆弱性評価](../azure-sql/database/sql-vulnerability-assessment.md) - データベースの潜在的な脆弱性を検出、追跡し、修復を支援するスキャン サービス。 評価スキャンでは、SQL マシンのセキュリティ状態の概要と、セキュリティ調査結果の詳細が示されます。

- [Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) - SQL インジェクション、ブルートフォース攻撃、特権の悪用などの脅威について、SQL サーバーを継続的に監視する検出サービス。 このサービスでは、不審なアクティビティの詳細、脅威を軽減する方法に関するガイダンス、Azure Sentinel で調査を続行するためのオプションについて、Azure Security Center のアクション指向のセキュリティ アラートが提供されます。


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Azure Defender for SQL で提供されるアラートの種類

セキュリティ アラートは、次のような場合にトリガーされます。

- **潜在的な SQL インジェクション攻撃** - アプリケーションによって、データベースにエラーのある SQL ステートメントが生成されたときに検出される脆弱性など
- **異常なデータベース アクセスとクエリ パターン** - さまざまな資格情報でのサインイン試行の失敗が異常に多い場合 (ブルートフォース試行) など
- **不審なデータベース アクティビティ** - SQL インポート/エクスポート操作の、ストレージのエクスポート先が変更された場合など

アラートには、それらをトリガーするインシデントの詳細と、脅威の調査や修復方法に関する推奨事項が含まれています。



## <a name="next-steps"></a>次の手順

この記事では、Azure Defender for SQL について説明しました。

関連資料については、次の記事をご覧ください。 

- [Azure Defender for SQL servers on machines を有効にする方法](defender-for-sql-usage.md)
- [Azure Defender for SQL database servers を有効にする方法](../azure-sql/database/azure-defender-for-sql.md)
- [Azure Defender for SQL のアラートの一覧](alerts-reference.md#alerts-sql-db-and-warehouse)