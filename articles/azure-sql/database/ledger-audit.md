---
title: 台帳が有効化されたテーブルを使用した Azure SQL Database 監査イベント
description: Azure SQL Database 台帳監査機能の概要
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 18e3c5ef5d9cb0316118e38f2ffb8dccc8c06fd7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061872"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>台帳が有効化されたテーブルを使用した Azure SQL Database 監査イベント

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳はパブリック プレビュー段階にあります。

台帳が有効になっているテーブルを使用してフォレンジック アクティビティを実行すると、データが台帳ビューとデータベース台帳にキャプチャされます。 その他のアクション ID も SQL 監査ログに追加されます。 次の表は、これらの新しい監査ログ イベントの概要を示しています。 イベントがトリガーされる条件は、各表の後にあります。

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

**イベントがトリガーされる条件**: 新しい台帳テーブルを作成したときか、標準のテーブルを台帳テーブルに変換したとき。

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

**イベントがトリガーされる条件**: 台帳テーブルを削除または名前変更したとき、台帳テーブルを通常のテーブルに変換したとき、および台帳テーブルで列を追加、削除、または名前変更したとき。


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

**イベントがトリガーされる条件**: 台帳ダイジェストを生成したとき。

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

**イベントがトリガーされる条件**: 台帳ダイジェストを検証したとき。

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

**イベントがトリガーされる条件**: 該当なし

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

**イベントがトリガーされる条件**: 該当なし 

## <a name="next-steps"></a>次の手順

- [Azure SQL Database および Azure Synapse Analytics の監査](auditing-overview.md)
- [Azure SQL Database 台帳の概要](ledger-overview.md)
- [クイックスタート: Azure SQL Database で台帳が有効化されたデータベースを作成する](ledger-create-a-single-database-with-ledger-enabled.md)
