---
title: 'PowerShell スクリプト: コンシューマーに送信された Azure Data Share の招待を一覧表示する'
description: この PowerShell スクリプトが、コンシューマーに送信された招待を取得する方法について説明します。また、使用できるスクリプトの例を確認します。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0b39c31271a0382cce3388e9c8176ba943bc43e6
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703880"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>PowerShell を使用してデータ共有の招待を取得する

この PowerShell スクリプトでは、コンシューマーに送信される招待を取得します。

## <a name="sample-script"></a>サンプル スクリプト
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | Notes |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | 送信されたデータ共有の招待を取得して一覧表示します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。

Azure Data Share のその他の PowerShell サンプル スクリプトについては、[Azure Data Share の PowerShell のサンプル](../../samples-powershell.md)に関する記事をご覧ください。
