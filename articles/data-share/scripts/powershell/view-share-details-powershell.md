---
title: 'PowerShell スクリプト: Azure Data Share の既存の共有を一覧表示する'
description: この PowerShell スクリプトでは、共有の詳細を一覧表示します。
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2692cbd836b351e5c41dfe40168b365885997ac0
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703887"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>PowerShell を使用して、送信されたデータ共有の詳細を表示する

この PowerShell スクリプトでは、既存のアカウントからのデータ共有を一覧表示し、特定の共有の詳細を取得します。


## <a name="sample-script"></a>サンプル スクリプト

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | Notes |
|---|---|
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare) | アカウント内の共有を取得し一覧表示します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。

Azure Data Share のその他の PowerShell サンプル スクリプトについては、[Azure Data Share の PowerShell のサンプル](../../samples-powershell.md)に関する記事をご覧ください。
