---
title: Azure Spring Cloud へのアプリケーション デプロイを自動化する
description: Azure Pipelines 用の Azure Spring Cloud タスクを使用する方法について説明します。
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 4f1075a690c806be76b0102c2954a4f5127dabee
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258337"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>Azure Spring Cloud へのアプリケーション デプロイを自動化する

この記事では、[Azure Pipelines 用の Azure Spring Cloud タスク](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud)を使用し、アプリケーションをデプロイする方法について説明します。

継続的インテグレーションと継続的デリバリーのツールを使用すると、最小限の労力とリスクで、既存のアプリケーションにすばやく更新プログラムをデプロイできます。 Azure DevOps は、これらの主要なジョブを整理および制御するのに役立ちます。 

次の動画では、Azure Pipelines など、自分で選択したツールを使用したエンドツーエンドの自動化について説明します。

<br>

> [!VIDEO https://www.youtube.com/embed/D2cfXAbUwDc?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

::: zone pivot="programming-language-csharp"

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager サービス接続を作成する

まず、ご自分の Azure DevOps プロジェクトへの Azure Resource Manager サービス接続を作成します。 手順については、「[Microsoft Azure に接続する](/azure/devops/pipelines/library/connect-to-azure)」を参照してください。 Azure Spring Cloud サービス インスタンスに使用しているものと同じサブスクリプションを必ず選択してください。

## <a name="build-and-deploy-apps"></a>アプリをビルドして配置する

一連のタスクを使用し、プロジェクトをビルドしてデプロイできるようになりました。 次の Azure Pipelines テンプレートでは、変数のほかに、アプリケーションをビルドする .NET Core タスク、アプリケーションをデプロイする Azure Spring Cloud タスクを定義しています。

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

## <a name="set-up-an-azure-spring-cloud-instance-and-an-azure-devops-project"></a>Azure Spring Cloud インスタンスと Azure DevOps プロジェクトを設定する

まず次の手順に従い、Azure DevOps で使用する既存の Azure Spring Cloud インスタンスを設定します。

1. Azure Spring Cloud インスタンスに移動して、新しいアプリを作成します。 
1. Azure DevOps ポータルに移動し、選択した組織で新しいプロジェクトを作成します。 Azure DevOps 組織がない場合は、無料で作成することができます。
1. **[Repos]** を選択し、[Spring Boot のデモ コード](https://github.com/spring-guides/gs-spring-boot)をリポジトリにインポートします。

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager サービス接続を作成する

次に、Azure DevOps プロジェクトへの Azure Resource Manager サービス接続を作成します。 手順については、「[Microsoft Azure に接続する](/azure/devops/pipelines/library/connect-to-azure)」を参照してください。 Azure Spring Cloud サービス インスタンスに使用しているものと同じサブスクリプションを必ず選択してください。

## <a name="build-and-deploy-apps"></a>アプリをビルドして配置する

一連のタスクを使用し、プロジェクトをビルドしてデプロイできるようになりました。 以降のセクションでは、Azure DevOps を使用してアプリをデプロイするためのさまざまな方法を紹介します。

### <a name="deploy-using-a-pipeline"></a>パイプラインを使用してデプロイする

パイプラインを使用してデプロイするには、次の手順に従います。

1. **[パイプライン]** を選択し、Maven テンプレートを使用して新しいパイプラインを作成します。
1. *azure-pipelines.yml* ファイルを編集して、`mavenPomFile` フィールドを *'complete/pom.xml'* に設定します。
1. 右側の **[アシスタントを表示する]** を選択して、**Azure Spring Cloud** テンプレートを選択します。
1. 自分の Azure サブスクリプションに対して作成したサービス接続を選択し、Spring Cloud インスタンスとアプリ インスタンスを選択します。 
1. **[Use Staging Deployment]\(ステージング デプロイを使用する\)** を無効にします。
1. **[Package or folder]\(パッケージまたはフォルダー\)** を *[complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar]* に設定します。
1. **[追加]** を選択して、このタスクをパイプラインに追加します。
  
   パイプラインの設定が次の画像と一致している必要があります。

   :::image type="content" source="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg" alt-text="パイプラインの設定のスクリーンショット。" lightbox="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg":::

   次のパイプライン テンプレートを使用してプロジェクトをビルド、デプロイすることもできます。 この例では、アプリケーションをビルドする Maven タスクが最初に定義され、次に Azure Pipelines 用の Azure Spring Cloud タスクを使用して JAR ファイルをデプロイする 2 つ目のタスクが定義されています。

   ```yaml
   steps:
   - task: Maven@3
     inputs:
       mavenPomFile: 'complete/pom.xml'
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

3. **[保存および実行]** を選択してジョブの完了を待ちます。

### <a name="blue-green-deployments"></a>ブルーグリーン デプロイ

前のセクションで示されているデプロイでは、デプロイ時にすぐにアプリケーション トラフィックを受信します。 そのため、顧客のトラフィックをアプリケーションで受信する前に運用環境でアプリケーションをテストすることができます。

#### <a name="edit-the-pipeline-file"></a>パイプライン ファイルを編集する

前述した同じ方法でアプリケーションをビルドし、それをステージング デプロイにデプロイするには、次のテンプレートを使用します。 この例では、ステージング デプロイが既に存在している必要があります。 別の方法については、[ブルーグリーン デプロイの戦略](concepts-blue-green-deployment-strategies.md)に関するページを参照してください。

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

#### <a name="use-the-releases-section"></a>[リリース] セクションを使用する

以下の手順は、 **[リリース]** セクションからブルーグリーン デプロイを有効にする方法を示しています。

1. **[パイプライン]** を選択し、Maven のビルドと成果物の発行のために新しいパイプラインを作成します。
   1. コードの場所に **[Azure Repos Git]** を選択します。
   1. コードが置かれているリポジトリを選択します。
   1. **[Maven]** テンプレートを選択し、そのファイルの `mavenPomFile` フィールドを *`complete/pom.xml`* に設定します。
   1. 右側の **[アシスタントを表示する]** を選択し、 **[Publish build artifacts]\(ビルド成果物の発行\)** テンプレートを選択します。
   1. **[Path to publish]\(発行するためのパス\)** を「*complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar*」に設定します。
   1. **[保存および実行]** を選択します。

1. **[リリース]** 、 **[リリースの作成]** の順に選択します。
1. 新しいパイプラインを追加し、 **[空のジョブ]** を選択してジョブを作成します。
1. **[ステージ]** で **[1 job, 0 task]\(1 ジョブ、0 タスク\)** という行を選択します。

   :::image type="content" source="media/spring-cloud-how-to-cicd/create-new-job.jpg" alt-text="ジョブにタスクを追加する際に選択する箇所のスクリーンショット。":::

   1. **[+]** を選択してジョブにタスクを追加します。
   1. **Azure Spring Cloud** テンプレートを検索し、 **[追加]** を選択してジョブにタスクを追加します。
   1. **[Azure Spring Cloud Deploy:]\(Azure Spring Cloud デプロイ:\)** を選択してタスクを編集します。
   1. このタスクに自分のアプリの情報を入力し、 **[Use Staging Deployment]\(ステージング デプロイを使用する\)** を無効にします。
   1. **[Create a new staging deployment if one does not exist]\(まだ存在しない場合は新しいステージング デプロイを作成する\)** を有効にして、 **[デプロイ]** に名前を入力します。
   1. **[保存]** を選択してこのタスクを保存します。
   1. **[OK]** を選択します。
1. **[パイプライン]** を選択し、 **[Add an artifact]\(成果物の追加\)** を選択します。
   1. **[Source (build pipeline)]\(ソース (ビルド パイプライン)\)** で、前に作成したパイプラインを選択します。
   1. **[追加]** を選択し、 **[保存]** を選択します。
1. **[ステージ]** の **[1 job, 1 task]\(1 ジョブ、1 タスク\)** を選択します。
1. **ステージ 1** の **[Azure Spring Cloud Deploy]\(Azure Spring Cloud デプロイ\)** タスクに移動し、 **[Package or folder]\(パッケージまたはフォルダー\)** の横の省略記号を選択します。
1. ダイアログの *[spring-boot-complete-0.0.1-SNAPSHOT.jar]* を選択し、 **[OK]** を選択します。

   :::image type="content" source="media/spring-cloud-how-to-cicd/change-artifact-path.jpg" alt-text="[ファイルまたはフォルダーの選択] ダイアログ ボックスのスクリーンショット。":::

1. **[+]** を選択して、別の **Azure Spring Cloud** タスクをジョブに追加します。
2. アクションを **[Set Production Deployment]\(運用デプロイの設定\)** に変更します。
3. **[保存]** を選択し、 **[リリースの作成]** を選択すると、自動的にデプロイが開始されます。 

アプリの最新のリリース状態を確認するには、 **[View release]\(リリースの表示\)** を選択します。 このタスクが完了したら、Azure portal にアクセスしてアプリの状態を確認してください。

### <a name="deploy-from-source"></a>ソースからデプロイする

別途ビルド ステップを使用せずに直接 Azure にデプロイするには、次のパイプライン テンプレートを使用します。

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

## <a name="next-steps"></a>次のステップ

* [クイック スタート: 初めての Spring Boot アプリを Azure Spring Cloud にデプロイする](./quickstart.md)
