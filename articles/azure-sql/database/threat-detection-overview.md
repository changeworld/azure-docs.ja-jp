---
title: 高度な脅威保護
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: Advanced Threat Protection では、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics での潜在的なセキュリティ脅威を示す異常なデータベース アクティビティが検出されます。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.topic: conceptual
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto, sstein
ms.date: 06/09/2021
tags: azure-synapse
ms.openlocfilehash: 5377af3720f8666f23bfc956c86fdc5df667b432
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301857"
---
# <a name="sql-advanced-threat-protection"></a>SQL Advanced Threat Protection
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)] :::image type="icon" source="../media/applies-to/yes.png" border="false":::Azure VM 上の SQL Server:::image type="icon" source="../media/applies-to/yes.png" border="false":::Azure Arc 対応 SQL Server

[Azure SQL Database](sql-database-paas-overview.md)、[Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)、[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)、[Azure Virtual Machines 上の SQL Server](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)、[Azure Arc 対応 SQL Server](/sql/sql-server/azure-arc/overview) の Advanced Threat Protection では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出されます。

Advanced Threat Protection は、高度な SQL セキュリティ機能の統合パッケージである [Microsoft Defender for SQL](../../security-center/defender-for-sql-introduction.md) オファリングの一部です。 Advanced Threat Protection は、中央の Microsoft Defender for SQL ポータル経由でアクセスし、管理できます。

## <a name="overview"></a>概要

Advanced Threat Protection で提供される新しいセキュリティ階層では、異常なアクティビティに対するセキュリティ アラートが提供されるので、お客様は潜在的な脅威が発生したときにそれを検出して対応できます。 不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセスやクエリのパターンが見つかった場合に、ユーザーはアラートを受信します。 Advanced Threat Protection では、アラートと [Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/) が統合されます。これには、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が含まれます。 Advanced Threat Protection を使用すると、データベースに対する潜在的な脅威に簡単に対処でき、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。

完全な調査エクスペリエンスを実現するために、監査を有効にし、データベース イベントを Azure ストレージ アカウントの監査ログに書き込むことをお勧めします。  監査を有効にするには、[Azure SQL Database と Azure Synapse の監査](../../azure-sql/database/auditing-overview.md)、または [Azure SQL Managed Instance の監査](../managed-instance/auditing-configure.md)に関するページを参照してください。

## <a name="alerts"></a>警告

Advanced Threat Protection では、データベースにアクセスしたり、データベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティを検出します。 アラートの一覧については、[Microsoft Defender for Cloud の SQL Database および Azure Synapse Analytics のアラート](../../security-center/alerts-reference.md#alerts-sql-db-and-warehouse)に関するセクションを参照してください。

## <a name="explore-detection-of-a-suspicious-event"></a>疑わしいイベントの検出を試す

異常なデータベース アクティビティが検出されると、電子メールで通知を受け取ります。 電子メールでは、異常なアクティビティの特徴、データベース名、サーバー名、アプリケーション名、イベントの時刻など、疑わしいセキュリティ イベントについての情報が提供されます。 さらに、データベースへの潜在的な脅威の考えられる原因と調査や緩和のための推奨されるアクションについての情報も提供されます。

![異常アクティビティ レポート](./media/threat-detection-overview/anomalous_activity_report.png)

1. 電子メールの **[View recent SQL alerts]\(最近の SQL アラートの表示\)** リンクをクリックして Azure portal を起動し、Microsoft Defender for Cloud のアラート ページを表示します。このページには、データベースで検出されたアクティブな脅威の概要が示されます。

   ![アクティビティの脅威](./media/threat-detection-overview/active_threats.png)

1. 特定のアラートをクリックすると、さらに詳細な情報と、この脅威を調査し、今後の脅威に対処するためのアクションが表示されます。

   たとえば、SQL インジェクションはインターネットにおける Web アプリケーションの最も一般的なセキュリティ問題の 1 つであり、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。 SQL インジェクションのアラートの場合、アラートの詳細に、悪用された脆弱な SQL ステートメントが含まれています。

   ![特定のアラート](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Azure portal でアラートを調べる

Advanced Threat Protection では、アラートが [Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/) と統合されています。 データベース内のライブ SQL Advanced Threat Protection タイルと Azure portal の SQL Microsoft Defender for Cloud ブレードでは、アクティブな脅威の状態を追跡できます。

**[Advanced Threat Protection アラート]** をクリックすると、Microsoft Defender for Cloud のアラート ページが起動され、データベースに対して検出されたアクティブな SQL 脅威の概要が表示されます。

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="データベースでの Advanced Threat Protection のアラートに関する概要":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="Defender for SQL の高度な脅威保護":::

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database と Azure Synapse の Advanced Threat Protection](threat-detection-configure.md) について説明します。
- [Azure SQL Managed Instance の Advanced Threat Protection](../managed-instance/threat-detection-configure.md) について説明します。
- [Microsoft Defender for SQL](azure-defender-for-sql.md) の詳細について学習します。
- [Azure SQL Database 監査](../../azure-sql/database/auditing-overview.md)の詳細について参照してください
- [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md) の詳細について学習します。価格の詳細については、[Azure SQL Database の価格ページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください
