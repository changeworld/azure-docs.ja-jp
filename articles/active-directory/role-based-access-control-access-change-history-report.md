<properties
	pageTitle="アクセス変更履歴レポートの作成 | Microsoft Azure"
	description="ロールベースのアクセス制御を使用した Azure サブスクリプションへのアクセス権の変更について、過去 90 日間分をすべて一覧表示するレポートを生成します。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="04/28/2016"
	ms.author="kgremban"/>

# アクセス変更履歴レポートの作成

Azure サブスクリプション、またはそこに含まれているリソースやリソース グループの所有者が他にもいる場合は、アクセス権の変更をすべて追跡できるようにしておく必要があります。サブスクリプション内でだれかがアクセス権を付与したり取り消したりしたときは、その変更が常に Azure イベントに記録されます。アクセス変更履歴レポートを作成すると、過去 90 日間のすべての変更を確認できます。

## Azure PowerShell を使用したレポートの作成
PowerShell でアクセス変更履歴レポートを作成するには、次のコマンドを使用します。

```
Get-AzureRMAuthorizationChangeLog
```

表示する割り当てのプロパティを指定できます。プロパティの例を次に示します。

| プロパティ | 説明 |
| -------- | ----------- |
| **アクション** | アクセス権が付与されているか取り消されているか |
| **Caller** | アクセス権の変更を担当する所有者 |
| **日付** | アクセス権が変更された日時 |
| **DirectoryName** | Azure Active Directory ディレクトリ |
| **PrincipalName** | ユーザー、グループ、またはアプリケーションの名前 |
| **PrincipalType** | 割り当てはユーザー、グループ、アプリケーションのどれか |
| **RoleId** | 付与または取り消されたロールの GUID |
| **RoleName** | 付与または取り消されたロール |
| **ScopeName** | サブスクリプション、リソース グループ、またはリソースの名前 |
| **ScopeType** | 割り当てはサブスクリプション、リソース グループ、またはリソース スコープのどのレベルで行われたか |
| **SubscriptionId** | Azure サブスクリプションの GUID |
| **SubscriptionName** | Azure サブスクリプションの名前 |

このコマンド例を実行すると、サブスクリプションにおける過去 7 日間のすべてのアクセス変更が一覧表示されます。

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - スクリーンショット](./media/role-based-access-control-configure/access-change-history.png)

## Azure CLI を使用したレポートの作成
Azure コマンド ライン インターフェイス (CLI) でアクセス変更履歴レポートを作成するには、次のコマンドを使用します。
```
azure authorization changelog
```

## スプレッドシートへのエクスポート
レポートを保存したりデータを操作したりするには、アクセス変更を .csv ファイルにエクスポートします。このレポートをスプレッドシートで表示して確認することができます。

![スプレッドシートとして表示した Changelog - スクリーンショット](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## 関連項目
- [Azure のロールベースのアクセス制御](role-based-access-control-configure.md)の基本事項の確認
- [Azure RBAC のカスタム ロール](role-based-access-control-custom-roles.md)の操作

<!---HONumber=AcomDC_0504_2016-->