---
title: Azure Stack で PowerShell を使用してテンプレートをデプロイする | Microsoft Docs
description: PowerShell を使用して Azure Stack にテンプレートをデプロイします。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 445628679a09a1884f63cdce446adec476af39af
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "41946619"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>PowerShell を使用して Azure Stack にテンプレートをデプロイする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

PowerShell を使用して、Azure Resource Manager テンプレートを Azure Stack にデプロイできます。 この記事では、PowerShell を使用してテンプレートをデプロイする方法を示します。

## <a name="run-azurerm-powershell-cmdlets"></a>Azure PowerShell コマンドレットの実行

この例では、AzureRM PowerShell コマンドレットと GitHub に格納されているテンプレートを使用します。 テンプレートにより、Windows Server 2012 R2 Datacenter の仮想マシンが作成されます。

>[!NOTE]
>この例を試す前に、Azure Stack ユーザーに [PowerShell が構成されている](azure-stack-powershell-configure-user.md)ことを確認します。

1. <http://aka.ms/AzureStackGitHub> に移動して、**101-simple-windows-vm** テンプレートを探します。 テンプレートを C:\\templates\\azuredeploy-101-simple-windows-vm.json に保存します。
2. 管理者特権の PowerShell コマンド プロンプトを開きます。
3. 次のスクリプト内の *username* と *password* を自分のユーザー名とパスワードに置き換えてから、スクリプトを実行します。

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

   >[!IMPORTANT]
   >このスクリプトを実行するたびに、"$myNum" パラメーターの値を増分してデプロイが上書きされないようにします。

4. Azure Stack ポータルを開き、**[参照]**、**[仮想マシン]** の順に選択して、新しい仮想マシン (*myDeployment001*) を探します。

## <a name="next-steps"></a>次の手順

[Visual Studio を使用したテンプレートのデプロイ](azure-stack-deploy-template-visual-studio.md)
