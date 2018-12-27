---
title: 'PowerShell スクリプト: Azure Lab Services で許可される VM のサイズを設定する | Microsoft Docs'
description: この PowerShell スクリプトは、Azure Lab Services で許可される VM のサイズを設定します。
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
ms.openlocfilehash: 559e74675a5d113584dca21979c20462c9cdf19c
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054708"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>PowerShell を使用して Azure Lab Services で許可される VM のサイズを設定する

この PowerShell のサンプル スクリプトは、Azure Lab Services で許可される仮想マシン (VM) のサイズを設定します。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>前提条件
* **ラボ** 。 スクリプトを実行するには、既存のラボが必要です。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | メモ |
|---|---|
| [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) | 指定したパラメーターに基づいて、リソースを検索します。 |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | リソースを取得します。 |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | リソースを変更します。 |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | リソースを作成します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Lab Services のその他の PowerShell のサンプル スクリプトについては、[Azure Lab Services の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。
