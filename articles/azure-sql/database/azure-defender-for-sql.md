---
title: Microsoft Defender for SQL
description: データベースの脆弱性の管理、Azure SQL Database、Azure SQL Managed Instance、または Azure Synapse のデータベースへの脅威を示す可能性がある異常なアクティビティの検出などの機能について説明します。
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 06/07/2021
ms.openlocfilehash: 77c9e9fc02ba5fdb5f67cf14495103b352196f0c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347896"
---
# <a name="microsoft-defender-for-sql"></a>Microsoft Defender for SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Microsoft Defender for SQL は、高度な SQL セキュリティ機能のための統合パッケージです。 Microsoft Defender for Cloud は、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics で使用可能です。 これには、データベースの潜在的な脆弱性を検出し、軽減する機能や、データベースに対する脅威を示す異常な行動を検出する機能が含まれています。 これらの機能を 1 つの場所で有効にして管理できます。

## <a name="what-are-the-benefits-of-microsoft-defender-for-sql"></a>Microsoft Defender for SQL の利点

Microsoft Defender for Cloud には、SQL 脆弱性の評価や Advanced Threat Protection など、高度な SQL セキュリティ機能のセットが用意されています。
- [脆弱性評価](sql-vulnerability-assessment.md)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 セキュリティの状態を表示することができ、セキュリティの問題を解決して、データベースのセキュリティを強化するために実行可能な手順が含まれます。
- [Advanced Threat Protection](threat-detection-overview.md) では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、潜在的な脆弱性、Azure SQL インジェクション攻撃、および異常なデータベース アクセス パターンが見つかるとすぐにセキュリティ通知を提供します。 Advanced Threat Protection アラートでは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が提供されます。

Microsoft Defender for SQL を一度有効にすれば、これらの含まれているすべての機能が有効になります。 1 回のクリックで、Azure 内の[サーバー](logical-servers.md)上または SQL Managed Instance 上のすべてのデータベースに対して Microsoft Defender を有効にできます。 Microsoft Defender for Cloud の設定を有効にしたり、管理したりするには、[SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) ロール、またはデータベースもしくはサーバーの管理者ロールに属している必要があります。

Microsoft Defender for SQL の価格の詳細については、[Microsoft Defender for Cloud の価格](https://azure.microsoft.com/pricing/details/security-center/)に関するページを参照してください。

## <a name="enable-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud を有効にする

Microsoft Defender プランを有効にする方法は複数あります。 次の場所からサブスクリプション レベルで有効にすることができます (**推奨**)。

- [Microsoft Defender for Cloud](#enable-microsoft-defender-for-azure-sql-database-at-the-subscription-level-from-microsoft-defender-for-cloud)
- [REST API、Azure CLI、PowerShell、または Azure Policy を使用してプログラムから Defender プランを有効にする](#enable-microsoft-defender-plans-programatically)

または、「[リソース レベルで Microsoft Defender for Azure SQL Database を有効にする](#enable-microsoft-defender-for-azure-sql-database-at-the-resource-level)」の説明に従って、リソース レベルで有効にすることもできます

### <a name="enable-microsoft-defender-for-azure-sql-database-at-the-subscription-level-from-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud からサブスクリプション レベルで Microsoft Defender for Azure SQL Database を有効にする
Microsoft Defender for Cloud 内からサブスクリプション レベルで Microsoft Defender for Azure SQL Database を有効にするには、次のようにします。

1. [Azure portal](https://portal.azure.com) から、**Defender for Cloud** を開きます。
1. Defender for Cloud のメニューで、 **[価格と設定]** を選択します。
1. 関連するサブスクリプションを選択します。
1. プランの設定を **[オン]** に変更します。

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="サブスクリプションレベルでの Microsoft Defender for Azure SQL Database の有効化。":::

1. **[保存]** を選択します。


### <a name="enable-microsoft-defender-plans-programatically"></a>Microsoft Defender プランをプログラムによって有効にする 

Azure の柔軟性により、プログラムで Microsoft Defender のプランを有効にするさまざまな方法があります。 

ご利用のサブスクリプションに対して Microsoft Defender を有効にするには、以下に示すいずれかのツールを使用します。 

| メソッド       | Instructions                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| REST API     | [Pricings API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI    | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [バンドルの価格](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-microsoft-defender-for-azure-sql-database-at-the-resource-level"></a>リソース レベルで Microsoft Defender for Azure SQL Database を有効にする

Microsoft Defender プランは、サブスクリプション レベルで有効にすることをお勧めします。これにより、保護されていないリソースの作成が楽になります。 ただし、サーバー レベルで Microsoft Defender for Cloud を有効にする理由が組織にある場合は、次の手順を使用します。

1. [Azure portal](https://portal.azure.com) から、サーバーまたはマネージド インスタンスを開きます。
1. **[セキュリティ]** という見出しの下で **[Defender for Cloud]** を選択します。
1. **[Microsoft Defender for SQL を有効にする]** を選択します。

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Azure SQL データベース内から Microsoft Defender for SQL を有効にする。":::

> [!NOTE]
> ストレージ アカウントが自動的に作成され、**脆弱性評価** のスキャン結果を格納するように構成されます。 同じリソース グループおよびリージョン内の別のサーバー用に Microsoft Defender を既に有効にしてある場合は、既存のストレージ アカウントが使用されます。
>
> Microsoft Defender for SQL のコストは、ノードあたりの Microsoft Defender for Cloud Standard レベルの価格に合わせて設定されています。ここで、ノードとは、マネージド インスタンスまたはサーバー全体です。 したがって、1 回お支払いいただければ、サーバーまたはマネージド インスタンス上のすべてのデータベースが Microsoft Defender for Cloud で保護されます。 無料試用版を使用して、Microsoft Defender for Cloud を評価することができます。

## <a name="manage-microsoft-defender-for-cloud-settings"></a>Microsoft Defender for Cloud の設定を管理する

Microsoft Defender for Cloud の設定を表示および管理するには、次のようにします。

1. ご利用のサーバーまたはマネージド インスタンスの **[セキュリティ]** 領域から **[Defender for Cloud]** を選択します。

    このページには、Microsoft Defender for SQL の状態が表示されます。

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Azure SQL データベース内の Microsoft Defender for SQL の状態を確認。":::

1. Microsoft Defender for SQL が有効になっている場合は、前の図に示されているように **[構成]** リンクが表示されます。 Microsoft Defender for SQL の設定を編集するには、 **[構成]** を選択します。

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Microsoft Defender for SQL の設定。":::

1. 必要な変更を加えて、 **[保存]** を選択します。


## <a name="next-steps"></a>次のステップ

- [脆弱性評価](sql-vulnerability-assessment.md)についてさらに詳しく学習する
- [Advanced Threat Protection](threat-detection-configure.md) についてさらに詳しく学習する
- [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md) について詳細を学習する
