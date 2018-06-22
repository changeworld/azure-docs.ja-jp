---
title: 'PowerShell スクリプト: Azure Lab Services で VHD ファイルからカスタム イメージを作成する | Microsoft Docs'
description: この PowerShell スクリプトは、Azure Lab Services で VHD ファイルからカスタム イメージを作成します。
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
ms.openlocfilehash: 19b7c3c6018ec56b056761c336bc56c8b63b47a2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636404"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>PowerShell を使用して、Azure Lab Services で VHD ファイルからカスタム イメージを作成する

この PowerShell のサンプル スクリプトは、Azure Lab Services で VHD ファイルからカスタム イメージを作成します。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>前提条件
* **ラボ** 。 スクリプトを実行するには、既存のラボが必要です。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | メモ |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | リソースを取得します。 |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Azure ストレージ アカウントのアクセス キーを取得します。 |
| [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) | リソース グループに Azure デプロイを追加します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Lab Services のその他の PowerShell のサンプル スクリプトについては、[Azure Lab Services の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。