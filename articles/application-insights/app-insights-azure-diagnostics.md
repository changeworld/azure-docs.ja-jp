---
title: "Azure 診断ログを Application Insights に送信する | Microsoft Docs"
description: "Application Insights ポータルに送信される Azure Cloud Services診断ログの詳細を構成します。"
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge
ms.assetid: a67dd20a-fc5d-4391-ba63-bfe164fb62f7
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2843ff25c118afe5d5421b322b529e9a9f2d5036


---
# <a name="configure-azure-diagnostic-logging-to-application-insights"></a>Application Insights に対する Azure 診断ログの構成
Microsoft Azure で Cloud Services プロジェクトまたは仮想マシンをセットアップするときに、 [Azure は診断ログを生成できます](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)。 これを Application Insights に送信し、Application Insights SDK によってアプリ内から送信された診断および使用テレメトリと共に分析できます。 Azure のログには、開始、停止、クラッシュ、パフォーマンス カウンターなど、アプリの管理でのイベントが含まれます。 また、ログにはアプリでの System.Diagnostics.Trace の呼び出しも含まれます。

この記事では、診断キャプチャの構成について詳しく説明します。

Azure SDK 2.8 以降を Visual Studio にインストールする必要があります。

## <a name="get-an-application-insights-resource"></a>Application Insights リソースを取得する
最善の結果を得るには、[Application Insights SDK を Cloud Services アプリの各ロールに追加する](app-insights-cloudservices.md)か、または [VM 内で実行する任意のアプリに追加](app-insights-overview.md)します。 そうすれば、診断データを送信して同じ Application Insights リソースで分析および表示できます。

または、SDK を使用したくない場合は (アプリがすでに活動中の場合など)、Azureポータルで [新しい Application Insights リソースを作成する](app-insights-create-new-resource.md) こともできます。 アプリケーションの種類として **Azure 診断** を選択します。

## <a name="send-azure-diagnostics-to-application-insights"></a>Azure 診断を Application Insights に送信する
アプリ プロジェクトを更新できる場合は、Visual Studio で各ロールを選択し、[プロパティ] を選択して、[構成] タブで **[診断を Application Insights に送信する]**をオンにします。

または、Publish コマンドを使用してアプリをアップロードする際に、[診断] ページで [Application Insights] オプションを選択できます。

アプリがまだ活動中の場合は、Visual Studio のサーバー エクスプローラーまたは Cloud Services エクスプローラーを使用して、アプリのプロパティを開きます。 **[診断を Application Insights に送信する]**をオンにします。

いずれの場合も、作成した Application Insights リソースの詳細を求められます。

個別のロールのデータを個別のリソースに送信することをお勧めします。 ポータルで[ダッシュ ボード](app-insights-dashboards.md)を作成することにより、それらのメトリック グラフを並べて表示できます。

[Cloud Services アプリ用の Application Insights の設定の詳細についてはこちらを参照してください](app-insights-cloudservices.md)。

## <a name="configuring-the-azure-diagnostics-adapter"></a>Azure 診断アダプターを構成する
ここでは、Application Insights に送信するログの部分を選択する方法を説明します。 既定では、Microsoft Azure イベント、パフォーマンス カウンター、アプリから System.Diagnostics.Trace のトレース呼び出しなど、すべてのデータが送信されます。

ロール プロパティ エディターや発行ウィザードで診断オプションを変更する際、実際には次の 2 つのファイル セットの内容を変更しています。

* ロール[診断構成ファイル](https://msdn.microsoft.com/library/azure/dn782207.aspx)。 ソリューション エクスプローラーの `<Your Service>/Roles/*/diagnostics.wadcfgx` にあります。
* サービス構成ファイル `ServiceConfiguration.*.cscfg`。

ウィザードよりも詳細なオプションを有効にするには、これらのファイルを直接編集します。 詳細については、続く内容をご覧ください。 

## <a name="separate-development-and-production-resources"></a>開発および運用リソースを分離する
アプリの開発および運用スタンプから、さまざまな Application Insights リソースにテレメトリ データを送信できます。 これにより、開発テレメトリがライブ テレメトリに埋もれることを回避できます。 

1. スタンプごとに[新しい Application Insights リソースを作成します](app-insights-create-new-resource.md)。 各リソースの [要点] タブから、インストルメンテーション キーを取得します。
2. 2 つの .cscfg ファイルを編集し、異なるインストルメンテーション キーを挿入します。

## <a name="choose-the-priority-levels-to-send"></a>送信の優先度レベルを選択する
各ロールの診断構成ファイル `diagnostics.wadcfgx` では、レベルによってログ メッセージをフィルター処理できます。

### <a name="define-a-sink"></a>シンクを定義する
`<SinksConfig>` では、Azure 診断データを送信できる追加シンクを定義します。  `SinksConfig` の例を次に示します。

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights/>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

`Channels` では、シンクに送信されるデータ ストリームの名前を指定します。 チャネルはフィルターのように動作します。 `loglevel` 属性では、チャネルが送信するログ レベルを指定できます。 使用できる値は `{Verbose, Information, Warning, Error, Critical}`です。

### <a name="send-data-to-the-sink"></a>データをシンクに送信する
DiagnosticMonitorConfiguration ノードまたはその下の任意のノードにシンク属性を追加することで、Application Insights シンクにデータを送信します。 シンク要素を各ノードに追加すると、そのノードとその下にあるすべてのノードから収集したデータが指定したシンクに送信されるように指定されます。

たとえば、Azure SDK によって作成される既定値では、すべての Azure 診断データが送信されます。

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

ただし、エラー ログのみを送信する場合は、チャネル名でシンク名を修飾します。

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

定義したシンクの名前と共に上で定義したチャネル名を使用していることに注意してください。

Application Insights に "詳細" アプリケーション ログのみを送信する場合は、シンク属性を `Logs` ノードに追加します。

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

構成の階層内のさまざまなレベルに複数のシンクを含めることもできます。 その場合、階層の最上位に指定されたシンクはグローバルな設定として機能し、個々の要素のレベルに指定されたシンクはそのグローバル設定よりも優先されます。

Application Insights にすべてのエラー (`DiagnosticMonitorConfiguration` ノードで指定) とさらにアプリケーション ログの "詳細" レベルのログ (`Logs` ノードで指定) を送信するパブリック構成ファイルの例の全体を次に示します。

```xml

    <WadCfg>
     <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData">
       <!-- All info below sent to this channel -->
      <DiagnosticInfrastructureLogs />
      <PerformanceCounters>
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
        <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
      </PerformanceCounters>
      <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Application!*" />
      </WindowsEventLog>
      <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/>
       <!-- This specific info sent to this channel -->
     </DiagnosticMonitorConfiguration>

     <SinksConfig>
      <Sink name="ApplicationInsights">
        <ApplicationInsights/>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```


この機能には制限事項がいくつかあります。

* チャネルは、ログの種類を操作することのみを目的としています。パフォーマンス カウンターは操作できません。 パフォーマンス カウンターの要素を含むチャネルを指定した場合、そのチャネルは無視されます。
* チャネルのログ レベルには、Azure 診断によって収集されるデータのログ レベルを超えるログを指定することはできません。 たとえば、Logs 要素でアプリケーション ログのエラーを収集できないため、Application Insight シンクに "詳細" ログを送信することにします。 scheduledTransferLogLevelFilter 属性では、シンクに送信するログと同じかそれを超えるレベルのログを常に収集する必要があります。
* Azure 診断の拡張機能によって収集される BLOB データは Application Insights に送信できません。 たとえば、Directories ノードの下で指定されたデータです。 クラッシュ ダンプの場合、実際のクラッシュ ダンプは Blob Storage に送信され、Application Insights にはクラッシュ ダンプが生成されたという通知のみが送信されます。

## <a name="next-steps"></a>次のステップ
* [Application Insights で Azure Cloud Services を監視する](app-insights-cloudservices.md)
* [PowerShell を使用した Application Insights への Azure 診断の送信](app-insights-powershell-azure-diagnostics.md)
* [Azure 診断構成ファイル](https://msdn.microsoft.com/library/azure/dn782207.aspx)




<!--HONumber=Nov16_HO3-->


