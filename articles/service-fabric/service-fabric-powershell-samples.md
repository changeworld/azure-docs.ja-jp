---
title: "Azure PowerShell のサンプル - Service Fabric | Microsoft Docs"
description: "Azure PowerShell のサンプル - Service Fabric"
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: 
tags: 
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 1825b2a58e1022f22c71395477a5fca54c715455
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2017
---
# <a name="azure-powershell-samples"></a>Azure PowerShell のサンプル

次の表には、Service Fabric クラスター、アプリケーション、およびサービスを作成して管理する PowerShell のサンプル スクリプトへのリンクが含まれています。

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **クラスターの作成** ||
| [クラスターの作成 (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Azure Service Fabric クラスターを作成します。 |
|[テスト クラスターの作成 (Azure)](./scripts/service-fabric-powershell-create-test-cluster.md)| Azure 上で 3 ノードのテスト Service Fabric クラスターを作成します。|
| **クラスター、ノード、およびインフラストラクチャの管理** ||
| [アプリケーション証明書の追加](./scripts/service-fabric-powershell-add-application-certificate.md)| クラスター内のすべてのノードにアプリケーションの X.509 証明書を追加します。 |
| [クラスター VM の RDP ポート範囲の更新](./scripts/service-fabric-powershell-change-rdp-port-range.md)|展開されたクラスター内にあるクラスター ノード VM の RDP ポート範囲を変更します。|
| [クラスター ノード VM の管理者ユーザー名とパスワードの更新](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | クラスター ノード VM の管理者ユーザー名とパスワードを更新します。 |
| [ロード バランサーでポートを開く](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Azure ロード バランサーの特定のポートで受信トラフィックを許可するため、アプリケーション ポートを開きます。 |
| [受信ネットワーク セキュリティ グループ ルールの作成](./scripts/service-fabric-powershell-add-nsg-rule.md) | 特定のポートで、クラスターへの着信トラフィックを許可する受信ネットワーク セキュリティ グループ ルールを作成します。 |
| **アプリケーションの管理** ||
| [アプリケーションをデプロイする](./scripts/service-fabric-powershell-deploy-application.md)| クラスターにアプリケーションをデプロイします。|
| [アプリケーションのアップグレード](./scripts/service-fabric-powershell-upgrade-application.md)| アプリケーションをアップグレードします。|
| [アプリケーションの削除](./scripts/service-fabric-powershell-remove-application.md)| クラスターからアプリケーションを削除します。|
