---
title: Azure App Service のパフォーマンスを監視する (Java) | Microsoft Docs
description: Java を使用した Azure App Service のアプリケーション パフォーマンスの監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: d673524b30eae13e24758aff23a68bd2b38c2e3e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699150"
---
# <a name="application-monitoring-for-azure-app-service-and-java"></a>Azure App Service と Java のアプリケーション監視

[Azure App Service](../../app-service/index.yml) で実行されている Java ベースの Web アプリケーションを監視するのに、コードを変更する必要はありません。 この記事では、Azure Monitor Application Insights の監視を有効にする手順を説明し、大規模なデプロイ プロセスを自動化する準備となるガイダンスを提供します。

## <a name="enable-application-insights"></a>Application Insights を有効にする

Azure App Service で実行されている Java アプリケーションのアプリケーション監視を有効にするには、Azure portal を使用することをお勧めします。 Azure portal でアプリケーション監視を有効にすると、Application Insights でアプリケーションが自動的にインストルメント化されます。  

### <a name="auto-instrumentation-through-azure-portal"></a>Azure portal を使用した自動インストルメンテーション

この方法を使用するのにコードの変更や高度な構成は必要ないので、Azure App Service の監視を始めるのに最も簡単な方法です。 追加の構成を適用してから、固有のシナリオに基づいて、[手動インストルメンテーション](./java-2x-get-started.md?tabs=maven)による高度な監視が必要かどうかを評価できます。

### <a name="enable-backend-monitoring"></a>バックエンドの監視を有効にする

1 回クリックするだけで、Azure App Service で実行されている Java アプリの監視を有効にすることができ、コードの変更は必要ありません。 Java 用の Application Insights は、Linux 上の App Service (コードベースとカスタムの両方のコンテナー) および Windows 上の App Service (コードベースのアプリ) と統合されています。 アプリケーションが監視される方法を知っておくことが重要です。 統合によって [Application Insights Java 3.x](./java-in-process-agent.md) が追加され、テレメトリが自動的に収集されます。

1. お使いのアプリ サービスの Azure コントロール パネルで **[Application Insights] を選択** し、 **[有効化]** を選択します。

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="[有効化] が選択されている Application Insights タブのスクリーンショット。"::: 

2. 新しいリソースを作成するか、このアプリケーションの既存の Application Insights リソースを選択します。

    > [!NOTE]
    > **[OK]** を選択して新しいリソースを作成すると、**監視の設定を適用する** ように求めるメッセージが表示されます。 **[続行]** を選択すると、新しい Application Insights リソースがアプリ サービスにリンクされ、**アプリ サービスの再起動がトリガー** されます。 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="リソース ドロップダウンの変更のスクリーンショット。"::: 

3. この手順は必須ではありません。 使用するリソースを指定したら、Java エージェントを構成できます。 Java エージェントを構成しない場合は、既定の構成が適用されます。 

    完全な[構成のセット](./java-standalone-config.md)を使用できます。必要なのは、有効な [json ファイル](./java-standalone-config.md#an-example)を貼り付けることだけです。 **プレビュー段階にある接続文字列とすべての構成を除外する** - 現在プレビュー段階の項目は、一般提供されたときに追加することができます。

    Azure portal で構成を変更すると APPLICATIONINSIGHTS_CONFIGURATION_FILE 環境変数が自動的に設定され、App Service の設定パネルに表示されます。 この変数には、Azure portal で Java アプリの構成テキスト ボックスに貼り付けた完全な json の内容が含まれます。 

    :::image type="content"source="./media/azure-web-apps-java/create-app-service-ai.png" alt-text="アプリケーションのインストルメント化のスクリーンショット。"::: 
    

## <a name="enable-client-side-monitoring"></a>クライアント側の監視を有効にする

Java アプリケーションでクライアント側の監視を有効にするには、[クライアント側の JavaScript SDK をアプリケーションに手動で追加する](./javascript.md)必要があります。

## <a name="automate-monitoring"></a>監視の自動化

### <a name="application-settings"></a>アプリケーションの設定

Application Insights でのテレメトリ収集を有効にするのに必要なのは、次のアプリケーション設定を設定することだけです。

|アプリ設定の名前 |  定義 | 値 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | ランタイムの監視を制御する | `~2` (Windows の場合) または `~3` (Linux の場合) |
|XDT_MicrosoftApplicationInsights_Java |  Java エージェントが含まれることを制御するフラグ | 0 または 1、Windows にのみ適用
|APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL | Application Insights と App Service の統合をデバッグする必要がある場合にのみ使用します | debug

:::image type="content"source="./media/azure-web-apps-java/application-settings-java.png" alt-text="利用可能な Application Insights の設定が表示された App Service のアプリケーション設定のスクリーンショット。"::: 

> [!NOTE]
> プロファイラーとスナップショット デバッガーは Java アプリケーションでは使用できません

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]

## <a name="troubleshooting"></a>トラブルシューティング

以下は、Azure App Service 上で実行されている Java ベースのアプリケーションについての、手順を追ったトラブルシューティング ガイドです。

1. `ApplicationInsightsAgent_EXTENSION_VERSION` アプリ設定が、Windows では "2 以下"、Linux では "3 以下" の値に設定されていることを確認します
1. ログ ファイルを調べて、エージェントが正常に開始されたことを確認します。 https://yoursitename.scm.azurewebsites.net/ でルート ディレクトリへの SSH の変更の下を見ると、ログ ファイルは LogFiles/ApplicationInsights の下にあります。 
  
    :::image type="content"source="./media/azure-web-apps-java/app-insights-java-status.png" alt-text="上記の結果ページのリンクのスクリーンショット。"::: 

1. Java アプリのアプリケーション監視を有効にした後、ライブ メトリックを確認することによって、エージェントが動作していることを検証できます。App Service にアプリをデプロイする前でも、環境からのいくつかの要求が表示されます。 テレメトリの完全なセットは、アプリがデプロイされて実行している場合にのみ利用可能であることに注意してください。 
1. エラーが表示されず、テレメトリがない場合は、APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL 環境変数を "debug" に設定します
1. Application Insights Java エージェントの最新バージョンを App Service で使用できない場合があります。最新バージョンがすべてのリージョンにロールアウトされるには、数か月かかることがあります。 App Service でアプリを監視するために最新バージョンの Java エージェントが必要な場合は、エージェントを手動でアップロードできます。 
    * Java エージェントの jar ファイルを App Service にアップロードします
        * [Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli) の最新バージョンを取得します
        * [Application Insights Java エージェント](./java-in-process-agent.md)の最新バージョンを取得します
        * Java エージェントを App Service にデプロイします。Java エージェントの jar をデプロイするためのサンプル コマンド (`az webapp deploy --src-path applicationinsights-agent-{VERSION_NUMBER}.jar --target-path java/applicationinsights-agent-{VERSION_NUMBER}.jar --type static --resource-group {YOUR_RESOURCE_GROUP} --name {YOUR_APP_SVC_NAME}`) を使用するか、[こちらのガイド](../../app-service/quickstart-java.md?tabs=javase&pivots=platform-linux#configure-the-maven-plugin)を使用して Maven プラグインでデプロイします
    * エージェントの jar ファイルをアップロードしたら、App Service の構成に移動し、新しい環境変数 JAVA_OPTS を追加して、その値を `-javaagent:D:/home/{PATH_TO_THE_AGENT_JAR}/applicationinsights-agent-{VERSION_NUMBER}.jar` に設定します
    * Application Insights のタブで Application Insights を無効にします
    * アプリを再起動する

    > [!NOTE]
    > JAVA_OPTS 環境変数を設定する場合は、ポータルで Application Insights を無効にする必要があります。 または、ポータルで Application Insights を有効にしたい場合は、App Service の構成設定で JAVA_OPTS 変数を設定していないことを確認してください。 


[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

## <a name="release-notes"></a>リリース ノート

最新の更新プログラムとバグ修正については、[リリース ノートを参照してください](web-app-extension-release-notes.md)。

## <a name="next-steps"></a>次のステップ

* [Application Insights で Azure Functions を監視](monitor-functions.md)します。
* [Azure Diagnostics](../agents/diagnostics-extension-to-application-insights.md) が Application Insights に送信されるように設定します。
* [サービスの正常性メトリックを監視](../data-platform.md)して、サービスの可用性と応答性を確認します。
* 操作イベントが発生したり、メトリックがしきい値を超えたりするたびに、[アラート通知を受け取り](../alerts/alerts-overview.md)ます。
* [JavaScript のアプリや Web ページに Application Insights](javascript.md) を使用して、Web ページを参照しているブラウザーからクライアント テレメトリを取得します。
* [可用性 Web テストを設定](monitor-web-app-availability.md) して、サイトがダウンした場合にアラートを送信するようにします。