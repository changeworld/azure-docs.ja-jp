---
title: 'PowerShell スクリプト: 新しい Azure Data Share を作成する'
description: この PowerShell スクリプトでは、既存の Data Share アカウント内に新しいデータ共有を作成します。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a3d4c85f5063623abd8d86304efcc3bcc375d22
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110704083"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>PowerShell を使用して Azure 内にデータ共有を作成する

この PowerShell スクリプトでは、既存の Data Share アカウント内に新しいデータ共有を作成します。

## <a name="sample-script"></a>サンプル スクリプト

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | メモ |
|---|---|
| [New-AzDataShare](/powershell/module/az.datashare/new-azdatashare) | データ共有を作成します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。

Azure Data Share のその他の PowerShell サンプル スクリプトについては、[Azure Data Share の PowerShell のサンプル](../../samples-powershell.md)に関する記事をご覧ください。
