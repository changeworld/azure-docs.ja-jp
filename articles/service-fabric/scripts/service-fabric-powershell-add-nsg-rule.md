---
title: Azure PowerShell スクリプトのサンプル - ネットワーク セキュリティ グループ ルールの追加 | Microsoft Docs
description: Azure PowerShell スクリプトのサンプル - 特定のポートで受信トラフィックを許可するネットワーク セキュリティ グループを追加します。
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
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
ms.locfileid: "25987193"
---
# <a name="add-an-inbound-network-security-group-rule"></a>受信ネットワーク セキュリティ グループ ルールの追加

このサンプル スクリプトは、ポート 8081 で受信トラフィックを許可するネットワーク セキュリティ グループ ルールを作成します。  このスクリプトは、クラスターがある `Microsoft.Network/networkSecurityGroups` リソースを取得し、新しいネットワーク セキュリティ構成ルールを作成し、ネットワーク セキュリティ グループを更新します。 必要に応じてパラメーターをカスタマイズします。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールします。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | `Microsoft.Network/networkSecurityGroups` リソースを取得します。 |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| ネットワーク セキュリティ グループを名前を条件に取得します。|
|[Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| ネットワーク セキュリティ グループにネットワーク セキュリティ ルールの構成を追加します。 |
|[Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| ネットワーク セキュリティ グループの目標とする状態を設定します。|

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。
