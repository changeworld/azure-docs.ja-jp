---
title: Azure Spring Cloud の CI/CD
description: Azure Spring Cloud の CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 3335dbb749369129889f132a68be0270a1016f1c
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790795"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>Azure Spring Cloud へのアプリケーション デプロイを自動化する

継続的インテグレーションと継続的デリバリーのツールを使用すると、最小限の労力とリスクで、既存のアプリケーションにすばやく更新プログラムをデプロイできます。 Azure DevOps は、これらの主要なジョブを整理および制御するのに役立ちます。 

この記事では、[Azure Pipelines 用の Azure Spring Cloud タスク](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud)を使用し、アプリケーションをデプロイする方法について説明します。

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager サービス接続を作成する

Azure DevOps プロジェクトへの Azure Resource Manager サービス接続を作成する方法については、[こちらの記事](/azure/devops/pipelines/library/connect-to-azure)を参照してください。 Azure Spring Cloud サービス インスタンスに使用しているものと同じサブスクリプションを必ず選択してください。

## <a name="build-and-deploy-apps"></a>アプリをビルドして配置する

::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>成果物をデプロイする

一連のタスクを使用し、プロジェクトをビルドしてデプロイできます。 このスニペットでは、変数、アプリケーションをビルドする .NET Core タスク、アプリケーションをデプロイする Azure Spring Cloud タスクを定義しています。

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'


steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Deploy the planet app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(planetAppName)/publish-deploy-planet.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(planetMainEntry)

# Deploy the solar app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(solarAppName)/publish-deploy-solar.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(solarMainEntry)
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>成果物をデプロイする

#### <a name="to-production"></a>運用環境

一連のタスクを使用し、プロジェクトをビルドしてデプロイできます。 このスニペットでは、最初にアプリケーションをビルドする Maven タスクが定義され、次に Azure Pipelines 用の Azure Spring Cloud タスクを使用して JAR ファイルをデプロイする 2 つ目のタスクが定義されています。

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: ./target/your-result-jar.jar
```

#### <a name="blue-green-deployments"></a>ブルーグリーン デプロイ

前のセクションで示されているデプロイでは、デプロイ時にすぐにアプリケーション トラフィックを受信します。 開発者は、顧客のトラフィックをアプリケーションで受信する前に、運用環境でアプリケーションをテストすることを望む場合があります。

次のスニペットでは、上と同じようにアプリケーションをビルドし、その後、それをステージング デプロイにデプロイします。 この例では、ステージング デプロイが既に存在している必要があります。 別の方法については、[ブルーグリーン デプロイの戦略](concepts-blue-green-deployment-strategies.md)に関するページを参照してください。


```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
    Package: ./target/your-result-jar.jar
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Set Production'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
```

### <a name="deploy-from-source"></a>ソースからデプロイする

独立したビルド ステップを使用せず、Azure に直接デプロイすることができます。

```yaml
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(Build.SourcesDirectory)
```
::: zone-end

## <a name="next-steps"></a>次の手順

* [クイック スタート: 初めての Azure Spring Cloud アプリケーションをデプロイする](./quickstart.md)

