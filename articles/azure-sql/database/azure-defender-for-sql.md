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
ms.date: 02/02/2021
ms.openlocfilehash: 48df96373554f6e474c3835bf81e38a9aea5450c
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508813"
---
# <a name="azure-defender-for-sql"></a>Azure Defender for SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Defender for SQL は、高度な SQL セキュリティ機能のための統合パッケージです。 Azure Defender は、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics で使用可能です。 機密データの検出と分類、データベースの潜在的な脆弱性の検出と軽減、データベースへの脅威を示す可能性がある異常なアクティビティの検出を行う機能が含まれています。 これらの機能を 1 つの場所で有効にして管理できます。

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Azure Defender for SQL の利点

Azure Defender により、SQL 脆弱性の評価、Advanced Threat Protection などの高度な SQL セキュリティ機能のセットが提供されます。
- [脆弱性評価](sql-vulnerability-assessment.md)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 セキュリティの状態を表示することができ、セキュリティの問題を解決して、データベースのセキュリティを強化するために実行可能な手順が含まれます。
- [Advanced Threat Protection](threat-detection-overview.md) では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、潜在的な脆弱性、Azure SQL インジェクション攻撃、および異常なデータベース アクセス パターンが見つかるとすぐにセキュリティ通知を提供します。 Advanced Threat Protection アラートでは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が提供されます。

Azure Defender for SQL を一度有効にして、これらのすべての機能を有効にします。 1 回のクリックで、Azure 内の[サーバー](logical-servers.md)上または SQL Managed Instance 上のすべてのデータベースに対して Azure Defender を有効にできます。 Azure Defender の設定を有効にしたり、管理したりするには、[SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) ロール、またはデータベースかサーバーの管理者ロールに属している必要があります。

Azure Defender for SQL の価格について詳しくは、[Azure Security Center の価格ページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。

## <a name="enable-azure-defender"></a>Azure Defender を有効にする

Azure Defender には、[Azure portal](https://portal.azure.com) からアクセスできます。 Azure Defender を有効にするには、ご使用のサーバーまたはマネージド インスタンスの **[セキュリティ]** 見出しの下の **[Security Center]** に移動します。

> [!NOTE]
> ストレージ アカウントが自動的に作成され、**脆弱性評価** のスキャン結果を格納するように構成されます。 同じリソース グループおよびリージョン内の別のサーバー用に Azure Defender を既に有効にしてある場合は、既存のストレージ アカウントが使用されます。
>
> Azure Defender のコストは、ノード (マネージド インスタンスまたはサーバー全体) あたりの Azure Security Center Standard レベルの価格に合わせて設定されています。 したがって、1 回お支払いいただければ、サーバーまたはマネージド インスタンス上のすべてのデータベースが Azure Defender で保護されます。 最初は無料試用版で Azure Defender をお試しいただけます。

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Azure SQL データベース内から Azure Defender for SQL を有効にする":::

## <a name="track-vulnerabilities-and-investigate-threat-alerts"></a>脆弱性を追跡し、脅威アラートを調査する

**[脆弱性評価]** カードをクリックして、脆弱性のスキャンとレポートを表示および管理し、セキュリティ水準を追跡します。 セキュリティ通知を受け取った場合は、 **[Advanced Threat Protection]** カードをクリックして、通知の詳細を表示し、Azure Security Center のセキュリティ通知ページを使って Azure サブスクリプション内のすべての通知の統合レポートを表示します。

## <a name="manage-azure-defender-settings"></a>Azure Defender の設定を管理する

Azure Defender の設定を表示および管理するには、次のようにします。

1. サーバーまたはマネージド インスタンスの **[セキュリティ]** 領域から **[Security Center]** を選択します。

    このページには、Azure Defender for SQL の状態が表示されます。

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Azure SQL データベース内の Azure Defender for SQL の状態を確認する":::

1. Azure Defender for SQL が有効になっている場合は、前の図に示されているように **[構成]** リンクが表示されます。 Azure Defender for SQL の設定を編集するには、 **[構成]** を選択します。

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="サーバーのセキュリティの設定":::

1. 必要な変更を加えて、 **[保存]** を選択します。


## <a name="next-steps"></a>次のステップ

- [脆弱性評価](sql-vulnerability-assessment.md)についてさらに詳しく学習する
- [Advanced Threat Protection](threat-detection-configure.md) についてさらに詳しく学習する
- [Azure Security Center](../../security-center/security-center-introduction.md) の詳細について参照してください