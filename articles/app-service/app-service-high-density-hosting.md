---
title: Azure App Service での高密度ホスティング | Microsoft Docs
description: Azure App Service での高密度ホスティング
author: btardif
manager: wpickett
editor: ''
services: app-service\web
documentationcenter: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/07/2016
ms.author: byvinyal

---
# Azure App Service での高密度ホスティング
App Service を使用するとき、アプリケーションは 2 つの概念によって割り当てられた容量から切り離されます。

* **アプリケーション:** アプリとその実行時構成を表します。たとえば、ランタイムで読み込む必要のある .NET のバージョンや、アプリの設定が該当します。
* **App Service プラン:** 容量、使用可能な機能セット、アプリケーションの局所性の特性を定義します。たとえば、米国東部に存在する L サイズ (4 コア) マシン 4 インスタンスのプレミアム機能といった特性が考えられます。

アプリは常に App Service プランにリンクされていますが、App Service プランは 1 つまたは複数のアプリに容量を提供できます。

つまり、プラットフォームは柔軟に、1 つのアプリを分離したり、App Service プランを共有して複数のアプリでリソースを共有したりできます。

ただし、複数のアプリが 1 つの App Service プランを共有するときは、その App Service プランの各インスタンス上でそのアプリのインスタンスが実行されます。

## アプリごとのスケーリング
"*アプリごとのスケーリング*" は、App Service プラン レベルで有効にしてアプリケーションごとに利用できる機能です。

アプリごとのスケーリングでは、アプリがそのホストとなる App Service プランとは無関係にスケーリングされます。これにより、10 個のインスタンスを提供するように App Service プランを構成しながら、アプリはそのうちの 5 個だけにスケーリングするように設定する、といったことができます。

以下の Azure Resource Manager テンプレートでは、10 インスタンスのスケールアウトに対応した App Service プランと、アプリごとのスケーリングによって規模の調整に使用できるインスタンスを 5 つまでとして構成されたアプリが作成されます。

そのために、App Service プランは**サイトごとのスケーリング** プロパティを true に設定し (`"perSiteScaling": true`)、アプリは使用する **worker の数**を 1 に設定しています (`"properties": { "numberOfWorkers": "1" }`)。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "1" }
             } ]
         }]
    }


## 高密度ホスティングの推奨される構成
アプリごとのスケーリングは、パブリック Azure リージョンと App Service Environment のどちらでも有効にすることができます。ただし推奨されるのは App Service Environment です。App Service Environment を使用した方が、その高度な機能と大きなプール容量を利用できます。

アプリに対して高密度ホスティングを構成するには、次の手順に従います。

1. App Service Environment を構成し、高密度ホスティング シナリオ専用のワーカー プールを選択します。
2. 1 つの App Service プランを作成し、ワーカー プールの全使用可能容量を使用するようにスケーリングします。
3. App Service プランでサイトごとのスケーリング フラグを true に設定します。
4. 新しいサイトが作成されて、その App Service プランに **1** に設定された **numberOfWorkers** プロパティが割り当てられます。これにより、このワーカー プールで可能な最高の密度になります。
5. worker の数はサイトごとに個別に構成でき、必要に応じて追加リソースを許可できます。たとえば、使用率が高いサイトでは **numberOfWorkers** を **3** に設定してそのアプリの処理能力を上げることができ、使用率の低いサイトでは **numberOfWorkers** を **1** に設定できます。

<!---HONumber=AcomDC_0907_2016-->