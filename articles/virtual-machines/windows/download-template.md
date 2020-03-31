---
title: Azure VM のテンプレートをダウンロードする
description: Resource Manager デプロイ モデルでのデプロイの自動化に役立つ、VM のテンプレートをダウンロードします。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: c73026515f0d7fde4e2f82838696700b1bb17c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033545"
---
# <a name="download-the-template-for-a-vm"></a>VM のテンプレートをダウンロードする
Azure でポータルまたは PowerShell を使用して VM を作成する際に、Resource Manager テンプレートが自動的に作成されます。 このテンプレートを使用すると、デプロイを簡単に複製できます。 テンプレートには、リソース グループ内のすべてのリソースに関する情報が含まれています。 仮想マシンの場合、これはそのリソース グループ内の VM をサポートするために作成されたすべてのもの (ネットワーク リソースなど) がテンプレートに含まれていることを意味します。

## <a name="download-the-template-using-the-portal"></a>ポータルを使用してテンプレートをダウンロードする
1. [Azure Portal](https://portal.azure.com/) にログインします。
2. 左側のメニューで **[Virtual Machines]** を選択します。
3. 一覧から仮想マシンを選択します。
4. **[テンプレートのエクスポート]** を選択します。
5. 上部のメニューから **[ダウンロード]** を選択して .zip ファイルをローカル コンピューターに保存します。
6. .zip ファイルを開き、ファイルをフォルダーに展開します。 .zip ファイルには以下が含まれています。
   
   * parameters.json
   * template.json

template.json ファイルはテンプレートです。

## <a name="download-the-template-using-powershell"></a>PowerShell を使用してテンプレートをダウンロードする
.json 形式のテンプレート ファイルは、[Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) コマンドレットを使用してダウンロードすることもできます。 `-path` パラメーターを使用して、.json ファイルのファイル名とパスを指定できます。 この例では、**myResourceGroup** という名前のリソース グループのテンプレートをローカル コンピューターの **C:\users\public\downloads** フォルダーにダウンロードする方法を示します。

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>次のステップ
テンプレートを使用してリソースをデプロイする方法について詳しくは、「[Resource Manager template walkthrough](../../azure-resource-manager/resource-manager-template-walkthrough.md)」(Resource Manager テンプレートのチュートリアル) をご覧ください。

