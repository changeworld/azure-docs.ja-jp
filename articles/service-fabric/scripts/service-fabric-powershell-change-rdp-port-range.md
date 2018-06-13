---
title: Azure PowerShell のサンプル スクリプト - RDP ポート範囲の変更 | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - デプロイしたクラスターの RDP ポート範囲を変更します。
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 83fb6cc03f605a60b06f31fa6ddd82cd4e3e899e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "30180189"
---
# <a name="update-the-rdp-port-range-values"></a>RDP ポート範囲の値の更新

このサンプル スクリプトは、クラスターをデプロイした後に、クラスター ノードの VM の RDP ポート範囲の値を変更します。  基になる VM が循環しないように、Azure PowerShell を使用します。  スクリプトはクラスターのリソース グループ内で `Microsoft.Network/loadBalancers` リソースを取得し、`inboundNatPools.frontendPortRangeStart` と `inboundNatPools.frontendPortRangeEnd` の値を更新します。 必要に応じてパラメーターをカスタマイズします。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールします。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | `Microsoft.Network/loadBalancers` リソースを取得します。 |
|[Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource)|`Microsoft.Network/loadBalancers` リソースを更新します。|

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure Service Fabric 用 Azure PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
