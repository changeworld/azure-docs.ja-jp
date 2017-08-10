---
title: "Azure CLI のサンプル スクリプト - クラスターへのアプリケーションのデプロイ"
description: "Azure CLI のサンプル スクリプト - Service Fabric クラスターへのアプリケーションのデプロイ。"
services: service-fabric
documentationcenter: 
author: Thraka
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
ms.openlocfilehash: de6ec4378a05656ecefefa7d5994a4f5dad404ba
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Service Fabric クラスターへのアプリケーションのデプロイ

このサンプル スクリプトは、アプリケーション パッケージをクラスター イメージ ストアにコピーし、クラスターにそのアプリケーションの種類を登録し、そのアプリケーションの種類からアプリケーション インスタンスを作成します。  ターゲット アプリケーションの種類のアプリケーション マニフェストで既定のサービスが定義されている場合、それらのサービスも同時に作成されます。

必要に応じて、[Azure CLI](../service-fabric-azure-cli-2-0.md) をインストールしてください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "クラスターへのアプリケーションのデプロイ")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、「[アプリケーションの削除](cli-remove-application.md)」のスクリプトを使用して、アプリケーション インスタンスを削除したり、アプリケーションの種類を登録解除したり、アプリケーション パッケージをイメージ ストアから削除したりできます。

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。


| コマンド | メモ |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | 使用するクラスターを選択します。 |
| [sf application upload](/cli/azure/sf/application#upload) | アプリケーションのファイルとマニフェストをアップロードします。 |
| [sf application provision](/cli/azure/sf/application#provision) | クラスター上でアプリケーションを登録します。|
| [sf application create](/cli/azure/sf/application#create) | アプリケーションのインスタンスを作成し、定義されているサービスをノードにデプロイします。 |

## <a name="next-steps"></a>次のステップ

詳細については、[Azure CLI のドキュメント](../service-fabric-azure-cli-2-0.md)をご覧ください。

その他の Azure Service Fabric 用 Azure CLI サンプルは、[Azure CLI のサンプル](../samples-cli.md)のページにあります。

