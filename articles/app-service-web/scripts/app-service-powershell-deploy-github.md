---
title: "Azure PowerShell のサンプル スクリプト - Web アプリの作成および GitHub からのコードのデプロイ | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - Web アプリの作成および GitHub からのコードのデプロイ"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 7ee385060bbb3800e8a37f2ae4ba7d06fb31b521
ms.lasthandoff: 03/10/2017

---

# <a name="create-a-web-app-and-deploy-code-from-github"></a>Web アプリを作成して GitHub からコードをデプロイする

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、(継続的なデプロイは行わずに) パブリックの GitHub リポジトリから Web アプリのコードをデプロイします。 継続的なデプロイを使用した GitHub でのデプロイについては、「[GitHub からの継続的なデプロイで Web アプリを作成する](app-service-powershell-continuous-deployment-github.md)」を参照してください。

このスクリプトを実行する前に、Azure との接続を `Login-AzureRmAccount` コマンドレットを使用して作成していること、およびデプロイ先になる GitHub リポジトリの URL を用意していることを確認してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-github/deploy-github.ps1?highlight=1-2 "Web アプリの作成および GitHub からのコードのデプロイ")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | App Service プランを作成します。 |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Web アプリを作成します。 |
| [Set-AzureRmResource](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/set-azurermresource) | リソース グループのリソースを変更します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../app-service-powershell-samples.md)のページにあります。

