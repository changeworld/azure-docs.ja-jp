---
title: 台帳が有効化されたテーブルを使用した Azure SQL Database 監査イベント
description: Azure SQL Database 台帳監査機能の概要
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: eafca78dc22a01784eee79293fb7a2ec798818ad
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388400"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>台帳が有効化されたテーブルを使用した Azure SQL Database 監査イベント

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳は **パブリック プレビュー** 段階にあります。

台帳が有効化されたテーブルを使用してフォレンジクス アクティビティを実行すると、台帳ビューとデータベース台帳でキャプチャされたデータに加えて、追加のアクション ID が SQL 監査ログに追加されます。  次のテーブルは、これらの新しい監査ログ イベントと、イベントをトリガーする条件の概要を示します。

## <a name="enable-ledger"></a>台帳を有効化する

| 列 | [値] |
|--|--|
| **action_id** | ENLR |
| **name** | ENABLE LEDGER  |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**イベントをトリガーする条件**: 新しい台帳テーブルの作成、通常のテーブルの台帳テーブルへの変換。

## <a name="alter-ledger"></a>台帳を変更する

| 列 | [値] |
|--|--|
| **action_id** | ALLR |
| **name** | ALTER LEDGER |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**イベントをトリガーする条件**: 台帳テーブルの削除または名前の変更、台帳テーブルの通常のテーブルへの変換、台帳テーブル内の列の追加、削除、または名前の変更。


## <a name="generate-ledger-digest"></a>台帳ダイジェストを生成する

| 列 | [値] |
|--|--|
| **action_id** | GDLR |
| **name** | GENERATE LEDGER DIGEST |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP  |
| **action_in_log** | 1 |

**イベントをトリガーする条件**: 台帳ダイジェストの生成。

## <a name="verify-ledger"></a>台帳を検証する

| 列 | [値] |
|--|--|
| **action_id** | VFLR |
| **name** | VERIFY LEDGER |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**イベントをトリガーする条件**: 台帳ダイジェストの検証。

## <a name="ledger-operation-group"></a>台帳操作グループ

| 列 | [値] |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | DATABASE |
| **covering_action_desc** | NULL |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**イベントをトリガーする条件**: 該当なし

| 列 | [値] |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | SERVER |
| **covering_action_desc** | NULL |
| **parent_class_desc** | NULL |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**イベントをトリガーする条件**: 該当なし 

## <a name="next-steps"></a>次の手順

- [Azure SQL Database および Azure Synapse Analytics の監査](auditing-overview.md)
- [Azure SQL Database 台帳の概要](ledger-overview.md)
- [クイックスタート: 台帳が有効化された Azure SQL Database を作成する](ledger-create-a-single-database-with-ledger-enabled.md)
