---
title: Azure Purview のアクセス ポリシーの概念
description: Azure Purview のアクセス ポリシーについて
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4d3d635b3f38f344ca0ae6cf89e16b0e6714073
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089942"
---
# <a name="concepts-for-azure-purview-data-access-policies"></a>Azure Purview のデータ アクセス ポリシーの概念

この記事は、Azure Purview 内からデータ資産へのアクセスを管理できる Azure Purview データ アクセス ポリシーについて理解するのに役立ちます。

> [!Note]
> この機能は、Azure Purview 自体のアクセスの制御とは異なります。これについては、「[Azure Purview でのアクセスの制御](catalog-permissions.md)」で説明されています。

> [!IMPORTANT]
> Azure Purview のアクセス ポリシーは現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

## <a name="overview"></a>概要

Azure Purview のアクセス ポリシーを使用すると、データ資産全体におけるさまざまなデータ システムへのアクセスを管理できます。 たとえば、次のように入力します。

ユーザーには、Azure Purview に登録されている Azure Storage アカウントに対する読み取りアクセスが必要です。 このアクセスを Azure Purview に直接付与するには、Purview Studio で **ポリシー管理** アプリを使用してデータ アクセス ポリシーを作成します。

データ アクセス ポリシーは、ポリシーに登録されているデータ システム上で Purview を使用して適用できます。

## <a name="azure-purview-policy-concepts"></a>Azure Purview のポリシーの概念

### <a name="azure-purview-policy"></a>Azure Purview のポリシー

**ポリシー** とは、ポリシー ステートメントの名前付きコレクションです。 ポリシーが Purview のガバナンス下で 1 つ以上のデータ システムに公開されると、そのポリシーが適用されます。 ポリシー定義には、ポリシー名、説明、および 1 つ以上のポリシー ステートメントのリストが含まれます。

### <a name="policy-statement"></a>ポリシー ステートメント

**ポリシー ステートメント** は、データ ソースが特定のデータ操作を処理する方法を指定する、人間が判読できる命令です。 ポリシー ステートメントは、**影響**、**アクション、データ リソース**、および **サブジェクト** で構成されます。

#### <a name="action"></a>操作

**アクション** とは、このポリシーの一部として許可または拒否される操作のことです。 例: 読み取りまたは書き込み。 これらの高レベルの論理アクションは、それらが適用されるデータ システム内の 1 つ (または複数) のデータ アクションにマップされます。

#### <a name="effect"></a>結果

**効果** は、このポリシーの結果として生じる効果を示します。 現在、サポートされている値は **[許可]** のみです。

#### <a name="data-resource"></a>データ リソース

**データ リソース** は、ポリシー ステートメントが適用される完全修飾データ資産パスです。 これは、次の形式に準拠しています。

*/subscription/\<subscription-id>/resourcegroups/\<resource-group-name>/providers/\<provider-name>/\<data-asset-path>*

Azure Storage データ資産パスの形式:

*Microsoft.Storage/storageAccounts/\<account-name>/blobservice/default/containers/\<container-name>*

Azure SQL DB データ資産パスの形式:

*Microsoft.Sql/servers/\<server-name>*

#### <a name="subject"></a>サブジェクト

このポリシー ステートメントが適用される Azure Active Directory (AAD) のエンド ユーザー ID。 この ID には、サービス プリンシパル、個々のユーザー、グループ、または管理サービス ID (MSI) を指定できます。

### <a name="example"></a>例

データ資産の読み取りの拒否: */subscription/finance/resourcegroups/prod/providers/Microsoft.Storage/storageAccounts/finDataLake/blobservice/default/containers/FinData to group Finance-analyst*

上記のポリシー ステートメントでは、効果は *拒否*、アクションは *読み取り*、データ リソースは Azure Storage コンテナー *FinData*、サブジェクトは AAD グループ *Finance-analyst* です。 このグループに属するユーザーがストレージ コンテナー *FinData* からデータを読み取ろうとすると、要求は拒否されます。

### <a name="hierarchical-enforcement-of-policies"></a>ポリシーの階層的な適用

既定では、ポリシー ステートメントで指定されたデータ リソースは階層化されています。 これは、ポリシー ステートメントがデータ オブジェクト自体と、データ オブジェクトに含まれる子オブジェクトの **すべて** に適用されることを意味します。 たとえば、Azure Storage コンテナーのポリシー ステートメントは、その中に含まれるすべての BLOB に適用されます。

### <a name="policy-combining-algorithm"></a>ポリシー結合アルゴリズム 

Azure Purview は、同じデータ資産を参照するさまざまなポリシー ステートメントを持つことができます。 Azure Purview は、データ アクセスの決定を評価する際に、適用可能なすべてのポリシーを結合し、統合された意思決定を行います。 結合戦略では、最も制限の厳しいポリシーが選択されます。
たとえば、次のように、Azure Storage コンテナー *FinData* に対して 2 つの異なるポリシーを使用するとします。

ポリシー 1 - *データ資産の読み取りの許可 /subscription/…./containers/FinData To group Finance-analyst*

ポリシー 2 - *データ資産の読み取りの拒否 /subscription/…./containers/FinData To group Finance-contractors*

次に、2 つのグループ (*Finance-analyst* および *Finance-contractors*) の一部であるユーザー「user1」 が、blob read API の呼び出しを実行するとします。 両方のポリシーが適用可能であるため、Azure Purview は、*読み取り* の *拒否* という、最も制限の厳しいポリシーを選択します。 このため、アクセス要求は拒否されます。

## <a name="policy-publishing"></a>ポリシーの発行

新しく作成されたポリシーはドラフト モード状態で存在し、Azure Purview でのみ表示されます。 発行操作によって、指定されたデータ システムでポリシーの適用が開始されます。 これは非同期アクションであり、基になるデータ ソースで有効になるまでに最大 2 分かかることがあります。

データ ソースに発行されたポリシーには、別のデータ ソースに属する資産への参照を含めることができます。 該当する資産は、ポリシーが適用されているデータ ソースに存在しないため、このような参照は無視されます。

## <a name="azure-purview-to-data-source-action-mapping"></a>Azure Purview からデータ ソースへのアクション マッピング

次の表は、Azure Purview データ ポリシーのアクションがデータ システムの特定のアクションにどのようにマップされるかを示しています。

| **Purview ポリシー アクション** | **データ ソース固有のアクション**                                                                |
|---------------------------|-------------------------------------------------------------------------------------------------|
|||
| *読み取り*                      |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read                        |
|                           |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery                      |
|                           |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed                    |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                            |
|||
| *Modify*                    |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                            |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write                           |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action                     |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete                          |
|||
| *SQL パフォーマンス モニター*   |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerMetadata/rows/select                     |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseMetadata/rows/select         |
|                           |<sub>Microsoft.Sql/sqlservers/Connect                                                                |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerState/rows/select                        |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseState/rows/select            |
|                           |<sub>Microsoft.Sql/sqlservers/databases/Connect                                                      |
|||
| *SQL 監査担当者*               |<sub>Microsoft.Sql/sqlservers/databases/Connect                                                      |
|                           |<sub>Microsoft.Sql/sqlservers/Connect                                                                |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityState/rows/select                |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityState/rows/select    |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityMetadata/rows/select             |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityMetadata/rows/select |

## <a name="next-steps"></a>次のステップ
Azure Storage などの特定のデータ システムで動作する Azure Purview でポリシーを作成する方法に関するチュートリアルを確認します。

* [Azure Storage のデータ所有者によるデータセットのプロビジョニング](how-to-access-policies-storage.md)

<!--
[Dataset provisioning by data owner for Azure SQL DB](how-to-access-policies-sql.md)
-->
