---
title: "アクセス レポート - Azure RBAC | Microsoft Docs"
description: "ロールベースのアクセス制御を使用した Azure サブスクリプションへのアクセス権の変更について、過去 90 日間分をすべて一覧表示するレポートを生成します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: bb894c38de63d0eac4066eeabaed7ba791021cc4
ms.openlocfilehash: bb15538a37fd610207c77fe2cf89fbfa16bbba11
ms.lasthandoff: 02/18/2017


---
# <a name="create-an-access-change-history-report"></a>アクセス変更履歴レポートの作成
サブスクリプション内でだれかがアクセス権を付与したり取り消したりしたときは、その変更が常に Azure イベントに記録されます。 アクセス変更履歴レポートを作成すると、過去 90 日間のすべての変更を確認できます。

## <a name="create-a-report-with-azure-powershell"></a>Azure PowerShell を使用したレポートの作成
PowerShell でアクセス変更履歴レポートを作成するには、 `Get-AzureRMAuthorizationChangeLog` コマンドを使用します。 このコマンドレットの詳細は、 [PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1)で確認できます。

このコマンドを呼び出すには、表示する割り当てのプロパティを指定します。プロパティの例を次に示します。

| プロパティ | Description |
| --- | --- |
| **アクション** |アクセス権が付与されているか取り消されているか |
| **Caller** |アクセス権の変更を担当する所有者 |
| **日付** |アクセス権が変更された日時 |
| **DirectoryName** |Azure Active Directory ディレクトリ |
| **PrincipalName** |ユーザー、グループ、またはアプリケーションの名前 |
| **PrincipalType** |割り当てはユーザー、グループ、アプリケーションのどれか |
| **RoleId** |付与または取り消されたロールの GUID |
| **RoleName** |付与または取り消されたロール |
| **ScopeName** |サブスクリプション、リソース グループ、またはリソースの名前 |
| **ScopeType** |割り当てはサブスクリプション、リソース グループ、またはリソース スコープのどのレベルで行われたか |
| **SubscriptionId** |Azure サブスクリプションの GUID |
| **SubscriptionName** |Azure サブスクリプションの名前 |

このコマンド例を実行すると、サブスクリプションにおける過去&7; 日間のすべてのアクセス変更が一覧表示されます。

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - スクリーンショット](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Azure CLI を使用したレポートの作成
Azure コマンド ライン インターフェイス (CLI) でアクセス変更履歴レポートを作成するには、 `azure role assignment changelog list` コマンドを使用します。

## <a name="export-to-a-spreadsheet"></a>スプレッドシートへのエクスポート
レポートを保存したりデータを操作したりするには、アクセス変更を .csv ファイルにエクスポートします。 このレポートをスプレッドシートで表示して確認することができます。

![スプレッドシートとして表示した Changelog - スクリーンショット](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="next-steps"></a>次のステップ
* [Azure RBAC のカスタム ロール](role-based-access-control-custom-roles.md)
* [PowerShell を使用して RBAC を管理する](role-based-access-control-manage-access-powershell.md)方法を確認します。


