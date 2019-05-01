---
title: Azure PowerShell のサンプル スクリプト - クラスターからのアプリケーションの削除 | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Service Fabric クラスターからのアプリケーションの削除。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: c8956b8bbbbe6d0928511da3cf75bfd23a76ed21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621776"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Service Fabric クラスターからのアプリケーションの削除

このサンプル スクリプトは、実行中の Service Fabric アプリケーション インスタンスを削除し、クラスターからアプリケーションの種類とバージョンの登録を解除します。  アプリケーション インスタンスを削除すると、そのアプリケーションに関連付けられている実行中のサービス インスタンスもすべて削除されます。 必要に応じてパラメーターをカスタマイズします。 

必要に応じて、Service Fabric PowerShell モジュールを、[Service Fabric SDK](../service-fabric-get-started.md) と共にインストールします。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | 実行中の Service Fabric アプリケーション インスタンスをクラスターから削除します。  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric アプリケーションの種類とバージョンの登録をクラスターから解除します。 |

## <a name="next-steps"></a>次の手順

Service Fabric PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/service-fabric/?view=azureservicefabricps)を参照してください。

その他の Azure Service Fabric 用 PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
