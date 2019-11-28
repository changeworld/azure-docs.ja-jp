---
title: Azure PowerShell のサンプル - Service Fabric | Microsoft Docs
description: Azure PowerShell のサンプル - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: cae88e142c3bca15e837db7f084eef68434a78ca
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903264"
---
# <a name="azure-powershell-samples"></a>Azure PowerShell のサンプル

次の表には、Service Fabric クラスター、アプリケーション、およびサービスを作成して管理する PowerShell のサンプル スクリプトへのリンクが含まれています。

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **クラスターの作成** ||
| [クラスターの作成 (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Azure Service Fabric クラスターを作成します。 |
| **クラスター、ノード、およびインフラストラクチャの管理** ||
| [アプリケーション証明書の追加](./scripts/service-fabric-powershell-add-application-certificate.md)| Key Vault に X509 証明書を作成して、クラスター内の仮想マシン スケール セットにデプロイします。 |
| [クラスター VM の RDP ポート範囲の更新](./scripts/service-fabric-powershell-change-rdp-port-range.md)|展開されたクラスター内にあるクラスター ノード VM の RDP ポート範囲を変更します。|
| [クラスター ノード VM の管理者ユーザー名とパスワードの更新](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | クラスター ノード VM の管理者ユーザー名とパスワードを更新します。 |
| [ロード バランサーでポートを開く](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Azure ロード バランサーの特定のポートで受信トラフィックを許可するため、アプリケーション ポートを開きます。 |
| [受信ネットワーク セキュリティ グループ ルールの作成](./scripts/service-fabric-powershell-add-nsg-rule.md) | 特定のポートで、クラスターへの着信トラフィックを許可する受信ネットワーク セキュリティ グループ ルールを作成します。 |
| **アプリケーションの管理** ||
| [アプリケーションをデプロイする](./scripts/service-fabric-powershell-deploy-application.md)| クラスターにアプリケーションをデプロイします。|
| [アプリケーションのアップグレード](./scripts/service-fabric-powershell-upgrade-application.md)| アプリケーションをアップグレードします。|
| [アプリケーションの削除](./scripts/service-fabric-powershell-remove-application.md)| クラスターからアプリケーションを削除します。|
