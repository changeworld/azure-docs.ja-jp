---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 70128735aef64d273c63236a13b0ae28edb6077d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003776"
---
### <a name="sql-databases"></a>[SQL データベース]

|  |  |
|---------|---------|
| [許可された SQL DB の SKU](../articles/governance/policy/samples/allowed-sql-db-skus.md) | 承認された SKU が SQL データベースで使用される必要があります。 許可された SKU ID の配列または許可された SKU 名の配列を指定します。 |
| [DB レベルの脅威検出設定の監査](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | SQL データベース セキュリティ アラート ポリシーが特定の状態に設定されていない場合、このポリシーの監査を行います。 脅威検出を有効または無効のどちらにするかを指示する値を指定します。  |
| [SQL Database の暗号化の監査](../articles/governance/policy/samples/sql-database-encryption-audit.md) | SQL Database で Transparent Data Encryption が有効になっていないかどうかを監査します。 |
| [SQL DB レベルの監査設定の監査](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | SQL データベースの監査設定が指定された設定と一致しない場合に、この設定の監査を行います。 監査設定を有効または無効のどちらにするべきかを指示する値を指定します。  |
| [Transparent Data Encryption の状態の監査](../articles/governance/policy/samples/audit-trans-data-enc-status.md) | SQL データベースの Transparent Data Encryption が有効ではない場合にその監査を行います。  |