---
title: New Relic Java エージェントを使用して Spring Boot アプリを監視する方法
titleSuffix: Azure Spring Cloud
description: New Relic Java エージェントを使用して Spring Boot アプリケーションを監視する方法について説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-java
ms.openlocfilehash: 7c0a659effc973737647f3868154be1e5154afba
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493706"
---
# <a name="how-to-monitor-spring-boot-apps-using-new-relic-java-agent-preview"></a>New Relic Java エージェントを使用して Spring Boot アプリを監視する方法 (プレビュー)

この機能を使用すると、Azure Spring Cloud の Spring Boot アプリケーションを New Relic Java エージェントによって監視できます。

New Relic Java エージェントによって、以下を行えます。
* New Relic Java エージェントを使用する。
* 環境変数を使用して New Relic Java エージェントを構成する。
* New Relic ダッシュボードからすべての監視データを確認する。

次のビデオでは、New Relic One を使用して Azure Spring Cloud の Spring Boot アプリケーションをアクティブにして監視する方法について説明します。

<br>

> [!VIDEO https://www.youtube.com/embed/4GQPwJSP3ys?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

## <a name="prerequisites"></a>前提条件

* [New Relic](https://newrelic.com/) アカウント。
* [Azure CLI バージョン 2.0.67 以降](/cli/azure/install-azure-cli)。

## <a name="activate-the-new-relic-java-in-process-agent"></a>New Relic Java インプロセス エージェントをアクティブにする

エージェントを受け入れるには、次の手順に従います。

1. Azure Spring Cloud のインスタンスを作成します。

2. アプリケーションを作成します。

    ```azurecli
      az spring-cloud app create --name "appName" --is-public true \
      -s "resourceName" -g "resourceGroupName"
    ```

3. New Relic エージェントと環境変数を使用してデプロイを作成します。

    ```azurecli
    az spring-cloud app deploy --name "appName" --jar-path app.jar \
       -s "resourceName" -g "resourceGroupName" \
       --jvm-options="-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar" \
       --env NEW_RELIC_APP_NAME=appName NEW_RELIC_LICENSE_KEY=newRelicLicenseKey
    ```

Azure Spring Cloud では、 */opt/agents/newrelic/java/newrelic-agent.jar* に New Relic Java エージェントが事前にインストールされます。 お客様は、アプリケーションの **JVM オプション** からエージェントをアクティブにできるだけでなく、[New Relic Java エージェント環境変数](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables)を使用してエージェントを構成することもできます。

## <a name="portal"></a>ポータル

次の手順で、このエージェントをポータルからアクティブにすることもできます。

1. ナビゲーション ウィンドウの **[設定]** / **[アプリ]** からアプリを見つけます。

   [ ![監視するアプリを見つける](media/new-relic-monitoring/find-app.png) ](media/new-relic-monitoring/find-app.png)

2. アプリケーションを選択して **[概要]** ページに移動します。

   [ ![[概要] ページ](media/new-relic-monitoring/overview-page.png) ](media/new-relic-monitoring/overview-page.png)

3. 左側のナビゲーション ウィンドウで **[構成]** を選択して、アプリケーションの **環境変数** を追加、更新、削除します。

   [ ![環境の更新](media/new-relic-monitoring/configurations-update-environment.png) ](media/new-relic-monitoring/configurations-update-environment.png)

4. **[全般設定]** を選択して、アプリケーションの **JVM オプション** を追加、更新、削除します。

   [ ![JVM オプションの更新 ](media/new-relic-monitoring/update-jvm-option.png) ](media/new-relic-monitoring/update-jvm-option.png)

5. アプリケーション API/ゲートウェイの **[概要]** ページを New Relic ダッシュボードから表示します。

   [ ![アプリの [概要] ページ](media/new-relic-monitoring/app-summary-page.png) ](media/new-relic-monitoring/app-summary-page.png)

6. アプリケーション顧客サービスの **[概要]** ページを New Relic ダッシュボードから表示します。
 
   [ ![[顧客サービス] ページ](media/new-relic-monitoring/customers-service.png) ](media/new-relic-monitoring/customers-service.png)  

7. New Relic ダッシュボードから **[サービス マップ]** ページを表示します。  

   [ ![[サービス マップ] ページ](media/new-relic-monitoring/service-map.png) ](media/new-relic-monitoring/service-map.png)

8. アプリケーションの **JVM** ページを New Relic ダッシュボードから表示します。

   [ ![JVM ページ](media/new-relic-monitoring/jvm-page.png) ](media/new-relic-monitoring/jvm-page.png)

9. アプリケーション プロファイルを New Relic ダッシュボードから表示します。

   [ ![アプリケーション プロファイル](media/new-relic-monitoring/profile-app.png) ](media/new-relic-monitoring/profile-app.png)

## <a name="automate-provisioning"></a>プロビジョニングを自動化する

Terraform または Azure Resource Manager テンプレート (ARM テンプレート) を使用して、プロビジョニング自動化パイプラインを実行することもできます。 このパイプラインでは、作成およびデプロイする新しいアプリケーションをインストルメント化して監視するための完全なハンズオン エクスペリエンスが提供されます。

### <a name="automate-provisioning-using-terraform"></a>Terraform でプロビジョニングを自動化する

Terraform テンプレートで環境変数を構成するには、次のコードをテンプレートに追加し、 *\<...>* プレースホルダーを実際の値に置き換えます。 詳細については、「[Azure Spring Cloud のアクティブなデプロイを管理する](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment)」を参照してください。

```terraform
resource "azurerm_spring_cloud_java_deployment" "example" {
  ...
  jvm_options = "-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar"
  ...
    environment_variables = {
      "NEW_RELIC_APP_NAME": "<app-name>",
      "NEW_RELIC_LICENSE_KEY": "<new-relic-license-key>"
  }
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>ARM テンプレートでプロビジョニングを自動化する

ARM テンプレートで環境変数を構成するには、次のコードをテンプレートに追加し、 *\<...>* プレースホルダーを実際の値に置き換えます。 詳細については、[Microsoft.AppPlatform Spring/apps/deployments](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json) を参照してください。

```ARM template
"deploymentSettings": {
  "environmentVariables": {
    "NEW_RELIC_APP_NAME" : "<app-name>",
    "NEW_RELIC_LICENSE_KEY" : "<new-relic-license-key>"
  },
  "jvmOptions": "-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar",
  ...
}
```

## <a name="view-new-relic-java-agent-logs"></a>New Relic Java エージェントのログを表示する

Azure Spring Cloud では、既定で New Relic Java エージェントのログが `STDOUT` に出力されます。 このログは、アプリケーション ログと混在しています。 アプリケーション ログから明示的なエージェント バージョンを見つけることができます。

New Relic エージェントのログは、次の場所から取得することもできます。

* Azure Spring Cloud のログ
* Azure Spring Cloud Application Insights
* Azure Spring Cloud LogStream

New Relic によって提供される一部の環境変数 (`NEW_RELIC_LOG_LEVEL` など) を利用して新しいエージェントのログ記録を構成し、ログのレベルを制御できます。 詳細については、[New Relic の環境変数](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables)に関するページを参照してください。

> [!CAUTION]
> New Relic 用に Azure Spring Cloud によって提供されるログ記録の既定の動作をオーバーライド "*しない*" ことを強くお勧めします。 そうすると、上記のログ記録シナリオがブロックされ、ログ ファイルが失われる可能性があります。 たとえば、次の環境変数をアプリケーションに渡さないようにします。 再起動またはアプリケーションの再デプロイ後に、ログ ファイルが失われる可能性があります。
>
> * NEW_RELIC_LOG
> * NEW_RELIC_LOG_FILE_PATH

## <a name="new-relic-java-agent-updateupgrade"></a>New Relic Java エージェントの更新またはアップグレード

New Relic Java エージェントでは、JDK が定期的に更新またはアップグレードされます。 エージェントの更新またはアップグレードは、次のシナリオに影響を与える可能性があります。

* 更新またはアップグレードの前に New Relic Java エージェントを使用している既存のアプリケーションは変更されません。
* 更新またはアップグレード前に New Relic Java エージェントを使用している既存のアプリケーションでは、新しいバージョンの New Relic Java エージェントが使用されるようにするため、再起動または再デプロイする必要があります。
* 更新またはアップグレード後に作成される新しいアプリケーションでは、新しいバージョンの New Relic Java エージェントが使用されます。

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>Vnet インジェクション インスタンスの送信トラフィックの構成

Azure Spring Cloud の Vnet インジェクション インスタンスでは、New Relic Java エージェントに対して送信トラフィックが正しく構成されていることを確認する必要があります。 詳細については、[New Relic のネットワーク](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [分散トレースと App Insights](how-to-distributed-tracing.md)
