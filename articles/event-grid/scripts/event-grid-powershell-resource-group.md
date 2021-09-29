---
title: Azure PowerShell のサンプル スクリプト - リソース グループのサブスクライブ | Microsoft Docs
description: この記事では、リソース グループの Event Grid イベントをサブスクライブする方法を示したサンプル Azure PowerShell スクリプトを紹介しています。
ms.devlang: powershell
ms.topic: sample
ms.date: 09/15/2021
ms.openlocfilehash: 317d712a5bdaf3532e31408dc8980eab854173b6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612018"
---
# <a name="subscribe-to-events-for-a-resource-group-with-powershell"></a>PowerShell を使用したリソース グループのイベントのサブスクライブ

このスクリプトは、リソース グループのイベントに対する Event Grid サブスクリプションを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

プレビュー版サンプル スクリプトには、Event Grid モジュールが必要です。 インストールするには、`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` を実行します

## <a name="sample-script---stable"></a>サンプル スクリプト - 安定版

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.ps1 "Subscribe to resource group")]

## <a name="sample-script---preview-module"></a>サンプル スクリプト - プレビュー モジュール

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.ps1 "Subscribe to resource group")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してイベント サブスクリプションを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid のサブスクリプションを作成する。 |

## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](/powershell/azure/get-started-azureps)をご覧ください。
