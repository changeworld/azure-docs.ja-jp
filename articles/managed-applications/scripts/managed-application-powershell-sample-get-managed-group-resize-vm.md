---
title: Azure PowerShell スクリプトのサンプル - マネージド リソース グループを取得し、VM のサイズを変更する | Microsoft Docs
description: Azure PowerShell スクリプトのサンプル - マネージド リソース グループを取得し、VM のサイズを変更する
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: baad12379c01ac955f1b0e036398e5889f3681b2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203200"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>PowerShell でマネージド リソース グループ内のリソースを取得し、VM のサイズを変更する

このスクリプトはマネージド リソース グループからリソースを取得し、そのリソース グループ内の VM のサイズを変更します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは次のコマンドを使用してマネージド アプリケーションをデプロイします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermmanagedapplication) | マネージド アプリケーションの一覧を表示します。 リソース グループの名前を指定して結果にフォーカスします。 |
| [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresource) | リソースの一覧を表示します。 リソース グループとリソースの種類を指定して結果にフォーカスします。 |
| [Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) | 仮想マシンのサイズを更新します。 |


## <a name="next-steps"></a>次の手順

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/get-started-azureps)をご覧ください。
