---
title: 'PowerShell スクリプト: Azure Data Share からの招待を受け入れる'
description: この PowerShell スクリプトでは、既存のデータ共有からの招待を受け入れます。
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ce2a315512af65147fa2354c5895bcff94710cf
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697154"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>PowerShell を使用してデータ共有の招待を受け入れる

この PowerShell スクリプトでは、コンシューマーに送信される招待を受け入れます。

## <a name="sample-script"></a>サンプル スクリプト
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | Notes |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | 送信されたデータ共有の招待を取得して一覧表示します。 |
| [New-AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription) | データ共有サブスクリプションを作成します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。

Azure Data Share のその他の PowerShell サンプル スクリプトについては、[Azure Data Share の PowerShell のサンプル](../../samples-powershell.md)に関する記事をご覧ください。
