---
title: Azure PowerShell スクリプトのサンプル - RDP のユーザー名とパスワードの更新 | Microsoft Docs
description: Azure PowerShell スクリプトのサンプル - 特定のノード型のすべての Service Fabric クラスター ノードの RDP ユーザー名とパスワードを更新します。
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
ms.openlocfilehash: ff9cfabc4ac7b759a916ddaaeb3f4c95ceecd452
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>クラスター内の VM の管理者のユーザー名とパスワードを更新します。

Service Fabric クラスター内の各[ノード型](../service-fabric-cluster-nodetypes.md)は仮想マシン スケール セットです。 このサンプル スクリプトは、特定のノード型のクラスター仮想マシンの管理者のユーザー名およびパスワードを更新します。  管理者のパスワードは変更可能なスケール セット プロパティではないため、スケール セットに VMAccessAgent 拡張機能を追加します。  ユーザー名とパスワードの変更は、スケール セット内のすべてのノードに適用されます。 必要に応じてパラメーターをカスタマイズします。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールします。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用します。表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | クラスター ノード型 (仮想マシン スケール セット) のプロパティの取得   |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension)| 仮想マシン スケール セットに拡張機能を追加します。|
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)|仮想マシン スケール セットの状態を、ローカル VMSS オブジェクトの状態に更新します。|

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure Service Fabric 用 Azure PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
