---
title: 'PowerShell スクリプト: Azure DevTest Labs でラボにマーケットプレース イメージを追加する | Microsoft Docs'
description: この PowerShell スクリプトは、Azure DevTest Labs でラボにマーケットプレース イメージを追加します。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 9a65237b3eba6c9878d73148f0143f20dd60dd79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636550"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>PowerShell を使用して、Azure DevTest Labs でラボにマーケットプレース イメージを追加します。

このサンプル PowerShell スクリプトは、Azure DevTest Labs でラボにマーケットプレース イメージを追加します。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>前提条件
* **ラボ**。 スクリプトを実行するには、既存のラボが必要です。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | メモ |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | 指定したパラメーターに基づいて、リソースを検索します。 |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | リソースを取得します。 |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | リソースを変更します。 |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | リソースを作成します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Lab Services のその他の PowerShell のサンプル スクリプトについては、[Azure Lab Services の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。