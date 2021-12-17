---
title: Azure App Services のパフォーマンス .NET Core を監視する | Microsoft Docs
description: ASP.NET Core を使用した Azure App Services のアプリケーション パフォーマンスの監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 747b79affc2cb2ef862c1fb4000879ddbbe5b9f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308790"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet-core"></a>Azure App Service と ASP.NET Core のアプリケーション監視 

[Azure App Services](../../app-service/index.yml) 上で実行されているご利用の ASP.NET Core ベースの Web アプリケーションでは、監視の有効化はこれまでよりも簡単になりました。 以前は手動でアプリをインストルメント化する必要がありましたが、最新の拡張機能とエージェントが既定で App Service イメージに組み込まれるようになりました。 この記事では、Azure Monitor Application Insights の監視を有効にする手順を説明し、大規模なデプロイ プロセスを自動化する準備となるガイダンスを提供します。

## <a name="enable-agent-based-monitoring"></a>エージェント ベースの監視を有効にする

# <a name="windows"></a>[Windows](#tab/Windows)

> [!IMPORTANT]
> Windows での自動インストルメンテーションに対してサポートされる ASP.NET Core バージョンは、ASP.NET Core 3.1、5.0、6.0 です。 バージョン 2.0、2.1、2.2、および 3.0 は廃止され、サポートされなくなりました。 自動インストルメンテーションを機能させるには、[サポートされているバージョン](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)の .NET Core にアップグレードしてください。

[自己完結型デプロイのトリミング](/dotnet/core/deploying/trimming/trim-self-contained)は **サポートされていません**。 代わりに、コードを介した[手動インストルメンテーション](./asp-net-core.md)を使用してください。

App Service リソースを使用した Application Insights の設定を開始するには、以下の[「監視を有効にする」セクション](#enable-monitoring )を参照してください。 

# <a name="linux"></a>[Linux](#tab/Linux)

> [!IMPORTANT]
> Linux での自動インストルメンテーションでは、ASP.NET Core 6.0 のみがサポートされています。

> [!NOTE]
> Linux の自動インストルメンテーション App Services ポータルの有効化は、パブリック プレビュー段階にあります。 これらのプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

[自己完結型デプロイのトリミング](/dotnet/core/deploying/trimming/trim-self-contained)は **サポートされていません**。 代わりに、コードを介した[手動インストルメンテーション](./asp-net-core.md)を使用してください。

App Service リソースを使用した Application Insights の設定を開始するには、以下の[「監視を有効にする」セクション](#enable-monitoring )を参照してください。 

---
 

### <a name="enable-monitoring"></a>監視を有効にする 

1. お使いのアプリ サービスの Azure コントロール パネルで **[Application Insights] を選択** し、 **[有効化]** を選択します。

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="[有効化] が選択されている Application Insights タブのスクリーンショット。"::: 

2. 新しいリソースを作成するか、このアプリケーションの既存の Application Insights リソースを選択します。

    > [!NOTE]
    > **[OK]** をクリックして新しいリソースを作成すると、**監視の設定を適用する** ように求めるメッセージが表示されます。 **[続行]** を選択すると、新しい Application Insights リソースがアプリ サービスにリンクされ、**アプリ サービスの再起動がトリガー** されます。 

    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="リソース ドロップダウンの変更のスクリーンショット。"::: 

2. 使用するリソースを指定した後、アプリケーションのプラットフォームごとのデータを Application Insights でどのように収集するかを選択できます。 ASP.NET Core では、 **[推奨収集]** または **[無効]** が提供されます。

    :::image type="content"source="./media/azure-web-apps-net-core/instrument-net-core.png" alt-text="[アプリケーションをインストルメント化する] セクションのスクリーンショット。"::: 


## <a name="enable-client-side-monitoring"></a>クライアント側の監視を有効にする

アプリ設定 'APPINSIGHTS_JAVASCRIPT_ENABLED' の有無に関係なく、ASP.NET Core アプリと **推奨収集** の組み合わせの場合、クライアント側の監視は **既定で有効** です。

何らかの理由でクライアント側の監視を無効にするには、次の手順を実行します。

* **[設定]** **>** **[構成]**
   * [アプリケーション設定] で、**新しいアプリケーション設定** を作成します。

     名前: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     値: `false`

   * 設定を **[保存]** し、アプリを **[再起動]** します。


## <a name="automate-monitoring"></a>監視の自動化

Application Insights を使用したテレメトリの収集を有効にするのに必要なのは、アプリケーション設定を行うことだけです。

:::image type="content"source="./media/azure-web-apps-net-core/application-settings-net-core.png" alt-text="Application Insights の設定が表示された App Service のアプリケーション設定のスクリーンショット。"::: 


### <a name="application-settings-definitions"></a>アプリケーション設定の定義

|アプリ設定の名前 |  定義 | 値 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | メインの拡張機能で、実行時の監視を制御します。 | `~2` (Windows の場合) または `~3` (Linux の場合) |
|XDT_MicrosoftApplicationInsights_Mode |  既定のモードでは、最適なパフォーマンスを保証するために、重要な機能のみが有効になります。 | `disabled` または `recommended`。 |
|XDT_MicrosoftApplicationInsights_PreemptSdk | ASP.NET Core アプリの場合のみ。 Application Insights SDK で相互運用を有効にします。 拡張機能を SDK とサイドバイサイドで読み込み、それを使用してテレメトリを送信します (Application Insights SDK を無効にします)。 |`1`|


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
> Azure App Services で `ASP.NET Core` ランタイムを使用して Web アプリを作成すると、1 つの静的な HTML ページがスターター Web サイトとしてデプロイされます。 既定のテンプレートに関する問題のトラブルシューティングはお勧め **しません**。 問題のトラブルシューティングを行う前に、アプリケーションをデプロイしてください。

以下は、Azure App Services 上で実行されている ASP.NET Core ベースのアプリケーションの拡張機能/エージェント ベースの監視について、手順を追って説明するトラブルシューティング ガイドです。

# <a name="windows"></a>[Windows](#tab/windows)

1. `ApplicationInsightsAgent_EXTENSION_VERSION` アプリ設定が "2 以下" の値に設定されていることを確認します。
2. [http://.azurewebsites.net/admin](`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`) を参照します。  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="上記の結果ページのリンクのスクリーンショット。"border ="false"::: 
    
    - `Application Insights Extension Status` が `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.` であることを確認します 
    
         実行中ではない場合は、[Application Insights の監視を有効にする手順](#enable-agent-based-monitoring)を実行します。

    - 状態ソースが存在し、以下のようになっていることを確認します。`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         似た値が存在しない場合は、アプリケーションが現在実行されていないか、サポートされていないことを意味します。 アプリケーションが実行されていることを確認するには、手動でアプリケーションの URL/アプリケーション エンドポイントにアクセスしてみてください。これで、ランタイム情報を使用できるようになります。

    - `IKeyExists` が `true` であることを確認します。`false` の場合は、お客様の ikey guid を指定した `APPINSIGHTS_INSTRUMENTATIONKEY` と `APPLICATIONINSIGHTS_CONNECTION_STRING` をアプリケーション設定に追加します。

    -  アプリケーションで Application Insights パッケージを参照している場合 (たとえば、以前に [ASP.NET Core SDK](./asp-net-core.md) を使用してアプリをインストルメント化した場合 (またはインストルメント化しようとした場合))、App Service の統合が有効にならず、データが Application Insights に表示されないことがあります。 この問題を修正するには、ポータルで [Application Insights SDK との相互運用] をオンにすると、Application Insights にデータが表示され始めます。 
    - 
        > [!IMPORTANT]
        > この機能はプレビュー状態です 

        :::image type="content"source="./media/azure-web-apps-net-core/interop.png" alt-text="相互運用設定が有効になっているスクリーンショット。"::: 
        
        これで、Application Insights SDK がもともと使用されていたか、使用しようとしていた場合でも、コード不要アプローチを使用してデータが送信されるようになりました。

        > [!IMPORTANT]
        > アプリケーションが Application Insights SDK を使用してテレメトリを送信していた場合、そのようなテレメトリは無効になります。つまり、たとえば、Track*() メソッドなどのカスタム テレメトリ (存在する場合)、およびサンプリングなどのカスタム設定は無効になります。 

# <a name="linux"></a>[Linux](#tab/linux)

1. `ApplicationInsightsAgent_EXTENSION_VERSION` アプリ設定が "3 以下" の値に設定されていることを確認します。
2. */home\LogFiles\ApplicationInsights\status* に移動し、*status_557de146e7fa_27_1.json* を開きます。

    `AppAlreadyInstrumented` が false、`AiHostingStartupLoaded` が true、および `IKeyExists` が true に設定されていることを確認します。

    JSON ファイルの例を次に示します。

    ```json
        "AppType":".NETCoreApp,Version=v6.0",
                
        "MachineName":"557de146e7fa",
                
        "PID":"27",
                
        "AppDomainId":"1",
                
        "AppDomainName":"dotnet6demo",
                
        "InstrumentationEngineLoaded":false,
                
        "InstrumentationEngineExtensionLoaded":false,
                
        "HostingStartupBootstrapperLoaded":true,
                
        "AppAlreadyInstrumented":false,
                
        "AppDiagnosticSourceAssembly":"System.Diagnostics.DiagnosticSource, Version=6.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51",
                
        "AiHostingStartupLoaded":true,
                
        "IKeyExists":true,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "ConnectionString":"InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/"
    
    ```
    
    `AppAlreadyInstrumented` が true の場合、これは、SDK の一部の側面が既にアプリケーションに存在することが拡張機能で検出され、拡張機能が停止されることを示します。

##### <a name="no-data"></a>データなし

1. アプリをホストしているプロセスを一覧表示して識別します。 ターミナルに移動し、コマンド ラインで「`ps ax`」と入力します。 
    
    出力は次のようになります。 

   ```bash
     PID TTY      STAT   TIME COMMAND
    
        1 ?        SNs    0:00 /bin/bash /opt/startup/startup.sh
    
       19 ?        SNs    0:00 /usr/sbin/sshd
    
       27 ?        SNLl   5:52 dotnet dotnet6demo.dll
    
       50 ?        SNs    0:00 sshd: root@pts/0
    
       53 pts/0    SNs+   0:00 -bash
    
       55 ?        SNs    0:00 sshd: root@pts/1
    
       57 pts/1    SNs+   0:00 -bash
   ``` 


1. 次に、アプリ プロセスから環境変数を一覧表示します。 コマンド ラインで「`cat /proc/27/environ | tr '\0' '\n`」と入力します。
    
    出力は次のようになります。 

    ```bash
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
    
    DOTNET_STARTUP_HOOKS=/DotNetCoreAgent/2.8.39/StartupHook/Microsoft.ApplicationInsights.StartupHook.dll
    
    APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/
    
    ```
    
1. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_STARTUP_HOOKS`、`APPLICATIONINSIGHTS_CONNECTION_STRING` が設定されていることを確認します。

---

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Web アプリでデプロイされた既定の Web サイトでは、クライアント側の自動監視はサポートされません

Azure App Services で `ASP.NET Core` ランタイムを使用して Web アプリを作成すると、1 つの静的な HTML ページがスターター Web サイトとしてデプロイされます。 静的な Web ページには、IIS の ASP.NET マネージド Web パーツも読み込まれます。 これで、サーバー側のコードなし監視をテストすることはできますが、クライアント側の自動監視はサポートされません。

Azure App Service Web アプリで ASP.NET Core のサーバーとクライアント側のコードなし監視をテストする場合は、[ASP.NET Core Web アプリの作成](../../app-service/quickstart-dotnetcore.md)に関する公式のガイドに従うことをお勧めします。 次に、現在の記事の手順を使用して監視を有効にします。

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>PHP および WordPress はサポートされていない

PHP および WordPress サイトはサポートされていません。 現時点では、これらのワークロードをサーバー側で監視するために正式にサポートされている SDK/エージェントはありません。 ただし、クライアント側の JavaScript を Web ページに追加することで PHP または WordPress サイトでクライアント側のトランザクションを手動でインストルメント化するのは、[JavaScript SDK](./javascript.md) を使用することで実行できます。

以下の表は、これらの値の意味、その根本的な原因、推奨される修正について詳細に説明したものです。

|問題の値 |説明 |Fix |
|---- |----|---|
| `AppAlreadyInstrumented:true` | この値は、SDK の一部の側面が既にアプリケーションに存在することが拡張機能で検出され、拡張機能が停止されることを示します。 これは、`Microsoft.ApplicationInsights.AspNetCore` または `Microsoft.ApplicationInsights` への参照が原因である可能性があります  | これらの参照を削除します。 これらの参照の一部は、特定の Visual Studio テンプレートによって既定で追加されており、以前のバージョンの Visual Studio で `Microsoft.ApplicationInsights` への参照が追加されている可能性があります。 |
|`AppAlreadyInstrumented:true` | アプリケーションで ASP.NET Core 2.1 または 2.2 をターゲットにしている場合、この値は、SDK の一部の側面が既にアプリケーションに存在することが拡張機能で検出され、拡張機能が停止されることを示します。 | .NET Core 2.1、2.2 をご利用のお客様は、代わりに Microsoft.AspNetCore.App メタパッケージを使用することを[お勧めします](https://github.com/aspnet/Announcements/issues/287)。 さらに、ポータルで [Interop with Application Insights SDK]\(Application Insights SDK との相互運用\) をオンにします (上記の手順を参照してください)。|
|`AppAlreadyInstrumented:true` | この値は、以前のデプロイのアプリ フォルダーに Microsoft.ApplicationsInsights dll が存在することが原因で発生する可能性もあります。 | アプリ フォルダーを消去し、これらの dll が削除されたことを確認してください。 ローカル アプリの bin ディレクトリと App Service の wwwroot ディレクトリの両方を確認します。 (App Service Web アプリの wwwroot ディレクトリを確認するには、次のようにします。高度なツール (Kudu) > デバッグ コンソール > CMD > home\site\wwwroot)。 |
|`IKeyExists:false`|この値は、インストルメンテーション キーが AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY` に存在しないことを示します。 考えられる原因:値が誤って削除された、自動化スクリプトで値を設定し忘れたなどの原因が考えられます。 | 設定が App Service アプリケーション設定に存在することを確認します。 |

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
