---
title: "Azure VM からの VM イメージの作成 | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで作成された既存の Azure VM から、一般化された VM イメージを作成する方法について説明します"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b6dffec166ffe8e04c5d7b701aef009bf7b72d45


---
# <a name="download-the-template-for-a-vm"></a>VM のテンプレートをダウンロードする
Azure でポータルまたは PowerShell を使用して VM を作成する際に、Resource Manager テンプレートが自動的に作成されます。 このテンプレートを使用すると、デプロイを簡単に複製できます。 テンプレートには、リソース グループ内のすべてのリソースに関する情報が含まれています。 仮想マシンの場合、これはそのリソース グループ内の VM をサポートするために作成されたすべてのもの (ネットワーク リソースなど) がテンプレートに含まれていることを意味します。

## <a name="download-the-template-using-the-portal"></a>ポータルを使用してテンプレートをダウンロードする
1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. ハブ メニューで **[Virtual Machines]** をクリックします。
3. 一覧から仮想マシンを選択します。
4. **[Automation スクリプト]**を選択します。
5. **[ダウンロード]** を選択して .zip ファイルをローカル コンピューターに保存します。
6. .zip ファイルを開き、ファイルをフォルダーに展開します。 .zip ファイルには、次のものが含まれています。
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

.json ファイルはテンプレートです。

## <a name="download-the-template-using-powershell"></a>PowerShell を使用してテンプレートをダウンロードする
.json 形式のテンプレート ファイルは、[Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) コマンドレットを使用してダウンロードすることもできます。 `-path` パラメーターを使用して、.json ファイルのファイル名とパスを指定できます。 この例では、**myResourceGroup** という名前のリソース グループのテンプレートをローカル コンピューターの **C:\users\public\downloads** フォルダーにダウンロードする方法を示します。

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>次のステップ
テンプレートを使用してリソースをデプロイする方法について詳しくは、「[Resource Manager template walkthrough](../resource-manager-template-walkthrough.md)」(Resource Manager テンプレートのチュートリアル) をご覧ください。




<!--HONumber=Nov16_HO3-->


