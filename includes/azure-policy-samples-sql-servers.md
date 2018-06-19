---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664593"
---
### <a name="sql-servers"></a>SQL Server

|  |  |
|---------|---------|
| [Azure Active Directory 管理者不在の監査](../articles/azure-policy/scripts/audit-no-aad-admin.md) | Azure Active Directory 管理者が SQL Server に割り当てられていない場合に、監査を行います。 |
| [サーバー レベルの脅威検出設定の監査](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) | SQL データベース セキュリティ アラート ポリシーが特定の状態に設定されていない場合、このポリシーの監査を行います。 脅威検出を有効または無効のどちらにするかを指示する値を指定します。  |
| [SQL Server の監査設定の監査](../articles/azure-policy/scripts/sql-server-audit.md) | 監査設定が有効になっているかどうかに基づいて SQL Server を監査します。 |
| [SQL Server レベルの監査設定の監査](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) | SQL Server の監査設定が指定された設定と一致しない場合に、この設定の監査を行います。 監査設定を有効または無効のどちらにするべきかを指示する値を指定します。 |
| [SQL Server バージョン 12.0 が必要](../articles/azure-policy/scripts/req-sql-12.md) | SQL Server でバージョン 12.0 を使用する必要があります。  |