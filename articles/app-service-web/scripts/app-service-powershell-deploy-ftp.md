---
title: "Azure PowerShell のサンプル スクリプト - FTP を使用した Web アプリへのファイルのアップロード | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - FTP を使用した Web アプリへのファイルのアップロード"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 5f3bf8087fa6601ae410d6db907471fd6faa2742
ms.lasthandoff: 03/10/2017

---

# <a name="upload-files-to-a-web-app-using-ftp"></a>FTP を使用して Web アプリにファイルをアップロードする

このサンプル スクリプトでは、Azure PowerShell を使用して次の操作を行います。 

* 西ヨーロッパ Azure リージョンの Azure App Service で Web アプリを作成する。
* Web アプリから FTP の接続情報を取得する。
* FTP を使用して ([WebClient.UploadFile()](https://msdn.microsoft.com/library/ms144229.aspx) を介して) Web アプリ コードをデプロイする。

## <a name="prerequisites"></a>前提条件

* `Login-AzureRmAccount` を実行して Azure にログインします。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "FTP を使用して Web アプリにファイルをアップロードする")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzureRmResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | App Service プランを作成します。 |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Web アプリを作成します。 |
| [Get-AzureRmWebAppPublishingProfile](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/get-azurermwebapppublishingprofile) | Web アプリの発行プロファイルを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../app-service-powershell-samples.md)のページにあります。

