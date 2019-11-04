---
title: Azure PowerShell スクリプトのサンプル - ネットワーク セキュリティ グループ ルールの追加 | Microsoft Docs
description: Azure PowerShell スクリプトのサンプル - 特定のポートで受信トラフィックを許可するネットワーク セキュリティ グループを追加します。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 44bb0e615453450c401949f0ce76f15cb82fab67
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680545"
---
# <a name="add-an-inbound-network-security-group-rule"></a>受信ネットワーク セキュリティ グループ ルールの追加

このサンプル スクリプトは、ポート 8081 で受信トラフィックを許可するネットワーク セキュリティ グループ ルールを作成します。  このスクリプトでは、ネットワーク セキュリティ グループを取得し、新しいネットワーク セキュリティ構成ルールを作成し、ネットワーク セキュリティ グループを更新します。 必要に応じてパラメーターをカスタマイズします。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールします。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | `Microsoft.Network/networkSecurityGroups` リソースを取得します。 |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| ネットワーク セキュリティ グループを名前を条件に取得します。|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| ネットワーク セキュリティ グループにネットワーク セキュリティ ルールの構成を追加します。 |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| ネットワーク セキュリティ グループの目標とする状態を設定します。|

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。
