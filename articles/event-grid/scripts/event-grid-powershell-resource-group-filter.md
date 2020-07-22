---
title: Azure PowerShell - リソース グループのサブスクライブ
description: この記事では、リソース グループの Event Grid イベントをサブスクライブして、特定のリソースをフィルターで抽出する方法を示したサンプル Azure PowerShell スクリプトを紹介しています。
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: ebb9b1ce729d5a2690492401bc4fd351e86f3462
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171212"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>PowerShell を使用したリソース グループのイベントのサブスクライブとリソースのフィルタリング

このスクリプトは、リソース グループのイベントに対する Event Grid サブスクリプションを作成します。 そのリソース グループ内の特定のリソースのイベントだけを取得するために、フィルターが使用されます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>サンプル スクリプト - 安定版

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events/filter-events.ps1 "Filter events")]

## <a name="sample-script---preview-module"></a>サンプル スクリプト - プレビュー モジュール

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

プレビュー版サンプル スクリプトには、Event Grid モジュールが必要です。 インストールするには、`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` を実行します

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events-preview/filter-events-preview.ps1 "Filter events")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してイベント サブスクリプションを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid のサブスクリプションを作成する。 |

## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/get-started-azureps)をご覧ください。
