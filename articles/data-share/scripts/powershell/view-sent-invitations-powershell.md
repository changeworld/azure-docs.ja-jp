---
title: 'PowerShell スクリプト: コンシューマーに送信された Azure Data Share の招待を一覧表示する |Microsoft Docs'
description: この PowerShell スクリプトが、コンシューマーに送信された招待を取得する方法について説明します。また、使用できるスクリプトの例を確認します。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 3f0374ba5b98c145359d18143584e23fc543c552
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985623"
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

| command | Notes |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | 送信されたデータ共有の招待を取得して一覧表示します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Data Share のその他の PowerShell サンプル スクリプトについては、[Azure Data Share の PowerShell のサンプル](../../samples-powershell.md)に関する記事をご覧ください。
