<properties 
	pageTitle="Azure App Service での高密度ホスティング" 
	description="Azure App Service での高密度ホスティング" 
	authors="btardif" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/17/2016" 
	ms.author="byvinyal"/>

#Azure App Service での高密度ホスティング#

##アプリのスケーリングを理解する##

App Service を使用するとき、アプリケーションは 2 つの概念によって割り当てられた容量から切り離されます。
 
>**アプリケーション:** アプリとその実行時構成を表します。ランタイムが読み込む必要のある .NET のバージョンや、アプリの設定などです。

>**App Service プラン:** 容量、使用可能な機能セット、アプリケーションの局所性の特性を定義します。たとえば、大きな (4 コア) コンピューター、4 インスタンス、米国東部の Premium 機能などです。

アプリは常に **App Service プラン**にリンクされていますが、**App Service プラン**は 1 つまたは複数のアプリに容量を提供できます。

つまり、プラットフォームは柔軟に、1 つのアプリを分離したり、**App Service プラン**を共有して複数のアプリでリソースを共有したりできます。

ただし、複数のアプリが **App Service プラン**を共有するときは、その **App Service プラン**の各インスタンスで実行するそのアプリのインスタンスがあります。

##アプリごとのスケーリング##
**アプリごとのスケーリング**は、**App Service プラン** レベルで有効にしてアプリケーションごとに利用できる機能です。

**アプリごとのスケーリング**を使用すると、アプリのホストに使用されている **App Service プラン**とは無関係に、アプリをスケーリングできます。これにより、10 個のインスタンスを提供するように **App Service プラン**を構成しながら、アプリはそのうちの 5 個だけにスケーリングするように設定する、といったことができます。

次の ARM テンプレートは、10 個のインスタンスにスケールアウトされる **App Service プラン**と、**アプリごとのスケーリング**を使用して 5 個のインスタンスだけにスケーリングするように構成されたアプリを作成します。

そのために、App Service プランはサイトごとのスケーリング プロパティを true に設定し ( `"perSiteScaling": true`)、アプリは使用する worker の数を 1 に設定しています `"properties": { "numberOfWorkers": "1" }`。

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


##高密度ホスティングの推奨される構成##

**アプリごとのスケーリング**はパブリック Azure リージョンと App Service 環境の両方で有効にされる機能ですが、推奨される戦略は、App Service 環境を使用して、高度な機能と大きな容量プールを活用することです。

以下の手順は、アプリの**高密度ホスティング**を構成する方法についてのガイドラインです。

1. **App Service 環境**を構成し、*高密度ホスティング* シナリオ専用の **worker プール**を選択します。

1. 1 つの **App Service プラン**を作成し、**worker プール**の全使用可能容量を使用するようにスケーリングします。

1. **App Service プラン**でサイトごとのスケーリング フラグを true に設定します。

1. 新しいサイトが作成されて、その **App Service プラン**に *1* に設定された *numberOfWorkers* プロパティが割り当てられます。これにより、この **worker プール**で可能な最高の密度になります。

1. worker の数はサイトごとに個別に構成でき、必要に応じて追加リソースを許可できます。たとえば、使用率が高いサイトでは *numberOfWorkers* を *3* に設定してそのアプリの処理能力を上げることができ、使用率の低いサイトでは *numberOfWorkers* を *1* に設定できます。

<!---HONumber=AcomDC_0518_2016-->