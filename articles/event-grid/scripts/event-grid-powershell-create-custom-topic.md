---
title: Azure PowerShell のサンプル スクリプト - カスタム トピックの作成 | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - カスタム トピックの作成
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
ms.openlocfilehash: 2948f1eabe93d092d905965429ff73eaa6c443cd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>PowerShell を使用した Event Grid のカスタム トピックの作成

このスクリプトは、Event Grid のカスタム トピックを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してカスタム トピックを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmEventGridTopic](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridtopic) | Event Grid のカスタム トピックを作成します。 |

## <a name="next-steps"></a>次の手順

* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/get-started-azureps)をご覧ください。
