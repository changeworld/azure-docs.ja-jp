---
title: Azure Defender for SQL
description: データベースの脆弱性の管理、Azure SQL Database、Azure SQL Managed Instance、または Azure Synapse のデータベースへの脅威を示す可能性がある異常なアクティビティの検出などの機能について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 4bb7f2fd5823a9d8ebf4234f3bb41d955574b838
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451554"
---
# <a name="azure-defender-for-sql"></a>Azure Defender for SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Azure Defender for SQL は、高度な SQL セキュリティ機能のための統合パッケージです。 Azure Defender は、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics で使用可能です。 機密データの検出と分類、データベースの潜在的な脆弱性の検出と軽減、データベースへの脅威を示す可能性がある異常なアクティビティの検出を行う機能が含まれています。 これらの機能を 1 つの場所で有効にして管理できます。

## <a name="overview"></a>概要

Azure Defender により、SQL 脆弱性の評価、Advanced Threat Protection などの高度な SQL セキュリティ機能のセットが提供されます。
- [脆弱性評価](sql-vulnerability-assessment.md)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 セキュリティの状態を表示することができ、セキュリティの問題を解決して、データベースのセキュリティを強化するために実行可能な手順が含まれます。
- [Advanced Threat Protection](threat-detection-overview.md) では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、潜在的な脆弱性、Azure SQL インジェクション攻撃、および異常なデータベース アクセス パターンが見つかるとすぐにセキュリティ通知を提供します。 Advanced Threat Protection アラートでは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が提供されます。

Azure Defender for SQL を一度有効にして、これらのすべての機能を有効にします。 1 回のクリックで、Azure 内の[サーバー](logical-servers.md)上または SQL Managed Instance 上のすべてのデータベースに対して Azure Defender を有効にできます。 Azure Defender の設定を有効にしたり、管理したりするには、[SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) ロール、またはデータベースかサーバーの管理者ロールに属している必要があります。

Azure Defender for SQL の価格について詳しくは、[Azure Security Center の価格ページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。

## <a name="getting-started-with-azure-defender"></a>Azure Defender の概要

Azure Defender の使用を始める手順は以下のとおりです。

## <a name="enable-azure-defender"></a>Azure Defender を有効にする

Azure Defender には、[Azure portal](https://portal.azure.com) からアクセスできます。 Azure Defender を有効にするには、ご使用のサーバーまたはマネージド インスタンスの **[セキュリティ]** 見出しの下の **[Security Center]** に移動します。

> [!NOTE]
> ストレージ アカウントが自動的に作成され、**脆弱性評価** のスキャン結果を格納するように構成されます。 同じリソース グループおよびリージョン内の別のサーバー用に Azure Defender を既に有効にしてある場合は、既存のストレージ アカウントが使用されます。
>
> Azure Defender のコストは、ノード (マネージド インスタンスまたはサーバー全体) あたりの Azure Security Center Standard レベルの価格に合わせて設定されています。 したがって、1 回お支払いいただければ、サーバーまたはマネージド インスタンス上のすべてのデータベースが Azure Defender で保護されます。 最初は無料試用版で Azure Defender をお試しいただけます。

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Azure Defender を有効にする":::

## <a name="start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>脆弱性の追跡と脅威アラートの調査を開始する

**[脆弱性評価]** カードをクリックして、脆弱性のスキャンとレポートを表示および管理し、セキュリティ水準を追跡します。 セキュリティ通知を受け取った場合は、 **[Advanced Threat Protection]** カードをクリックして、通知の詳細を表示し、Azure Security Center のセキュリティ通知ページを使って Azure サブスクリプション内のすべての通知の統合レポートを表示します。

## <a name="manage-azure-defender-settings"></a>Azure Defender の設定を管理する

Azure Defender の設定を表示および管理するには、ご使用のサーバーまたはマネージド インスタンスの **[セキュリティ]** 見出しの下の **[Security Center]** に移動します。 このページでは、Azure Defender を有効または無効にしたり、サーバーまたはマネージド インスタンス全体に対する脆弱性評価や Advanced Threat Protection の設定を変更したりできます。

:::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="サーバーのセキュリティの設定":::

## <a name="manage-azure-defender-settings-for-a-database"></a>データベースの Azure Defender の設定を管理する

特定のデータベースに対する Azure Defender の設定をオーバーライドするには、データベースの **[Security Center]** の設定で **[データベース レベルで Azure Defender for SQL を有効にします]** チェック ボックスをオンにします。 このオプションは、サーバーまたはマネージド インスタンス上の全データベースについて受け取る通知の代わりに、またはそれに加えて、個別のデータベースに対する Advanced Threat Protection アラートまたは脆弱性評価結果を別に受け取る特定の要件がある場合にのみ使用します。

チェック ボックスをオンにすると、このデータベースに対して関連する設定を構成することができます。

:::image type="content" source="media/azure-defender-for-sql/enable-for-database-level.png" alt-text="データベース レベルで Azure Defender を有効にする":::

サーバーまたはマネージド インスタンス用の Azure Defender for SQL 設定には、Azure Defender のデータベース ペインからもアクセスできます。 メインの [Security Center] ペインで **[設定]** をクリックし、 **[Azure Defender for SQL Server 設定を表示します]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [脆弱性評価](sql-vulnerability-assessment.md)についてさらに詳しく学習する
- [Advanced Threat Protection](threat-detection-configure.md) についてさらに詳しく学習する
- [Azure Security Center](../../security-center/security-center-introduction.md) の詳細について参照してください