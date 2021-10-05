---
title: Node.js を使用する Azure App Service のパフォーマンスを監視する | Microsoft Docs
description: Node.js を使用する Azure App Service のアプリケーション パフォーマンスの監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 5f80666d4a184da40979a38f6d7f17782291c9e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699140"
---
# <a name="application-monitoring-for-azure-app-service-and-nodejs"></a>Azure App Service と Node.js でのアプリケーションの監視

[Azure App Service](../../app-service/index.yml) で実行されている Node.js ベースの Web アプリケーションで、これまでよりも簡単に監視を有効にできるようになりました。 以前は手動でアプリをインストルメント化する必要がありましたが、最新の拡張機能とエージェントが既定で App Service イメージに組み込まれるようになりました。 この記事では、Azure Monitor Application Insights の監視を有効にする手順を説明し、大規模なデプロイ プロセスを自動化する準備となるガイダンスを提供します。

> [!NOTE]
> エージェント ベースの監視と手動の SDK ベースのインストルメンテーションの両方が検出された場合は、手動のインストルメンテーション設定のみが受け付けられます。 これは、重複したデータが送信されないようにするためです。 この詳細については、以下の「[トラブルシューティング](#troubleshooting)」セクションを参照してください。

## <a name="enable-agent-based-monitoring"></a>エージェント ベースの監視を有効にする

コードを変更することなく、いくつかの簡単な手順だけで、Azure App Service で実行されている Node.js アプリを監視できます。 Node.js アプリケーション用の Application Insights は、Linux 上の App Service (コードベースとカスタムの両方のコンテナー) および Windows 上の App Service (コードベースのアプリ) と統合されています。 統合はパブリック プレビュー段階です。 統合により、GA である Node.js SDK が追加されます。 

1. お使いのアプリ サービスの Azure コントロール パネルで **[Application Insights] を選択** し、 **[有効化]** を選択します。

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="[有効化] が選択されている Application Insights タブのスクリーンショット。"::: 

2. 新しいリソースを作成するか、このアプリケーションの既存の Application Insights リソースを選択します。

     > [!NOTE]
     > **[OK]** をクリックして新しいリソースを作成すると、**監視の設定を適用する** ように求めるメッセージが表示されます。 **[続行]** を選択すると、新しい Application Insights リソースがアプリ サービスにリンクされ、**アプリ サービスの再起動がトリガー** されます。 
    
    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="リソース ドロップダウンの変更のスクリーンショット。"::: 
    
3. 使用するリソースを指定すると、準備は完了です。 


    :::image type="content"source="./media/azure-web-apps-nodejs/app-service-node.png" alt-text="アプリケーションのインストルメント化のスクリーンショット。"::: 


## <a name="enable-client-side-monitoring"></a>クライアント側の監視を有効にする

Node.js アプリケーションでクライアント側の監視を有効にするには、[クライアント側の JavaScript SDK をアプリケーションに手動で追加する](./javascript.md)必要があります。

## <a name="automate-monitoring"></a>監視の自動化

Application Insights を使用したテレメトリの収集を有効にするのに必要なのは、アプリケーション設定の設定のみです。

:::image type="content"source="./media/azure-web-apps-nodejs/application-settings-nodejs.png" alt-text="利用可能な Application Insights の設定が表示された App Service のアプリケーション設定のスクリーンショット。"::: 


### <a name="application-settings-definitions"></a>アプリケーション設定の定義

|アプリ設定の名前 |  定義 | 値 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | メインの拡張機能で、実行時の監視を制御します。 | Windows では `~2`、Linux では `~3` |
|XDT_MicrosoftApplicationInsights_NodeJS |  node.js エージェントを含めるかどうかを制御するフラグ。 | 0 または 1、Windows にのみ適用。 |


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="troubleshooting"></a>トラブルシューティング

以下は、Azure App Service 上で実行されている Node.js ベースのアプリケーションの拡張機能およびエージェント ベースの監視について、手順を追って説明するトラブルシューティング ガイドです。

# <a name="windows"></a>[Windows](#tab/windows)

1. `ApplicationInsightsAgent_EXTENSION_VERSION` アプリ設定が "2 以下" の値に設定されていることを確認します。
2. [http://.azurewebsites.net/admin](`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`) を参照します。  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="上記の結果ページのリンクのスクリーンショット。"border ="false"::: 

    - `Application Insights Extension Status` が `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.` であることを確認します 

         実行中ではない場合は、[Application Insights の監視を有効にする手順](#enable-agent-based-monitoring)を実行します。

    - *D:\local\Temp\status.json* に移動して、*status.json* を開きます。

    `SDKPresent` が false に設定され、`AgentInitializedSuccessfully` が true に設定され、`IKey` に有効なキーが設定されていることを確認します。

    JSON ファイルの例を次に示します。

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    `SDKPresent` が true の場合、これは、SDK の一部の側面が既にアプリケーションに存在することが拡張機能で検出され、拡張機能が停止されることを示します。


# <a name="linux"></a>[Linux](#tab/linux)

1. `ApplicationInsightsAgent_EXTENSION_VERSION` アプリ設定が "3 以下" の値に設定されていることを確認します。
2. */var/log/applicationinsights/* に移動して、*status.json* を開きます。

    `SDKPresent` が false に設定され、`AgentInitializedSuccessfully` が true に設定され、`IKey` に有効なキーが設定されていることを確認します。

    JSON ファイルの例を次に示します。

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    `SDKPresent` が true の場合、これは、SDK の一部の側面が既にアプリケーションに存在することが拡張機能で検出され、拡張機能が停止されることを示します。
---

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