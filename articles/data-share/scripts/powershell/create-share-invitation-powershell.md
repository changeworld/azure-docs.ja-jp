---
title: 'PowerShell スクリプト: Azure Data Share の招待を作成する'
description: この PowerShell スクリプトでは、データ共有の招待を送信します。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f6d9a4885e5281db9339a1529fd35381430d788c
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110704071"
---
# <a name="use-a-powershell-script-to-monitor-the-usage-of-a-sent-data-share"></a>送信されたデータ共有の使用状況を監視するために PowerShell スクリプトを使用する

この PowerShell スクリプトでは、データ共有の招待を作成します。

## <a name="sample-script"></a>サンプル スクリプト


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | Notes |
|---|---|
| [New-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation) | データ共有の招待を作成します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。

Azure Data Share のその他の PowerShell サンプル スクリプトについては、[Azure Data Share の PowerShell のサンプル](../../samples-powershell.md)に関する記事をご覧ください。
