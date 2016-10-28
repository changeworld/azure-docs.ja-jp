<properties
   pageTitle="Application Insights にデータを送信するための Azure 診断の構成 | Microsoft Azure"
   description="Application Insights にデータを送信するように Azure 診断のパブリック構成を更新します。"
   services="multiple"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="" />
<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />

# Application Insights にデータを送信するための Azure 診断の構成

Azure 診断のデータは、Azure Storage のテーブルに格納されます。ただし、Azure 診断の拡張機能 1.5 以降を使用する場合、その構成に "シンク" と "チャネル" を構成することで、すべてのデータまたはデータのサブセットを Application Insights にパイプすることもできます。

この記事では、Application Insights にデータを送信するように構成された Azure 診断の拡張機能のパブリック構成を作成する方法を説明します。

## シンクとしての Application Insights の構成

Azure 診断の拡張機能 1.5 では、パブリック構成に **<SinksConfig>** 要素が導入されました。この要素に、Azure 診断データの送信先となる *sink* を追加して定義します。Azure 診断データの送信先の Application Insights リソースの詳細は、この **<SinksConfig>** の一部として指定できます。**SinksConfig** の例は次のようになります。

	<SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>

**Sink** 要素の *name* 属性で、シンクを一意に参照するために使用する文字列値を指定します。**ApplicationInsights** 要素では、Azure 診断データの送信先となる Application Insights リソースのインストルメンテーション キーを指定します。既存の Application Insights リソースがない場合、リソースの作成方法とインストルメンテーション キーの取得方法の詳細については、「[新しい Application Insights リソースを作成します。](./application-insights/app-insights-create-new-resource.md)」を参照してください。

Azure SDK 2.8 を使用してクラウド サービス プロジェクトを開発している場合、このインストルメンテーション キーは、クラウド サービス プロジェクトをパッケージ化するときに、**APPINSIGHTS\_INSTRUMENTATIONKEY** サービス構成設定に基づいてパブリック構成に自動的に設定されます。詳細については、「[Application Insights と Azure 診断を使用したクラウド サービスの問題のトラブルシューティング](./cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md)」を参照してください。

**Channels** 要素では、シンクに送信するデータ用に 1 つまたは複数の **Channel** 要素を定義できます。チャネルはフィルターのように機能し、シンクに送信する特定のログ レベルを選択するために使用できます。たとえば、詳細ログを収集してストレージに送信できますが、ログ レベルが Error のチャネルを定義して、そのチャネル経由でログを送信した場合はエラー ログのみがそのシンクに送信されるようにできます。**Channel** 要素の *name* 属性は、そのチャネルを一意に参照するために使用します。*loglevel* 属性では、チャネルで許可されるログ レベルを指定できます。使用可能なログ レベルを情報の少ない順に並べると、次のようになります。
 - 詳細
 - 情報
 - 警告
 - エラー
 - 重大

## Application Insights のシンクへのデータの送信
Application Insights のシンクを定義したら、*sink* 属性を **DiagnosticMonitorConfiguration** ノードの下の要素に追加することで、そのシンクにデータを送信できます。*sink* 要素を各ノードに追加すると、そのノードとその下にあるすべてのノードから収集したデータが指定したシンクに送信されるように指定されます。

たとえば、Azure 診断によって収集されるすべてのデータを送信する場合、*sink* 属性を **DiagnosticMonitorConfiguration** ノードに直接追加します。*sinks* の値に **SinkConfig** で指定したシンクの名前を設定します。

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

Application Insights のシンクにエラー ログのみを送信する場合は、*sinks* に、シンク名とチャネル名をピリオド (".") で区切った値を設定します。たとえば、Application Insights のシンクにエラー ログのみを送信するには、上の SinksConfig で定義した MyTopDiagdata チャネルを使用します。

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

Application Insights に "詳細" アプリケーション ログのみを送信する場合は、*sinks* 属性を **Logs** ノードに追加します。

	<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

構成の階層内のさまざまなレベルに複数のシンクを含めることもできます。その場合、階層の最上位に指定されたシンクはグローバルな設定として機能し、個々の要素のレベルに指定されたシンクはそのグローバル設定よりも優先されます。

Application Insights にすべてのエラー (**DiagnosticMonitorConfiguration** ノードで指定) とさらにアプリケーション ログの "詳細" レベルのログ (**Logs** ノードで指定) を送信するパブリック構成ファイルの例の全体を次に示します。

    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
           sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
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
                sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
      </DiagnosticMonitorConfiguration>

    <SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>

![Diagnostics Public Configuration](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

この機能には制限事項がいくつかあります。

- チャネルは、ログの種類を操作することのみを目的としています。パフォーマンス カウンターは操作できません。パフォーマンス カウンターの要素を含むチャネルを指定した場合、そのチャネルは無視されます。
- チャネルのログ レベルには、Azure 診断によって収集されるデータのログ レベルを超えるログを指定することはできません。たとえば、Logs 要素でアプリケーション ログのエラーを収集できないため、Application Insight シンクに "詳細" ログを送信することにします。*scheduledTransferLogLevelFilter* 属性では、シンクに送信するログと同じかそれを超えるレベルのログを常に収集する必要があります。
- Azure 診断の拡張機能によって収集される BLOB データは Application Insights に送信できません。たとえば、*Directories* ノードの下で指定されたデータです。クラッシュ ダンプの場合、実際のクラッシュ ダンプは Blob Storage に送信され、Application Insights にはクラッシュ ダンプが生成されたという通知のみが送信されます。


## 次のステップ

- [PowerShell](./cloud-services/cloud-services-diagnostics-powershell.md) を使用して、アプリケーションの Azure 診断の拡張機能を有効にします。 
- [Visual Studio](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) を使用して、アプリケーションの Azure 診断の拡張機能を有効にします。

<!---HONumber=AcomDC_0413_2016-->