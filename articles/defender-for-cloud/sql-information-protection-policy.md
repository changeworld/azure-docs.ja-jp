---
title: Microsoft Defender for Cloud での SQL 情報保護ポリシー
description: Microsoft Defender for Cloud で情報保護ポリシーをカスタマイズする方法を説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: defender-for-cloud
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 2300d038cdecd6b994775290e7ae1c3afa874aa3
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557137"
---
# <a name="sql-information-protection-policy-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud での SQL 情報保護ポリシー

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

SQL 情報保護の[データの検出と分類メカニズム](../azure-sql/database/data-discovery-and-classification-overview.md)では、データベース内の機密データの検出、分類、ラベル付け、およびレポートを行うために高度な機能が提供されます。 これは、[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)、[Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)、[Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) に組み込まれています。

分類メカニズムは、次の 2 つの要素に基づいています。

- **ラベル** - 列に格納される *データの機密レベル* を定義するために使用される、主な分類属性です。 
- **情報の種類** – 列に格納されている *データの種類* に対する追加の細分性を提供します。

Defender for Cloud 内の情報保護ポリシー オプションには、ラベルと情報の種類の定義済みセットが用意されおり、分類エンジンの既定値として機能します。 次に示すように、組織のニーズに応じてポリシーをカスタマイズできます。

:::image type="content" source="./media/sql-information-protection-policy/sql-information-protection-policy-page.png" alt-text="SQL 情報の保護ポリシーを示しているページ。":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>SQL 情報保護ポリシーにアクセスする方法

情報保護ポリシーにアクセスするには、次の 3 つの方法があります。

- **(推奨)** Defender for Cloud の **[環境設定]** ページから
- セキュリティの推奨事項 "SQL データベースの機密データを分類する必要があります" から
- Azure SQL DB のデータ検出ページから

これらはそれぞれ、次の関連タブに表示されます。



### <a name="from-defender-for-clouds-settings"></a>[**Defender for Cloud の設定から**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-defender-for-clouds-environment-settings-page"></a>Defender for Cloud の環境設定ページからポリシーにアクセスする <a name="sqlip-tenant"></a>

Defender for Cloud の **[環境設定]** ページから **[SQL Information Protection]** を選択します。

> [!NOTE]
> このオプションは、テナントレベルのアクセス許可を持つユーザーに対してのみ表示されます。 [テナント全体のアクセス許可を自分に付与します](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)。

:::image type="content" source="./media/sql-information-protection-policy/environment-settings-link-to-information-protection.png" alt-text="Microsoft Defender for Cloud の環境設定ページから SQL Information Protection ポリシーへのアクセス。":::



### <a name="from-defender-for-clouds-recommendation"></a>[**Defender for Cloud の推奨事項から**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-defender-for-cloud-recommendation"></a>Defender for Cloud の推奨事項からポリシーにアクセスする <a name="sqlip-db"></a>

Defender for Cloud の推奨事項である "SQL データベースの機密データを分類する必要がある" を使用して、データベースのデータ検出と分類ページを表示します。 ここには、分類が推奨される情報が含まれていることが検出された列も表示されます。

1. Defender for Cloud の **[推奨事項]** ページで、"**SQL データベースの機密データを分類する必要がある**" という推奨事項を検索します。

    :::image type="content" source="./media/sql-information-protection-policy/sql-sensitive-data-recommendation.png" alt-text="SQL 情報保護ポリシーへのアクセスを提供する推奨事項の検索。":::

1. [推奨事項の詳細] ページで、 **[正常]** または **[異常]** タブからデータベースを選択します。

1. **[データの検出と分類]** ページが開きます。 **[構成]** をクリックします。

    :::image type="content" source="./media/sql-information-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Microsoft Defender for Cloud の関連する推奨事項から SQL 情報保護ポリシーを開く":::



### <a name="from-azure-sql"></a>[**Azure SQL から**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Azure SQL からポリシーにアクセスする <a name="sqlip-azuresql"></a>

1. Azure portal から Azure SQL を開きます。

    :::image type="content" source="./media/sql-information-protection-policy/open-azure-sql.png" alt-text="Azure portal から Azure SQL を開く。":::

1. 任意のデータベースを選択します。

1. メニューの **[セキュリティ]** 領域で、 **[データ検出と分類]** ページ (1) を開き、 **[構成]** (2) を選択します。

    :::image type="content" source="./media/sql-information-protection-policy/access-policy-from-azure-sql.png" alt-text="Azure SQL から SQL 情報保護ポリシーを開く。":::

--- 

## <a name="customize-your-information-types"></a>情報の種類をカスタマイズする

情報の種類を管理およびカスタマイズするには:

1. **[情報の種類を管理する]** を選択します。

    :::image type="content" source="./media/sql-information-protection-policy/manage-types.png" alt-text="情報保護ポリシーの情報の種類を管理する。":::

1. 新しい種類を追加するには、 **[情報の種類を作成する]** を選択します。 情報の種類の名前、説明、検索パターン文字列を構成できます。 検索パターン文字列では、オプションで、ワイルドカード文字 ('%' 文字を使用) を含むキーワードを使用できます。自動検出エンジンでは、このキーワードを使用して、列のメタデータに基づいてデータベース内の機密データが識別されます。
 
    :::image type="content" source="./media/sql-information-protection-policy/configure-new-type.png" alt-text="情報保護ポリシーの新しい情報の種類を構成する。":::

1. 追加の検索パターン文字列の追加、既存の文字列の一部の無効化、または説明の変更により、組み込みの情報の種類を変更することもできます。 

    > [!TIP]
    > 組み込みの種類を削除したり、名前を変更したりすることはできません。 

1. **[情報の種類]** は、検出の順位付けの昇順で一覧表示されます。つまり、リスト内の上位の種類の照合が最初に試みられます。 情報の種類間で順位を変更するには、テーブル内で種類を適切な位置にドラッグするか、 **[上へ移動]** と **[下へ移動]** を使用して順序を変更します。 

1. 完了したら、 **[OK]** をクリックします。

1. 情報の種類の管理が完了したら、特定のラベルの **[構成]** をクリックし、情報の種類を適宜追加または削除することで、適切なラベルに適切な種類を関連付けてください。

1. 変更を適用するには、メインの **[ラベル]** ページで **[保存]** を選択します。
 

## <a name="exporting-and-importing-a-policy"></a>ポリシーのエクスポートおよびインポート

定義されているラベルと情報の種類が含まれる JSON ファイルをダウンロードし、任意のエディターでファイルを編集して、更新されたファイルをインポートすることができます。 

:::image type="content" source="./media/sql-information-protection-policy/export-import.png" alt-text="情報保護ポリシーのエクスポートとインポート。":::

> [!NOTE]
> ポリシー ファイルをインポートするには、テナント レベルのアクセス許可が必要です。 


## <a name="permissions"></a>アクセス許可

Azure テナントの情報保護ポリシーをカスタマイズするには、テナントのルート管理グループに対して次のアクションが必要です。
  - Microsoft.Security/informationProtectionPolicies/read
  - Microsoft.Security/informationProtectionPolicies/write 

詳細については、「[テナント全体の可視性を付与および要求する](tenant-wide-permissions-management.md)」を参照してください。

## <a name="manage-sql-information-protection-using-azure-powershell"></a>Azure PowerShell を使用した SQL 情報保護の管理

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy):有効なテナント SQL 情報保護ポリシーを取得します。
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy):有効なテナント SQL 情報保護ポリシーを設定します。
 

## <a name="next-steps"></a>次のステップ
 
この記事では、Microsoft Defender for Cloud 上での情報保護ポリシーの定義について説明しました。 SQL Information Protection を使用して、SQL データベース内の機密データを分類および保護する方法について詳しくは、「[Azure SQL Database のデータの検出と分類](../azure-sql/database/data-discovery-and-classification-overview.md)」をご覧ください。

Defender for Cloud でのセキュリティ ポリシーとデータ セキュリティについて詳しくは、次の記事をご覧ください。
 
- [Microsoft Defender for Cloud でのセキュリティ ポリシーの設定](tutorial-security-policy.md): Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
- [Microsoft Defender for Cloud のデータ セキュリティ](data-security.md): Defender for Cloud によってデータが管理および保護される方法について説明します
