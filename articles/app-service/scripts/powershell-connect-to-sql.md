---
title: PowerShell:SQL Database への接続
description: Azure PowerShell を使用して App Service のデプロイと管理を自動化する方法について説明します。 このサンプルでは、SQL Database にアプリを接続する方法を紹介しています。
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 9086e00e4b6caf89ab249bbf25ca03a6f068ba49
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85248299"
---
# <a name="connect-an-app-service-app-to-sql-database"></a>App Service アプリを SQL Database に接続する

このシナリオでは、Azure SQL Database のデータベースと App Service アプリの作成方法について説明します。 作成後、アプリの設定を使用してデータベースをアプリにリンクします。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Connect-AzAccount` を実行して、Azure との接続を作成します。

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to SQL Database")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、App Service アプリ、すべての関連リソースなどが削除できます。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service プランを作成します。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | App Service アプリを作成します。 |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | サーバーを作成します。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | サーバーレベルのファイアウォール規則を作成します。 |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | データベースまたはエラスティック データベースを作成します。 |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | App Service アプリの構成を変更します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure App Service 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../samples-powershell.md)のページにあります。
