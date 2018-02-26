---
title: "Azure Stack で PowerShell を使用してテンプレートをデプロイする | Microsoft Docs"
description: "Resource Manager テンプレートと PowerShell を使用して仮想マシンをデプロイする方法について説明します。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: d271b155d65a7dd95a92262da338cf3a272d140b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>PowerShell を使った Azure Stack でのテンプレートのデプロイ

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

PowerShell を使用して、Azure Resource Manager テンプレートを Azure Stack Development Kit にデプロイします。  Resource Manager テンプレートは、お使いのアプリケーションのすべてのリソースを、単一の連携した操作でデプロイしてプロビジョニングします。

## <a name="run-azurerm-powershell-cmdlets"></a>Azure PowerShell コマンドレットの実行
この例では、Resource Manager テンプレートを使って、スクリプトを実行して仮想マシンを Azure Stack Development Kit にデプロイします。  先に進む前に、[PowerShell が構成済み](azure-stack-powershell-configure-user.md)であることを確認します。  

このサンプル テンプレートで使用している VHD は、WindowsServer-2012-R2-Datacenter です。

1. <http://aka.ms/AzureStackGitHub> にアクセスし、**101-simple-windows-vm** テンプレートを検索して、c:\\templates\\azuredeploy-101-simple-windows-vm.json に保存します。
2. PowerShell で、次のデプロイ スクリプトを実行します。 *username* と *password* は自分のユーザー名とパスワードに変更します。 次に使用するときは、*$myNum* パラメーターの値を増分してデプロイが上書きされないようにします。
   
   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
       New-AzureRmResourceGroupDeployment `
           -Name myDeployment$myNum `
           -ResourceGroupName $RGName `
           -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
           -NewStorageAccountName mystorage$myNum `
           -DnsNameForPublicIP mydns$myNum `
           -AdminUsername <username> `
           -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
           -VmName myVM$myNum `
           -WindowsOSVersion 2012-R2-Datacenter
   ```
3. Azure Stack ポータルを開き、**[参照]**、**[仮想マシン]** の順にクリックして、新しい仮想マシン (*myDeployment001*) を探します。


## <a name="next-steps"></a>次の手順
[Visual Studio を使用したテンプレートのデプロイ](azure-stack-deploy-template-visual-studio.md)

