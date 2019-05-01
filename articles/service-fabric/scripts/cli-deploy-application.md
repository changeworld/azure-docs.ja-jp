---
title: Azure Service Fabric CLI (sfctl) スクリプトのデプロイのサンプル
description: Azure Service Fabric CLI を使用した Azure Service Fabric クラスターへのアプリケーションのデプロイ
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
origin.date: 04/16/2018
ms.date: 04/29/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: f3ab32101a46c1044954f2efe88ac05ab81af24a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622000"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Service Fabric クラスターへのアプリケーションのデプロイ

このサンプル スクリプトは、アプリケーション パッケージをクラスター イメージ ストアにコピーし、クラスターにそのアプリケーションの種類を登録し、そのアプリケーションの種類からアプリケーション インスタンスを作成します。 また、この時点で既定のサービスも作成されます。

必要に応じて、[Service Fabric CLI](../service-fabric-cli.md) をインストールします。

## <a name="sample-script"></a>サンプル スクリプト

```sh
#!/bin/bash

# Select cluster
sfctl cluster select \
    --endpoint http://svcfab1.chinanorth.cloudapp.chinacloudapi.cn:19080

# Upload the application files to the image store
# (note the last folder name, Debug in this example)
sfctl application upload \
    --path  C:\Code\svcfab-vs\svcfab-vs\pkg\Debug \
    --show-progress

# Register the application (manifest files) from the image store
# (Note the last folder from the previous command is used: Debug)
sfctl application provision \
    --application-type-build-path Debug \
    --timeout 500

# Create an instance of the registered application and 
# auto deploy any defined services
sfctl application create \
    --app-name fabric:/MyApp \
    --app-type MyAppType \
    --app-version 1.0.0

```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

終了したら、[remove](cli-remove-application.md) スクリプトを使用してアプリケーションを削除することができます。 remove スクリプトは、アプリケーション インスタンスを削除し、アプリケーションの種類の登録を解除し、イメージ ストアからアプリケーション パッケージを削除します。

## <a name="next-steps"></a>次の手順

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。

<!--Update_Description: update meta properties, update link -->