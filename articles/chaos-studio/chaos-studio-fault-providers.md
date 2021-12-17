---
title: Chaos Studio でサポートされているリソースの種類とロールの割り当て
description: サポートされているリソースの種類の一覧と、実験でそのリソースの種類で障害を実行するために割り当てる必要があるロールについて説明します。
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 10bb9b826d5c749b283dd38caa77ae03b7695b8e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091690"
---
# <a name="supported-resource-types-and-role-assignments-for-chaos-studio"></a>Chaos Studio でサポートされているリソースの種類とロールの割り当て

以下は、障害でサポートされているリソースの種類、ターゲットの種類、およびそのリソースの種類に実験の権限を付与するときに使用が推奨されるロールです。

| リソースの種類 | ターゲット名 | 割り当てが推奨されるロール |
| - | - | - |
| Microsoft.Compute/virtualMachines (エージェントベース) | Microsoft-Agent | Reader |
| Microsoft.Compute/virtualMachineScaleSets (エージェントベース) | Microsoft-Agent | Reader |
| Microsoft.Compute/virtualMachines (サービス直接) | Microsoft-VirtualMachine | Virtual Machine Contributor |
| Microsoft.Compute/virtualMachineScaleSets (サービス直接) | Microsoft-VirtualMachineScaleSet | Virtual Machine Contributor |
| Microsoft.DocumentDb/databaseAccounts (CosmosDB、サービス直接) | Microsoft-CosmosDB | Cosmos DB オペレーター |
| Microsoft.ContainerService/managedClusters (サービス直接) | Microsoft-AzureKubernetesServiceChaosMesh | Azure Kubernetes Service クラスター ユーザー ロール |
| Microsoft.Network/networkSecurityGroups (サービス直接) | Microsoft-NetworkSecurityGroup | Network Contributor |
| Microsoft.Cache/Redis (サービス直接) | Microsoft-AzureCacheForRedis | Redis Cache Contributor |
