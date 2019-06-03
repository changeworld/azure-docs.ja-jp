---
title: Azure PowerShell スクリプト サンプル - アプリを作成してローカル Git リポジトリからデプロイする | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Web アプリの作成およびローカル Git リポジトリからのコードのデプロイ
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 80d1b295db7d1eb1e0ea0dda9522aa3875ea882d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136440"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Web アプリを作成してローカル Git リポジトリからコードをデプロイする

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、ローカル Git リポジトリから Web アプリのコードをデプロイします。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従い、Azure PowerShell を最新版に更新し、`Connect-AzAccount` を実行して、Azure との接続を作成します。 アプリケーション コードがローカル Git リポジトリにコミットしている必要もあります。

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 必要なリソース グループと App Service グループと共に Web アプリを作成します。 現在のディレクトリに Git リポジトリが含まれている場合は、`azure` リモートも追加してください。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../samples-powershell.md)のページにあります。
