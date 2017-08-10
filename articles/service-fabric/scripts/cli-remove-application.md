---
title: "Azure CLI のサンプル スクリプト - クラスターからのアプリケーションの削除"
description: "Azure CLI のサンプル スクリプト - Service Fabric クラスターからのアプリケーションの削除。"
services: service-fabric
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 77fa78199854d10c4897d56da12c8e359ae491f3
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Service Fabric クラスターからのアプリケーションの削除

このサンプル スクリプトは、実行中の Service Fabric アプリケーション インスタンスを削除し、クラスターからアプリケーションの種類とバージョンの登録を解除します。  アプリケーション インスタンスを削除すると、そのアプリケーションに関連付けられている実行中のサービス インスタンスもすべて削除されます。 次に、アプリケーション ファイルがイメージ ストアから削除されます。 

必要に応じて、[Azure CLI](../service-fabric-azure-cli-2-0.md) をインストールしてください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "クラスターからのアプリケーションの削除")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | 使用するクラスターを選択します。 |
| [sf application delete](/cli/azure/sf/application#delete) | クラスターからアプリケーション インスタンスを削除します。 |
| [sf application unprovision](/cli/azure/sf/application#unprovision) | Service Fabric アプリケーションの種類とバージョンの登録をクラスターから解除します。|
| [sf application package-delete](/cli/azure/sf/application#package-delete) | Service Fabric アプリケーション パッケージをイメージ ストアから削除します。 |

## <a name="next-steps"></a>次のステップ

詳細については、[Azure CLI のドキュメント](../service-fabric-azure-cli-2-0.md)をご覧ください。

その他の Azure Service Fabric 用 Azure CLI サンプルは、[Azure CLI のサンプル](../samples-cli.md)のページにあります。

