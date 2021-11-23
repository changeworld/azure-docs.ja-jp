---
title: AppDynamics Java エージェントを使用して Spring Boot アプリを監視する方法 (プレビュー)
titleSuffix: Azure Spring Cloud
description: AppDynamics Java エージェントを使用し、Azure Spring Cloud の Spring Boot アプリケーションを監視する方法。
author: KarlErickson
ms.author: jiec
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: 8c4a708cd04334f10f44c07d5daf9468f7137acf
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722698"
---
# <a name="how-to-monitor-spring-boot-apps-with-the-appdynamics-java-agent-preview"></a>AppDynamics Java エージェントを使用して Spring Boot アプリを監視する方法 (プレビュー)

この記事では、AppDynamics Java エージェントを使用し、Azure Spring Cloud の Spring Boot アプリケーションを監視する方法について説明します。

AppDynamics Java エージェントを使用すると、以下を行うことができます。

- アプリケーションの監視
- 環境変数を使用して AppDynamics Java エージェントを構成する
- AppDynamics ダッシュボードからすべての監視データを確認する

次の動画では、AppDynamics Java インプロセス エージェントについて解説しています。

<br>

> [!VIDEO https://www.youtube.com/embed/4dZuRX5bNAs]

## <a name="prerequisites"></a>前提条件

* [Azure CLI](/cli/azure/install-azure-cli)
* [AppDynamics アカウント](https://www.appdynamics.com/)

## <a name="activate-the-appdynamics-java-in-process-agent"></a>AppDynamics Java インプロセス エージェントをアクティブ化する

ワークフロー全体で、次のことを行う必要があります。

* Azure Spring Cloud で AppDynamics Java インプロセス エージェントをアクティブ化して、アプリケーション メトリック データを生成します。
* AppDynamics エージェントを AppDynamics コントローラーに接続し、コントローラー内のデータを収集して視覚化します。

!["Azure Spring Cloud" の Spring Boot アプリケーション ボックスと "AppDynamics Agent" ボックスが 2 方向の矢印で接続されており、そこからさらに "AppDynamics コントローラー" ボックスを指す矢印が伸びている図](media/how-to-appdynamics-java-agent-monitor/appdynamics-activation.jpg)

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-cli"></a>Azure CLI を使用して、AppDynamics エージェントを使用してアプリケーションをアクティブ化する

Azure CLI を使用してアプリケーションをアクティブ化するには、以下の手順を使用します。

1. リソース グループを作成する。
1. Azure Spring Cloud のインスタンスを作成します。
1. 次のコマンドを使用してアプリケーションを作成します。 プレースホルダー *\<...>* を実際の値に置き換えてください。

    ```azurecli
    az spring-cloud app create \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --is-public true
    ```

1. 環境変数を使用して、AppDynamics エージェントでデプロイを作成します。

    ```azurecli
    az spring-cloud app deploy \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --jar-path app.jar \
        --jvm-options="-javaagent:/opt/agents/appdynamics/java/javaagent.jar" \
        --env APPDYNAMICS_AGENT_APPLICATION_NAME=<your-app-name> \
              APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY=<your-agent-access-key> \
              APPDYNAMICS_AGENT_ACCOUNT_NAME=<your-agent-account-name> \
              APPDYNAMICS_AGENT_NODE_NAME=<your-agent-node-name> \
              APPDYNAMICS_AGENT_TIER_NAME=<your-agent-tier-name> \
              APPDYNAMICS_CONTROLLER_HOST_NAME=<your-AppDynamics-controller-host-name> \
              APPDYNAMICS_CONTROLLER_SSL_ENABLED=true \
              APPDYNAMICS_CONTROLLER_PORT=443
    ```

Azure Spring Cloud により、AppDynamics Java エージェントがパス */opt/agents/appdynamics/java/javaagent.jar* に事前インストールされます。 アプリケーションの JVM オプションからエージェントを有効にしてから、環境変数を使用してエージェントを構成できます。 これらの変数の値については、[Java エージェントを使用して Azure Spring Cloud を監視する](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent)方法に関するページを参照してください。 これらの変数が、AppDynamics UI でレポートを表示したり整理したりする上でどのように役立つかについては、[階層とノード](https://docs.appdynamics.com/21.9/en/application-monitoring/tiers-and-nodes)に関するページを参照してください。

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-portal"></a>Azure portal を使用して、AppDynamics エージェントを使用してアプリケーションをアクティブ化する

Azure portal を使用してアプリケーションをアクティブ化するには、以下の手順を使用します。

1. Azure portal で Azure Spring Cloud インスタンスに移動します。

1. 左側のナビゲーション ウィンドウの **[Settings]\(設定\)** セクションから **[アプリ]** をクリックします。

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png" alt-text="[アプリ] セクションを示す Azure portal のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png":::

1. アプリケーションを選択して、 **[概要]** ページに移動します。

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png" alt-text="Azure portal の [概要] ページのスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png":::

1. 左側のナビゲーション ウィンドウで **[構成]** を選択して、アプリケーションの環境変数を追加、更新、または削除します。

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png" alt-text="アプリの [構成] ページの [環境変数] セクションを示す Azure portal のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png":::

1. **[全般設定]** を選択して、アプリケーションの JVM オプションを追加、更新、または削除します。

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png" alt-text="アプリの [構成] ページの [全般設定] セクションを示す Azure portal のスクリーンショット。[JVM オプション] が強調表示されています" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png":::

## <a name="automate-provisioning"></a>プロビジョニングを自動化する

Terraform または Azure Resource Manager テンプレート (ARM テンプレート) を使用して、プロビジョニング自動化パイプラインを実行することもできます。 このパイプラインでは、作成およびデプロイする新しいアプリケーションをインストルメント化して監視するための完全なハンズオン エクスペリエンスが提供されます。

### <a name="automate-provisioning-using-terraform"></a>Terraform でプロビジョニングを自動化する

Terraform テンプレートで環境変数を構成するには、次のコードをテンプレートに追加し、 *\<...>* プレースホルダーを実際の値に置き換えます。 詳細については、「[Azure Spring Cloud のアクティブなデプロイを管理する](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment)」を参照してください。

```terraform
resource "azurerm_spring_cloud_java_deployment" "example" {
  ...
  jvm_options = "-javaagent:/opt/agents/appdynamics/java/javaagent.jar"
  ...
    environment_variables = {
      "APPDYNAMICS_AGENT_APPLICATION_NAME" : "<your-app-name>",
      "APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY" : "<your-agent-access-key>",
      "APPDYNAMICS_AGENT_ACCOUNT_NAME" : "<your-agent-account-name>",
      "APPDYNAMICS_AGENT_NODE_NAME" : "<your-agent-node-name>",
      "APPDYNAMICS_AGENT_TIER_NAME" : "<your-agent-tier-name>",
      "APPDYNAMICS_CONTROLLER_HOST_NAME" : "<your-AppDynamics-controller-host-name>",
      "APPDYNAMICS_CONTROLLER_SSL_ENABLED" : "true",
      "APPDYNAMICS_CONTROLLER_PORT" : "443"
  }
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>ARM テンプレートでプロビジョニングを自動化する

ARM テンプレートで環境変数を構成するには、次のコードをテンプレートに追加し、 *\<...>* プレースホルダーを実際の値に置き換えます。 詳細については、[Microsoft.AppPlatform Spring/apps/deployments](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json) を参照してください。

```ARM template
"deploymentSettings": {
  "environmentVariables": {
    "APPDYNAMICS_AGENT_APPLICATION_NAME" : "<your-app-name>",
    "APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY" : "<your-agent-access-key>",
    "APPDYNAMICS_AGENT_ACCOUNT_NAME" : "<your-agent-account-name>",
    "APPDYNAMICS_AGENT_NODE_NAME" : "<your-agent-node-name>",
    "APPDYNAMICS_AGENT_TIER_NAME" : "<your-agent-tier-name>",
    "APPDYNAMICS_CONTROLLER_HOST_NAME" : "<your-AppDynamics-controller-host-name>",
    "APPDYNAMICS_CONTROLLER_SSL_ENABLED" : "true",
    "APPDYNAMICS_CONTROLLER_PORT" : "443"
  },
  "jvmOptions": "-javaagent:/opt/agents/appdynamics/java/javaagent.jar",
  ...
}
```

## <a name="review-reports-in-the-appdynamics-dashboard"></a>AppDynamics ダッシュボードでレポートを確認する

このセクションでは、AppDynamics のさまざまなレポートを紹介します。

次のスクリーンショットは、AppDynamics ダッシュボードに表示されるアプリの概要を示しています。

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg" alt-text="[アプリケーション] ページが表示されている AppDynamics のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg":::

**[アプリケーション ダッシュボード]** には、アプリケーションの例を用いた以下のスクリーンショットのように、各アプリケーションの全体的な情報が表示されます。

- `api-gateway`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg" alt-text="例の api-gateway アプリのアプリケーション ダッシュボードを示す AppDynamics のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg":::

- `customers-service`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg" alt-text="例の customers-service アプリのアプリケーション ダッシュボードを示す AppDynamics のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg":::

次のスクリーンショットは、**データベース呼び出し** ダッシュボードから基本情報を取得する方法を示しています。

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg" alt-text="データベース呼び出しダッシュボードを示す AppDynamics のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg":::

次のスクリーンショットに示すように、最も低速なデータベース呼び出しに関する情報を取得することもできます。

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg" alt-text="[最も低速なデータベース呼び出し] ページを示す AppDynamics のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg":::

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg" alt-text="[最も低速なデータベース呼び出し] ページからアクセスできる [相関スナップショット] ページを示す AppDynamics のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg":::

次のスクリーンショットは、 **[メモリ]** ページの **[ヒープ]** セクションに表示されるメモリ使用量分析を示しています。

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg" alt-text="[メモリ] ページの [ヒープ] セクションを示す AppDynamics のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg":::

次のスクリーンショットに示すように、ガベージ コレクションのプロセスを確認することもできます。

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg" alt-text="[メモリ] ページの [ガベージ コレクション] セクションを示す AppDynamics のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg":::

次のスクリーンショットは **[低速のトランザクション]** ページを示しています。 

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg" alt-text="[低速のトランザクション] ページを示す AppDynamics スクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg":::

この **[メトリック ブラウザー]** のスクリーンショットに示されているように、JVM に対してさらに多くのメトリクスを定義することができます。

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg" alt-text="メトリック ブラウザーを示す AppDynamics のスクリーンショット" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg":::

## <a name="view-appdynamics-agent-logs"></a>AppDynamics エージェントのログを表示する

Azure Spring Cloud では、既定で AppDynamics エージェントの "*情報*" レベルのログが `STDOUT` に出力されます。 このログは、アプリケーション ログと混在しています。 アプリケーション ログから明示的なエージェント バージョンを見つけることができます。

AppDynamics エージェントのログは、次の場所から取得することもできます。

* Azure Spring Cloud のログ
* Azure Spring Cloud Application Insights
* Azure Spring Cloud LogStream

## <a name="learn-about-appdynamics-agent-upgrade"></a>AppDynamics エージェントのアップグレードについて

AppDynamics エージェントは、JDK を用いて定期的にアップグレードされます (四半期ごと)。 エージェントのアップグレードは、次のシナリオに影響する場合があります。

* アップグレード前に AppDynamics エージェントが使用されていた既存のアプリケーションは変更されませんが、新しいバージョンの AppDynamics エージェントを利用するには再起動または再デプロイが必要になります。
* アップグレード後に作成されたアプリケーションでは、新しいバージョンの AppDynamics エージェントが使用されます。

## <a name="configure-vnet-injection-instance-outbound-traffic"></a>VNet インジェクション インスタンスの送信トラフィックの構成

Azure Spring Cloud の VNet インジェクション インスタンスでは、AppDynamics エージェントに対して送信トラフィックが正しく構成されていることを確認します。 詳細については、[SaaS ドメインと IP 範囲](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges)に関するページと、「[VNET での Azure Spring Cloud の実行に関するお客様の責任](vnet-customer-responsibilities.md)」を参照してください。

## <a name="understand-the-limitations"></a>制限事項を理解する

AppDynamics エージェントの制限事項については、[Java エージェントを使用して Azure Spring Cloud を監視する](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent)方法に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Spring Cloud で Application Insights Java インプロセス エージェントを使用する](./how-to-application-insights.md)