<properties linkid="dev-net-commons-tasks-diagnostics" urlDisplayName="診断" pageTitle="診断の使用方法 (.NET) - Azure の機能ガイド" metaKeywords="Azure 診断監視, ログ クラッシュ ダンプ C#" description="Azure で、デバッグ、パフォーマンス測定、監視、トラフィック解析などに診断データを使用する方法を説明します。" metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Azure の診断機能" authors=""  solutions="" writer="ryanwi" manager="" editor=""  />





# Azure の診断機能

Azure 診断を使用すると、Azure で実行している Worker ロールまたは Web ロールから診断データを収集できます。診断データは、デバッグ、トラブルシューティング、パフォーマンス測定、リソース使用状況の監視、トラフィック解析と容量計画、監査などに使用できます。

Azure SDK 2.0 以降を使用して Visual Studio 2012 または Visual Studio 2013 内で展開前または実行時に診断を構成することができます。詳細については、「[Azure 診断の構成][]」を参照してください。

ログやトレース方法を作成する方法と、診断機能やその他の手法で問題のトラブルシューティングを行う方法の詳細なガイダンスについては、「[Troubleshooting Best Practices for Developing Azure Applications (Azure アプリケーション開発のトラブルシューティングのベスト プラクティス)][]」を参照してください。

アプリケーション内で診断を手動で構成したり、診断モニターの構成をリモートで変更したりする方法の詳細については、「[Azure 診断を使用したログ データの収集][]」を参照してください。

<h2>次のステップ</h2>

-	[診断モニターの構成のリモート変更][]: アプリケーションを展開したら、診断構成を変更できます。
-	[How to monitor cloud services (クラウド サービスの監視方法)][]: クラウド サービスの主要パフォーマンス メトリックは [Azure の管理ポータル][]で監視できます。

<h2>その他のリソース</h2>

- [Azure 診断を使用したログ データの収集][]
- [Debugging a Azure Application (Azure アプリケーションのデバッグ)][]
- [Azure 診断の構成][]

  [Troubleshooting Best Practices for Developing Azure Applications (Azure アプリケーション開発のトラブルシューティングのベスト プラクティス)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh771389.aspx
  

  [Azure でのパフォーマンス カウンターの使用]: ../cloud-services-performance-testing-visual-studio-profiler/
  [How to monitor cloud services (クラウド サービスの監視方法)]: ../cloud-services-how-to-monitor/
  [DiagnosticMonitorTraceListener クラス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx
  [Azure 診断の構成ファイルの使用]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh411551.aspx
  [「IIS 7.0 Configuration Reference (IIS 7.0 構成リファレンス)」の「Adding Trace Failed Requests (失敗した要求のトレースの追加)」]: http://www.iis.net/ConfigReference/system.webServer/tracing/traceFailedRequests/add
  [Using Performance Counters in Azure (Azure でのパフォーマンス カウンターの使用)]: /ja-jp/develop/net/common-tasks/performance-profiling/
  [ローカル ストレージ リソースを構成する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758708.aspx
  [サーバー エクスプローラーを使用したストレージ リソースの参照]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff683677.aspx
  [Azure ストレージ エクスプローラー]: http://azurestorageexplorer.codeplex.com/
  [Azure Diagnostics Manager]: http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx
  [Azure 診断を使用したログ データの収集]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433048.aspx
  [Debugging a Azure Application (Azure アプリケーションのデバッグ)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee405479.aspx
  [Azure 診断の構成ファイルの使用]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh411551.aspx
  [LogLevel 列挙体]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.diagnostics.loglevel.aspx
  [OnStart メソッド]: http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
  [Azure 診断構成スキーマ]: http://msdn.microsoft.com/ja-jp/library/gg593185.aspx
  [テーブル ストレージ サービスを使用する方法]: http://www.windowsazure.com/ja-jp/develop/net/how-to-guides/table-services/
  [How to use the Azure Blob Storage Service (Azure BLOB ストレージ サービスの使用方法)]: http://www.windowsazure.com/ja-jp/develop/net/how-to-guides/blob-storage/
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [診断モニターの構成のリモート変更]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg432992.aspx
  [Azure 診断の構成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn186185.aspx  
   

