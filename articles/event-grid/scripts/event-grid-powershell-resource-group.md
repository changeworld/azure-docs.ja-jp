---
title: Azure PowerShell のサンプル スクリプト - リソース グループのサブスクライブ | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - リソース グループのサブスクライブ
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: tomfitz
ms.openlocfilehash: 1a3a32003c76c0132ee0503af996e3f323a08577
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="subscribe-to-events-for-a-resource-group-with-powershell"></a>PowerShell を使用したリソース グループのイベントのサブスクライブ

このスクリプトは、リソース グループのイベントに対する Event Grid サブスクリプションを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.ps1 "Subscribe to resource group")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してイベント サブスクリプションを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmEventGridSubscription](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridsubscription) | Event Grid のサブスクリプションを作成する。 |

## <a name="next-steps"></a>次の手順

* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/get-started-azureps)をご覧ください。
