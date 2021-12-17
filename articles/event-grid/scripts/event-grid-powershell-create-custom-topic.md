---
title: Azure PowerShell のサンプル スクリプト - カスタム トピックの作成 | Microsoft Docs
description: この記事では、Event Grid のカスタム トピックの作成方法を示したサンプル Azure PowerShell スクリプトを紹介しています。
services: event-grid
documentationcenter: na
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2021
ms.openlocfilehash: 86ecab935209a21fbb07ae670b338f85ca455deb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680473"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>PowerShell を使用した Event Grid のカスタム トピックの作成

このスクリプトは、Event Grid のカスタム トピックを作成します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してカスタム トピックを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzEventGridTopic](/powershell/module/az.eventgrid/new-azeventgridtopic) | Event Grid のカスタム トピックを作成します。 |

## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](/powershell/azure/get-started-azureps)をご覧ください。
