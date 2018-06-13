---
title: Azure PowerShell のサンプル スクリプト - Service Fabric クラスターの作成 | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - 3 ノードのテスト Service Fabric クラスターを作成します。
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 169f68d179c7f895078fe649d0e2a69e58d148cb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
ms.locfileid: "31597505"
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>3 ノードのテスト Service Fabric クラスターを作成する

このサンプル スクリプトでは、X.509 証明書で保護される 3 ノードのテスト Service Fabric クラスターを作成します。 3 ノードのクラスター構成は、開発/テストでサポートされます。その理由は、(アップグレードや個々のノードの障害が同時に発生しない限り) アップグレードを安全に実行でき、個々のノードの障害にも対応できるためです。 運用環境クラスターでは、同時に複数の障害が発生した場合の回復性のために 5 つ以上のノードが必要です。  

このコマンドを実行すると、自己署名証明書が作成され、クラスターと同じリソース グループに作成された新しいキー コンテナーにアップロードされます。 証明書はローカル ディレクトリにもコピーされます。  *-OS* パラメーターを設定して、クラスター ノードで実行される Windows または Linux のバージョンを選択します。  必要に応じてパラメーターをカスタマイズします。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Connect-AzureRmAccount` を実行して、Azure との接続を作成します。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用して、リソース グループ、クラスター、およびすべての関連リソースを削除できます。

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | 新しい Service Fabric クラスターを作成します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure Service Fabric 用 Azure PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
