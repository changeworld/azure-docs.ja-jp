---
title: Log Analytics ワークスペースを作成する - Azure PowerShell
description: Azure PowerShell のサンプル スクリプト - Log Analytics ワークスペースを作成する
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: 62b02de5d1c08f6047052d71e3be420cceb1c5c0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80054640"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>PowerShell を使用して Log Analytics ワークスペースを作成する

このスクリプトを使用すると、Azure Log Analytics ワークスペースを短時間で作成して稼働させることができます。このワークスペースは、データの収集、分析、および対応を開始する場合は必要です。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、サブスクリプション内に新しい Log Analytics ワークスペースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | 既存のワークスペースに関する情報を取得します。 |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | 指定されたリソース グループと場所にワークスペースを作成します。 |


## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

