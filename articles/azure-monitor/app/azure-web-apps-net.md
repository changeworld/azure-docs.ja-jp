---
title: Azure App Services のパフォーマンスを監視する ASP.NET | Microsoft Docs
description: ASP.NET を使用した Azure App Services のアプリケーション パフォーマンスの監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 97c0d2710d14e523338ac7cd670383caa75c7cef
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443606"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet"></a>Azure App Service と ASP.NET のアプリケーション監視 

[Azure App Services](../../app-service/index.yml) 上で実行されているご利用の ASP.NET ベースの Web アプリケーションで、これまでよりも簡単に監視を有効にすることができるようになりました。 以前は手動でアプリをインストルメント化する必要がありましたが、最新の拡張機能とエージェントが既定で App Service イメージに組み込まれるようになりました。 この記事では、Azure Monitor Application Insights の監視を有効にする手順を説明し、大規模なデプロイ プロセスを自動化する準備となるガイダンスを提供します。

> [!NOTE]
> **[開発ツール]**  >  **[拡張機能]** を使用して Application Insights のサイト拡張機能を手動で追加することは、非推奨になりました。 この拡張機能のインストール方法は、新しい各バージョンの手動更新が必要でした。 拡張機能の最新の安定版リリースが、App Service イメージの一部として[プレインストール](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)されるようになりました。 これらのファイルは `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` にあり、安定版リリースごとに自動的に更新されます。 エージェントベースの手順を実行して以下の監視を有効にすると、非推奨の拡張機能は自動的に削除されます。

> [!NOTE]
> エージェント ベースの監視と手動の SDK ベースのインストルメンテーションの両方が検出された場合は、手動のインストルメンテーション設定のみが受け付けられます。 これは、重複したデータが送信されないようにするためです。 この詳細については、以下の「[トラブルシューティング](#troubleshooting)」セクションを参照してください。

## <a name="enable-agent-based-monitoring"></a>エージェント ベースの監視を有効にする

> [!NOTE]
> APPINSIGHTS_JAVASCRIPT_ENABLED と urlCompression の組み合わせはサポートされていません。 詳細については、[トラブルシューティングのセクション](#appinsights_javascript_enabled-and-urlcompression-is-not-supported)の説明を参照してください。

1. お使いのアプリ サービスの Azure コントロール パネルで **[Application Insights] を選択** し、 **[有効化]** を選択します。

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="[有効化] が選択されている Application Insights タブのスクリーンショット。"::: 

2. 新しいリソースを作成するか、このアプリケーションの既存の Application Insights リソースを選択します。 

    > [!NOTE]
    > **[OK]** をクリックして新しいリソースを作成すると、**監視の設定を適用する** ように求めるメッセージが表示されます。 **[続行]** を選択すると、新しい Application Insights リソースがアプリ サービスにリンクされ、**アプリ サービスの再起動がトリガー** されます。 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="リソース ドロップダウンの変更のスクリーンショット。"::: 

3. 使用するリソースを指定した後、アプリケーションのプラットフォームごとのデータを Application Insights でどのように収集するかを選択できます。 ASP.NET アプリの監視は既定でオンであり、2 つの異なる収集レベルがあります。

    :::image type="content"source="./media/azure-web-apps-net/instrument-net.png" alt-text="Application Insights サイト拡張機能ページのスクリーンショット。[新しいリソースの作成] が選択されています。"::: 
 
     それぞれのルートで収集されるデータは次のとおりです。
            
    | Data | ASP.NET 基本収集 | ASP.NET 推奨収集 |
    | --- | --- | --- |
    | CPU、メモリ、および I/O の使用状況の傾向が追加されます |はい |はい |
    | 使用状況の傾向が収集され、可用性の結果からトランザクションへの相関関係が有効になります | はい |はい |
    | ホスト プロセスによって処理されていない例外が収集されます | はい |はい |
    | 負荷がかかっているときの APM メトリックの精度が上がります (サンプリングが使用される場合) | はい |はい |
    | 要求/依存関係の境界を越えてマイクロサービスが相互に関連付けられます | いいえ (シングルインスタンス APM 機能のみ) |はい |

4. 以前に applicationinsights.config ファイルで制御できたサンプリングを構成する場合、[アプリケーション設定] で対応するプレフィックス `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor` を使用して対話できるようになりました。 

    - たとえば、最初のサンプリング率を変更するには、`MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` と `100` の値の [アプリケーション設定] を作成できます。
    - サンプリングを無効にするには、`MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage` を `100` の値に設定します。
    - 次の設定がサポートされます。
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_EvaluationInterval`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MaxTelemetryItemsPerSecond`
        
    - サポートされるアダプティブ サンプリング テレメトリ プロセッサ設定と定義の一覧については、[コード](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs)と[サンプリング ドキュメント](./sampling.md#configuring-adaptive-sampling-for-aspnet-applications)を参照してください。


## <a name="enable-client-side-monitoring"></a>クライアント側の監視を有効にする

ASP.NET の場合、クライアント側の監視はオプトインです。 クライアント側の監視を有効にするには:

* **[設定]** **>** **[構成]**
   * [アプリケーション設定] で、**新しいアプリケーション設定** を作成します。

     名前: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     値: `true`

   * 設定を **[保存]** し、アプリを **[再起動]** します。

クライアント側の監視を無効にするには、[アプリケーション設定] から関連付けられているキーと値のペアを削除するか、値を false に設定します。

## <a name="automate-monitoring"></a>監視の自動化

Application Insights を使用したテレメトリの収集を有効にするのに必要なのは、アプリケーション設定の設定のみです。

:::image type="content"source="./media/azure-web-apps-net/application-settings-net.png" alt-text="Application Insights の設定が表示された App Service のアプリケーション設定のスクリーンショット。"::: 

### <a name="application-settings-definitions"></a>アプリケーション設定の定義

|アプリ設定の名前 |  定義 | 値 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | メインの拡張機能で、実行時の監視を制御します。 | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  既定のモードでは、最適なパフォーマンスを保証するために、重要な機能のみが有効になります。 | `default` または `recommended`。 |
|InstrumentationEngine_EXTENSION_VERSION | バイナリ再書き込みエンジン `InstrumentationEngine` がオンにされるかどうかを制御します。 この設定は、パフォーマンスに影響し、コールド スタート/起動時間に影響を与えます。 | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | SQL と Azure テーブル テキストが依存関係呼び出しと共にキャプチャされるかどうかを制御します。 パフォーマンスの警告: アプリケーションのコールド スタートアップ時間が影響を受けます。 この設定には `InstrumentationEngine` が必要です。 | `~1` |

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>監視拡張機能またはエージェントをアップグレードする - .NET 

### <a name="upgrade-from-versions-289-and-up"></a>バージョン 2.8.9 以降からアップグレードする

バージョン 2.8.9 からのアップグレードは自動的に実行され、追加の操作は必要ありません。 新しい監視ビットは、バックグラウンドでターゲット アプリ サービスに配信され、アプリケーションの再起動時に取得されます。

実行している拡張機能のバージョンを確認するには、`https://yoursitename.scm.azurewebsites.net/ApplicationInsights` に移動します。

:::image type="content"source="./media/azure-web-apps/extension-version.png" alt-text="実行している拡張機能のバージョンを確認するための URL パスのスクリーンショット。" border="false"::: 

### <a name="upgrade-from-versions-100---265"></a>バージョン 1.0.0 - 2.6.5 からのアップグレード

バージョン 2.8.9 以降では、プレインストールされたサイト拡張機能が使用されています。 以前のバージョンを使用している場合は、次の 2 つの方法のいずれかを使用して更新できます。

* [ポータル経由で有効にしてアップグレードする](#enable-agent-based-monitoring) (Azure App Service の Application Insights 拡張機能がインストールされている場合でも、UI には **[有効]** ボタンのみが表示されます。 背後では、以前のプライベート サイト拡張機能が削除されます)。

* [PowerShell を使用してアップグレードする](#enable-through-powershell):

    1. プレインストールされたサイト拡張機能 ApplicationInsightsAgent を有効にするようにアプリケーション設定を指定します。 「[PowerShell を使用して有効にする](#enable-through-powershell)」を参照してください。
    2. Azure App Service の Application Insights 拡張機能という名前のプライベート サイト拡張機能を手動で削除します。

2\.5.1 より前のバージョンからアップグレードする場合は、ApplicationInsigths dll がアプリケーションの bin フォルダーから削除されていることを確認します。[トラブルシューティングの手順](#troubleshooting)を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

> [!NOTE]
> Azure App Services で `ASP.NET` ランタイムを使用して Web アプリを作成すると、1 つの静的な HTML ページがスターター Web サイトとしてデプロイされます。 既定のテンプレートに関する問題のトラブルシューティングはお勧め **しません**。 問題のトラブルシューティングを行う前に、アプリケーションを展開します。

以下は、Azure App Services 上で実行されている ASP.NET ベースのアプリケーションの拡張機能/エージェント ベースの監視について、手順を追って説明するトラブルシューティング ガイドです。

1. `ApplicationInsightsAgent_EXTENSION_VERSION` アプリ設定が "2 以下" の値に設定されていることを確認します。
2. [http://.azurewebsites.net/admin](`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`) を参照します。  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="上記の結果ページのリンクのスクリーンショット。"border ="false"::: 
    
    - `Application Insights Extension Status` が `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.` であることを確認します 
    
         実行中ではない場合は、[Application Insights の監視を有効にする手順](#enable-agent-based-monitoring)を実行します。

    - 状態ソースが存在し、以下のようになっていることを確認します。`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         似た値が存在しない場合は、アプリケーションが現在実行されていないか、サポートされていないことを意味します。 アプリケーションが実行されていることを確認するには、手動でアプリケーションの URL/アプリケーション エンドポイントにアクセスしてみてください。これで、ランタイム情報を使用できるようになります。

    - `IKeyExists` が `true` であることを確認します。`false` の場合は、お客様の ikey guid を指定した `APPINSIGHTS_INSTRUMENTATIONKEY` と `APPLICATIONINSIGHTS_CONNECTION_STRING` をアプリケーション設定に追加します。

    - `AppAlreadyInstrumented`、`AppContainsDiagnosticSourceAssembly`、および `AppContainsAspNetTelemetryCorrelationAssembly` のエントリがないことを確認します。

         これらのエントリのいずれかが存在する場合は、アプリケーションから `Microsoft.ApplicationInsights`、`System.Diagnostics.DiagnosticSource`、および `Microsoft.AspNet.TelemetryCorrelation` のパッケージを削除します。

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Web アプリでデプロイされた既定の Web サイトでは、クライアント側の自動監視はサポートされません

Azure App Services で `ASP.NET` ランタイムを使用して Web アプリを作成すると、1 つの静的な HTML ページがスターター Web サイトとしてデプロイされます。 静的な Web ページには、IIS の ASP.NET マネージド Web パーツも読み込まれます。 これにより、サーバー側のコードなし監視をテストすることはできますが、クライアント側の自動監視はサポートされません。

Azure App Services Web アプリで ASP.NET に対するコードなしサーバーとクライアント側の監視をテストする場合は、[ASP.NET Framework Web アプリの作成](../../app-service/quickstart-dotnetcore.md?tabs=netframework48)に関する公式のガイドに従って、この記事にある監視を有効にするための手順を使用することをお勧めします。


### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED と urlCompression はサポートされていません

APPINSIGHTS_JAVASCRIPT_ENABLED=true を使用する場合 (この場合、コンテンツがエンコードされます)、次のようなエラーが発生する可能性があります。

- 500 URL 書き換えエラー
- 500.53 URL 書き換えモジュール エラー。HTTP 応答のコンテンツがエンコード ('gzip') されている場合、アウトバウンド書き換えルールを適用することはできませんというメッセージを伴います。

これは、APPINSIGHTS_JAVASCRIPT_ENABLED アプリケーション設定が true に設定されており、同時に content-encoding が存在することが原因です。 このシナリオはまだサポートされていません。 回避策は、アプリケーション設定から APPINSIGHTS_JAVASCRIPT_ENABLED を削除することです。 残念ながら、これは、クライアント/ブラウザー側の JavaScript インストルメンテーションがまだ必要な場合、Web ペーでは手動の SDK 参照が必要であることを意味します。 JavaScript SDK での手動のインストルメンテーションに関する[手順](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup)に従ってください。

Application Insights エージェント/拡張機能の最新情報については、[リリース ノート](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)のページを参照してください。

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>PHP および WordPress はサポートされていない

PHP および WordPress サイトはサポートされていません。 現時点では、これらのワークロードをサーバー側で監視するために正式にサポートされている SDK/エージェントはありません。 ただし、クライアント側の JavaScript を Web ページに追加することで PHP または WordPress サイトでクライアント側のトランザクションを手動でインストルメント化するのは、[JavaScript SDK](./javascript.md) を使用することで実行できます。

以下の表は、これらの値の意味、その根本的な原因、推奨される修正について詳細に説明したものです。

|問題の値|説明|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | この値は、SDK の一部の側面が既にアプリケーションに存在することが拡張機能で検出され、拡張機能が停止されることを示します。 これは、`System.Diagnostics.DiagnosticSource`、`Microsoft.AspNet.TelemetryCorrelation`、または `Microsoft.ApplicationInsights` への参照が原因である可能性があります  | これらの参照を削除します。 これらの参照の一部は、特定の Visual Studio テンプレートによって既定で追加されており、以前のバージョンの Visual Studio で `Microsoft.ApplicationInsights` への参照が追加されている可能性があります。
|`AppAlreadyInstrumented:true` | アプリケーションで ASP.NET Core 2.1 または 2.2 をターゲットにしている場合、この値は、SDK の一部の側面が既にアプリケーションに存在することが拡張機能で検出され、拡張機能が停止されることを示します。 | .NET Core 2.1、2.2 をご利用のお客様は、代わりに Microsoft.AspNetCore.App メタパッケージを使用することを[お勧めします](https://github.com/aspnet/Announcements/issues/287)。 さらに、ポータルで [Interop with Application Insights SDK]\(Application Insights SDK との相互運用\) をオンにします (上記の手順を参照してください)。|
|`AppAlreadyInstrumented:true` | この値は、以前のデプロイのアプリ フォルダーに上記の dll が存在する場合でも発生する可能性があります。 | アプリ フォルダーを消去し、これらの dll が削除されたことを確認してください。 ローカル アプリの bin ディレクトリと App Service の wwwroot ディレクトリの両方を確認します。 (App Service Web アプリの wwwroot ディレクトリを確認するには、次のようにします。高度なツール (Kudu) > デバッグ コンソール > CMD > home\site\wwwroot)。
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | この値は、アプリケーション内の `Microsoft.AspNet.TelemetryCorrelation` への参照が拡張機能で検出され、拡張機能が停止されることを示します。 | 参照を削除します。
|`AppContainsDiagnosticSourceAssembly**:true`|この値は、アプリケーション内の `System.Diagnostics.DiagnosticSource` への参照が拡張機能で検出され、拡張機能が停止されることを示します。| ASP.NET の場合は、参照を削除します。 
|`IKeyExists:false`|この値は、インストルメンテーション キーが AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY` に存在しないことを示します。 考えられる原因:値が誤って削除された、自動化スクリプトで値を設定し忘れたなどの原因が考えられます。 | 設定が App Service アプリケーション設定に存在することを確認します。

## <a name="release-notes"></a>リリース ノート

最新の更新プログラムとバグ修正については、[リリース ノートを参照してください](web-app-extension-release-notes.md)。

## <a name="next-steps"></a>次のステップ

* [実行中のアプリに対してプロファイラーを実行](./profiler.md)します。
* [Application Insights で Azure Functions を監視](monitor-functions.md)します。
* [Azure Diagnostics](../agents/diagnostics-extension-to-application-insights.md) が Application Insights に送信されるように設定します。
* [サービスの正常性メトリックを監視](../data-platform.md)して、サービスの可用性と応答性を確認します。
* 操作イベントが発生したり、メトリックがしきい値を超えたりするたびに、[アラート通知を受け取り](../alerts/alerts-overview.md)ます。
* [JavaScript のアプリや Web ページに Application Insights](javascript.md) を使用して、Web ページを参照しているブラウザーからクライアント テレメトリを取得します。
* [可用性 Web テストを設定](monitor-web-app-availability.md) して、サイトがダウンした場合にアラートを送信するようにします。
