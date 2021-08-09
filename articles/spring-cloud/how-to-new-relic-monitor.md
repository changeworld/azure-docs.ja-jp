---
title: New Relic Java エージェントを使用して監視する方法
titleSuffix: Azure Spring Cloud
description: New Relic Java エージェントを使用して Azure Spring Cloud アプリを監視する方法について説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-java
ms.openlocfilehash: 61796641dbba6b02e8298f084f6c36908b053669
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758140"
---
# <a name="how-to-monitor-with-new-relic-java-agent-preview"></a>New Relic Java エージェントを使用して監視する方法 (プレビュー)

この機能によって、**New Relic** Java エージェントで Azure Spring Cloud アプリを監視することができます。

**New Relic** Java エージェントによって、以下を行えます。
* **New Relic** Java エージェントを使用する。
* **環境変数** を使用して **New Relic** Java エージェントを構成する。
* **New Relic** ダッシュボードからすべての監視データを確認する。

## <a name="prerequisites"></a>前提条件

* **New Relic** アカウント。
* [Azure CLI バージョン 2.0.67 以降](/cli/azure/install-azure-cli)。

## <a name="leverage-the-new-relic-java-in-process-agent"></a>New Relic Java インプロセス エージェントを利用する

エージェントを受け入れるには、次の手順に従います。

1. Azure Spring Cloud のインスタンスを作成します。

2. アプリケーションを作成します。

    ```shell
      az spring-cloud app create --name "appName" --is-public true \
      -s "resourceName" -g "resourceGroupName"
    ```

3. **New Relic** エージェントと **環境変数** を使用してデプロイを作成します。

    ```shell
    az spring-cloud app deploy --name "appName" --jar-path app.jar \
       -s "resourceName" -g "resourceGroupName" \
       --jvm-options="-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar" \
       --env NEW_RELIC_APP_NAME=appName NEW_RELIC_LICENSE_KEY=newRelicLicenseKey
    ```

  Azure Spring Cloud では、次のパスに **New Relic** Java エージェントが事前にインストールされます。 お客様は、アプリケーションの **Jvm オプション** からエージェントを利用できるだけでなく、[New Relic Java エージェント環境変数](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables)を使用してエージェントを構成することもできます。

  ```shell
  /opt/agents/newrelic/java/newrelic-agent.jar
  ```

## <a name="portal"></a>ポータル

次の手順で、このエージェントをポータルから利用することもできます。 

1. ナビゲーション ウィンドウの **[設定]** / **[アプリ]** からアプリを見つけます。

   [ ![監視するアプリを見つける](media/new-relic-monitoring/find-app.png) ](media/new-relic-monitoring/find-app.png)

2. アプリケーションをクリックして **[概要]** ページに移動します。

   [ ![[概要] ページ](media/new-relic-monitoring/overview-page.png) ](media/new-relic-monitoring/overview-page.png)

3. 左側のナビゲーション ウィンドウで **[構成]** をクリックして、アプリケーションの **環境変数** を追加、更新、削除します。

   [ ![環境の更新](media/new-relic-monitoring/configurations-update-environment.png) ](media/new-relic-monitoring/configurations-update-environment.png)

4. **[全般設定]** をクリックして、アプリケーションの **Jvm オプション** を追加、更新、削除します。

   [ ![JVM オプションの更新 ](media/new-relic-monitoring/update-jvm-option.png) ](media/new-relic-monitoring/update-jvm-option.png)

5. アプリケーション API/ゲートウェイの **[概要]** ページを **New Relic** ダッシュボードから表示します。

   [ ![アプリの [概要] ページ](media/new-relic-monitoring/app-summary-page.png) ](media/new-relic-monitoring/app-summary-page.png)

6. アプリケーション顧客サービスの **[概要]** ページを **New Relic** ダッシュボードから表示します。
 
   [ ![[顧客サービス] ページ](media/new-relic-monitoring/customers-service.png) ](media/new-relic-monitoring/customers-service.png)  

7. **New Relic** ダッシュボードから **[サービス マップ]** ページを表示します。  

   [ ![[サービス マップ] ページ](media/new-relic-monitoring/service-map.png) ](media/new-relic-monitoring/service-map.png) 

8. アプリケーション **JVM** を **New Relic** ダッシュボードから表示します。

   [ ![JVM ページ](media/new-relic-monitoring/jvm-page.png) ](media/new-relic-monitoring/jvm-page.png) 

9. アプリケーション プロファイルを **New Relic** ダッシュボードから表示します。

   [ ![アプリケーション プロファイル](media/new-relic-monitoring/profile-app.png) ](media/new-relic-monitoring/profile-app.png) 

## <a name="new-relic-java-agent-logging"></a>New Relic Java エージェントのログ記録

Azure Spring Cloud では、既定で **New Relic** Java エージェントのログが `STDOUT` に出力されます。 アプリケーション ログと統合されます。 アプリケーション ログから明示的なエージェント バージョンを取得できます。

**New Relic** エージェントのログは、以下から取得することもできます。

* Azure Spring Cloud のログ。
* Azure Spring Cloud Application Insights。
* Azure Spring Cloud LogStream。

**New Relic** によって提供される一部の環境変数 (`NEW_RELIC_LOG_LEVEL` など) を利用して **新しいエージェント** のログ記録を構成し、ログのレベルを制御できます。 詳細については、[New Relic の環境変数](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables)に関するページを参照してください。

> [!CAUTION]
> New Relic 用に Azure Spring Cloud によって提供されるログ記録の既定の動作をオーバーライド "*しない*" ことを強くお勧めします。 そうすると、上記のログ記録シナリオがブロックされ、ログ ファイルが失われる可能性があります。 たとえば、次の環境変数をアプリケーションに渡さないようにします。 再起動またはアプリケーションの再デプロイ後に、ログ ファイルが失われる可能性があります。
>
> * NEW_RELIC_LOG
> * NEW_RELIC_LOG_FILE_PATH

## <a name="new-relic-java-agent-updateupgrade"></a>New Relic Java エージェントの更新またはアップグレード

**New Relic** Java エージェントでは、JDK が定期的に更新またはアップグレードされます。 エージェントの更新またはアップグレードは、次のシナリオに影響を与える可能性があります。

* 更新またはアップグレードの前に **New Relic** Java エージェントを使用している既存のアプリケーションは変更されません。
* 更新またはアップグレード前に **New Relic** Java エージェントを使用している既存のアプリケーションでは、新しいバージョンの **New Relic** Java エージェントが使用されるようにするため、再起動または再デプロイする必要があります。
* 更新またはアップグレード後に作成される新しいアプリケーションでは、新しいバージョンの **New Relic** Java エージェントが使用されます。

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>Vnet インジェクション インスタンスの送信トラフィックの構成

Azure Spring Cloud の Vnet インジェクション インスタンスでは、**New Relic** Java エージェントに対して送信トラフィックが正しく構成されていることを確認する必要があります。 詳細については、[New Relic のネットワーク](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
*  [分散トレースと App Insights](how-to-distributed-tracing.md)