<properties 
	pageTitle="Application Insights データ モデル" 
	description="JSON の連続エクスポートからエクスポートされ、フィルターとして使用されるプロパティについて説明します。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="awills"/>

# Application Insights エクスポート データ モデル

次のテーブルは、[Application Insights SDK](app-insights-overview.md) からポータルに送信されるテレメトリのプロパティを一覧表示したものです。[連続エクスポート](app-insights-export-telemetry.md)からのデータ出力で、テレメトリのプロパティが表示されます。テレメトリは[メトリック エクスプローラー](app-insights-metrics-explorer.md)と[診断検索](app-insights-diagnostic-search.md)のプロパティ フィルターでも表示されます。

テレメトリの使用例は[こちら](app-insights-export-telemetry.md#code-samples)です。

最初のセクションの "&lt;telemetryType&gt;" は、"表示" や "要求" などのテレメトリの種類の名前のプレースホルダーです。


## &lt;telemetryType&gt;

**<measurement>**

    KVPs <string, double> <telemetryType>.measurement      Max: 100
* 
    キーと値のペア (KVP) のプロパティ バッグです。AppInsights テレメトリ項目にカスタム メトリックを追加する機能拡張を提供します。 

    *派生:* 測定名の最大サイズは 100

    *既定:* 既存のキーの場合、値がないと、count = 1、value = 0、min/max = 0 になります

**<property>**

    KVPs <string, string> <telemetryType>.properties      Max: 100
* 
    キーと値のペア (KVP) のプロパティ バッグです。AppInsights テレメトリ項目にカスタム プロパティを追加する機能拡張を提供します。開発者は、テレメトリ項目に関連付けられている KVP リストを指定できます。各キーが追跡され、AppInsights ikey (アプリ) あたり最大 200 の一意のキーを指定できます。キーには、最大 100 文字を指定できます。すべての値が文字列として扱われ、最大 1000 文字のサイズを指定できます。各プロパティは最初にディメンションとして分類され、各プロパティの値セットに基づいて、セグメント化機能を有効にします。値のセットはそれぞれ、基数のプロパティ キーごとに追跡されます。キーの基数が一意の値の 100 を超えると、プロパティは属性として分類されます。属性は検索できますが、セグメント化 (集計またはグループ化) の対象にはできません。 

    *派生:* プロパティ名の最大サイズは 100、プロパティ値の最大サイズは 1024

    *既定:* 既存のキー場合、値がないと、値は Null になります

**count**

    long <telemetryType>.count      
* 
    テレメトリ項目の数。   

    *派生:* Null の場合、count = 1

**duration**

    simpleMetric <telemetryType>.duration   ticks   
* 
    テレメトリ項目の期間。要求の場合は、要求の実行時間になります。 

    *既定:* R1: ビューの場合、このフィールドは省略可能です

**message**

    string <telemetrytype>.message      Max: 10240
* 
    テレメトリ タイプのテキスト メッセージ。この文字列は、フルテキスト検索に使用できます。 

**name**

    string <telemetrytype>.name      Max: 250
* 
    テレメトリ項目の名前。この名前はインスタンス間で一意ではありません。テレメトリ タイプのグループ化を表します。ビューの場合、既定で URLData.base に設定されます。イベントの場合は、開発者が指定したラベルです。要求の場合は、controller\\action などの判読可能な要求形式になります。 

    *例*<br/> View names:<br/>70-486 Exam Question 1<br/>About - My ASP.NET Application<br/><br/>Example request names:<br/>POST /Components/WebHandlers/ItemCompare.ashx<br/>GET /signalr/poll<br/>GET /signalr/negotiate

**severity**

    string <telemetryType>.severity      Max: 100
* 
    テレメトリ項目の重大度。これは、トレース テレメトリ項目と例外テレメトリ項目で有効です。 

**url**

    string <telemetrytype>.url      Max: 2048
* 
    印刷ページ、イベント、要求、RDD の URL。完全な URL です。フルテキスト検索とエクスポートでサポートされます。このフィールドには高基数を指定できます。これは属性です。メトリック エクスプローラーで集計に使用できる urlData データ項目のセットに解析されます。 

    *既定:* R2: remotedepencyType で dependencyType = HTTP の場合、このフィールドは必須。<br/>clientperformanceType でこのフィールドは必須。

    *例*<br/> https://icecream.contoso.com/main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>http://fabrikam-oats.azurewebsites.net/index.htm

**urlData.base**

    string <telemetrytype>.urldata.base      Max: 2048
* 
    ホストのクエリ parms を除く URL データ項目の一部です。ルート URI です。この値は、セグメント化/集計に使用できます。 

    *派生:* URL の変換については付録をご覧ください

    *例*<br/> /main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>/default.aspx<br/>/Patients/Search/<br/>

**urldata.hashTag**

    string <telemetrytype>.urldata.hashtag      Max: 100
* 
    URL データ項目のハッシュタグのテキスト 

    *派生:* URL の変換については付録をご覧ください

**urlData.host**

    string <telemetrytype>.urldata.host      Max: 200
* 
    URL データ項目のホスト。URL データ項目がローカル URI の場合、空で表されます。 

    *派生:* URL の変換については付録をご覧ください

    *例*<br/> www.fabrikam.com<br/>www.contoso.com<br/>bretwpc711.azurewebsites.net<br/>



## availability

**availability**

    simpleMetric availability.availability      
* 
    可用性テストの成功を示すインジケーター 

**dataSize**

    simpleMetric availability.datasize      
* 
    &lt;telemetry&gt;.message テキスト メッセージのサイズ 

**result**

    enum (pass/fail) availability.result      
* 
    可用性 Web テストの詳細情報を取得するポインター (HTTP 呼び出し) 

**runLocation**

    string availability.runlocation      Max: 100
* 
    可用性テストの実行場所 

**testName**

    string availability.testname      Max: 1024
* 
    可用性テストの名前 

**testRunId**

    string availability.testrunid      Max: 100
* 
    可用性テスト実行インスタンスの一意の ID 

**testTimeStamp**

    datetime availability.testtimestamp      
* 
    可用性テスト実行インスタンスの先頭のタイムスタンプ 


## basicexception

**assembly**

    string basicexception.assembly      Max: 100
**exceptionGroup**

    string basicException.exceptionGroup      
**exceptionType**

    string basicexception.exceptiontype      Max: 100
**failedUserCodeAssembly**

    string basicException.failedUserCodeAssembly      
**failedUserCodeMethod**

    string basicException.failedUserCodeMethod      
**handledAt**

    string basicexception.handledat      Max: 100
**innerMostExceptionMessage**

    string basicException.innermostExceptionMessage      
**innerMostExceptionThrownAtAssembly**

    string basicException.innermostExceptionThrownAtAssembly      
**innerMostExceptionThrownAtMethod**

    string basicException.innermostExceptionThrownAtMethod      
**innerMostExceptionType**

    string basicException.innermostExceptionType      
**静的メソッド**

    string basicexception.method      Max: 100
**outerMostExceptionMessage**

    string basicException.outerExceptionMessage      
**outerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtAssembly      
**outerMostExceptionTrownAtMethod**

    string basicException.outerExceptionThrownAtMethod      
**outerMostExceptionType**

    string basicException.outerExceptionType      
**problemid**

    string basicexception.problemid      Max: 100
* 
    *派生:* コール スタックの解析については付録をご覧ください 

**Exceptions.Assembly**

    string basicexception.exceptions.assembly      Max: 100
**Exceptions.fileName**

    string basicexception.exceptions.filename      Max: 100
**Exceptions.hasFullStack**

    boolean basicexception.exceptions.hasfullstack      
**Exceptions.Level**

    string basicexception.exceptions.level      Max: 100
**Exceptions.Line**

    long basicexception.exceptions.line      
**exceptions.message**

    string basicexception.exceptions.message      Max: 10240
**Exceptions.Method**

    string basicexception.exceptions.method      Max: 100
**Exceptions.outerId**

    long basicexception.exceptions.outerid      
**Exceptions.parsedStack**

    List<StackFrame> basicexception.exceptions.parsedstack      
**Exceptions.Stack**

    string basicexception.exceptions.stack      Max: 10240
**Exceptions.typeName**

    string basicexception.exceptions.typename      Max: 100
**parsedStackAssembly**

    string basicException.parsedStack.assembly      
**parsedStackFilename**

    string basicException.parsedStack.fileName      
**parsedStackLevel**

    string basicException.parsedStack.level      
**parsedStackLine**

    string basicException.parsedStack.line      
**paseStackMethod**

    string basicException.parsedStack.method      

## clientperformance

**domProcessing**

    simpleMetric clientperformance.domprocessing   ms   
* 
    PerTotal 時間の一部です。この部分は、アプリで応答の処理にかかった時間を表します。Web クライアントの場合は、ドキュメント オブジェクト モデル (DOM) の処理時間です。このタイミングは、最新のブラウザーの perfTiming API を使用してキャプチャされます。 

**networkConnect**

    simpleMetric clientperformance.networkconnect   ms   
* 
    PerTotal 時間の一部です。この部分は、アプリでネットワークの接続にかかった時間を表します。このタイミングは、最新のブラウザーの perfTiming API を使用してキャプチャされます。 

**perfTotal**

    simpleMetric clientperformance.perftotal   ms   
* 
    ビューの読み込みにかかる合計時間です。Web クライアントの場合は、"ページの読み込み時間" に相当します。このタイミングは、最新のブラウザーの perfTiming API を使用してキャプチャされます。 

**receiveResponse**

    simpleMetric clientperformance.receiveresponse   ms   
* 
    PerTotal 時間の一部です。この部分は、アプリで要求応答の受信にかかった時間を表します。このタイミングは、最新のブラウザーの perfTiming API を使用してキャプチャされます。 

**sendRequest**

    simpleMetric clientperformance.sendrequest   ms   
* 
    PerTotal 時間の一部です。この部分は、アプリでサーバーへの要求の送信にかかった時間を表します。このタイミングは、最新のブラウザーの perfTiming API を使用してキャプチャされます。 


## context

**applicationBuild**

    string context.application.build      Max: 100
* 
    アプリのアプリケーション ビルド番号 

**applicationVersion**

    string context.application.version      Max: 100
* 
    クライアント アプリケーションのアプリケーション バージョン[不明] に設定されている場合は利用できません。 

    *例*<br/> 2015.5.21.3<br/>NokiaMailBye\_CD\_20150227.4

**telemetryType**

    string context.billing.telemetrytype      Max: 100
* 
    これは、課金レコードのテレメトリ タイプを表し、アプリの課金対象となるテレメトリ項目のセグメント化として内部で使用されます。 

**dataId**

    string context.data.id      Max: 100
* 
    テレメトリ項目の一意の識別子。データ コレクションのエンドポイントに割り当てられます。 

    *派生:* 生成された UUID4

    *例*<br/> edc6eaf3-3459-46a0-bb81-bedc24913864

**eventTime**

    datetime context.data.eventtime      
* 
    UTC で記録されたテレメトリ イベントの時刻。通常、クライアント側で設定されます。このフィールドがない場合は、データ コレクションのエンドポイントで設定されます。フィールドの形式は、YYYY-MM-DDTHH:MM:SS.sssZ です。    

    *例*<br/> 2015-05-20T04:00:46.8338283Z

**samplingRate**

    string context.data.samplerate      Max: 100
* 
    データ プロデューサー (SDK) のサンプリング レートです。ここでは、1 以外の値は、このテレメトリ項目に関連付けられているメトリックがサンプリングされた値を表すことを示します。したがって、0.05 のサンプリング レートは、20 の数を表す任意の 1 つのテレメトリ項目になります。 

**の作成**

    string context.device.browser      Max: 100
* 
    クライアントのブラウザー 

    *既定:* Null の場合、ユーザー エージェントの処理に基づいて設定されます。ユーザー エージェントの解析については、付録をご覧ください。

    *例*<br/> Opera<br/>Mobile Safari<br/>Ovi Browser<br/>Chrome<br/>Firefox<br/>Internet Explorer

**browserVersion**

    string context.device.browserversion      Max: 100
* 
    クライアントのブラウザー バージョン 

    *既定:* Null の場合、ユーザー エージェントの処理に基づいて設定されます。ユーザー エージェントの解析については、付録をご覧ください。

    *例*<br/> Opera 12.17<br/>Mobile Safari 8.0<br/>Ovi Browser 5.5<br/>Chrome 37.0<br/>Firefox 21.0<br/>Internet Explorer 7.0

**deploymentId**

    string context.device.deploymentid      Max: 100
* 
    サーバーのデプロイ ID 


**deviceName**

    string context.device.name      Max: 100
* 
    アプリが実行されているデバイスの名前 

**locale**

    string context.device.locale      Max: 100
* 
    クライアント側のアプリのロケールです。テレメトリ項目で明示的に指定しない場合、ユーザー エージェントのフィールドの処理により指定されます。 

    *例*<br/> ru<br/>ja-JP<br/>de-DE<br/>unknown

**machineName**

    string context.device.vmname      Max: 100
* 
    サーバーのマシン名。仮想化されたコンピューティングの場合、このデータ項目は、基盤となるホストに相当します。専用のコンピューティングの場合は、コンピューター名になります。 


**operatingSystem**

    string context.device.os      Max: 100
* 
    クライアントのオペレーティング システム 

    *既定:* Null の場合、ユーザー エージェントの処理に基づいて設定されます。ユーザー エージェントの解析については、付録をご覧ください。

    *例*<br/> Windows<br/>iOS iPad<br/>Nokia

**operatingSystemVersion**

    string context.device.osversion      Max: 100
* 
    クライアントのオペレーティング システム バージョン 

    *既定:* Null の場合、ユーザー エージェントの処理に基づいて設定されます。ユーザー エージェントの解析については、付録をご覧ください。

    *例*<br/> Windows XP<br/>iOS 8.3<br/>Nokia Series 40<br/>Windows 7<br/>Windows 8

**roleInstance**

    string context.device.roleinstance      Max: 100
* 
    サーバー コンピューティングのインスタンス。クラウド/仮想化のシナリオでは、これは、コンピューティング インスタンスの仮想名になります。専用のコンピューティング インスタンスの場合は、コンピューター名になります。Azure クラウド サービスの場合、既定で PaaS ロール インスタンス名または IaaS 仮想マシン名に設定する必要があります。  

**roleName**

    string context.device.rolename      Max: 100
* 
    サーバー コンピューティング インスタンスをグループ化する論理上の名前空間。Azure ホステッド サービスの場合、PaaS ロールは既定で PaaS ロール名に設定する必要があります。IaaS ロールは既定で仮想マシン名に設定する必要があります。  

**screenHeight**

    string context.device.screenresolution.height      Max: 100
* 
    テレメトリ項目が記録される時点でのクライアント ハードウェアでのアプリの画面の高さ。明示的に指定されない場合、screenresolution データ項目の変換によって指定されます。 

    *派生:* (存在する場合) context.device.screenresolution より解析

    *例*<br/> 360<br/>1280<br/>1920

**screenResolution**

    string context.device.screenresolution      Max: 100
* 
    テレメトリ項目がアプリによってキャプチャされた時点での画面の解像度。セッション中に縦方向と横方向の間で切り替えられます。この属性をセッション レベルで使用する場合、フル セッションを表すためにキャプチャされる最初の画面の解像度になります。 

    *例*<br/> Screen Resolution Height Width<br/>360X640<br/>1280X800<br/>1920x1080

**screenWidth**

    string context.device.screenresolution.width      Max: 100
* 
    テレメトリ項目が記録される時点でのクライアント ハードウェアでのアプリの画面の幅。明示的に指定されない場合、screenresolution データ項目の変換によって指定されます。 

    *派生:* (存在する場合) context.device.screenresolution より解析

    *例*<br/> 640<br/>800<br/>1080


**aiAgentVersion**

    string context.internal.agentversion      Max: 100
* 
    内部のデータ項目です。テレメトリ項目を生成した SDK エージェントのバージョンを表します。 

**city**

    string context.location.city      Max: 100
* 
    アプリ セッションの市区町村。テレメトリ項目で直接指定できます。存在しない場合は、テレメトリ項目の IPv4 に基づいて設定されます。IPv4 の指定がない場合、フィールドは空白のままです。 

    *例*<br/> Minsk<br/>Haarlem

**clientIpAddress**

    ipv4 context.location.clientip      
* 
    xxx.xxx.xxx.xxx 形式のクライアントの IPv4 アドレス。

     プライバシーの問題に対処するために、最後のオクテットは常に 0 に設定されます。

    *既定:* Null の場合、データ コレクションのエンドポイントでキャプチャされる HTTP IP に設定されます

    *例*<br/> 186.123.63.0<br/>123.203.131.0

**continent**

    string context.location.continent      Max: 100
* 
    アプリ セッションの大陸。テレメトリ項目で直接指定できます。存在しない場合は、テレメトリ項目の IPv4 に基づいて設定されます。IPv4 の指定がない場合、フィールドは空白のままです。 

    *例*<br/> Europe<br/>North America

**country**

    string context.location.country      Max: 100
* 
    アプリ セッションの国。テレメトリ項目で直接指定できます。存在しない場合は、テレメトリ項目の IPv4 に基づいて設定されます。IPv4 の指定がない場合、フィールドは空白のままです。 

    *例*<br/> Belarus<br/>Netherlands<br/>Germany


**state**

    string context.location.province      Max: 100
* 
    アプリのセッションの郡。テレメトリ項目で直接指定できます。存在しない場合は、テレメトリ項目の IPv4 に基づいて設定されます。IPv4 の指定がない場合、フィールドは空白のままです。 

    *例*<br/> Minsk<br/>Oregon<br/>Central Serbia<br/>Provincia di Oristano

**operationId**

    string context.operation.id      Max: 100
* 
    operation.id は、テレメトリ項目間で使用できる相関 ID です。たとえば、関連するすべてのテレメトリ項目の operation.id に要求 ID を設定し、rdd、例外、イベントなどのテレメトリ項目間の相関関係を有効にすることもできます。  

**operationName**

    string context.operation.name      Max: 100
* 
    人間が判読可能な操作名です。「operationId」をご覧ください。これにより、発注書完了などの類似する操作 ID をグループ化できます。   

**operationParentId**

     context.operation.parentid      
* 
    テレメトリの相関関係で ID の入れ子を許可する、operation.id の親 ID。   

**syntheticSource**

    string context.data.syntheticsource      Max: 100
* 
    issynthetic = true の場合、このデータ項目は代理データのソースを表します。 

    *既定:* Null の場合、既知の代理ソース (webcrawlers など) に対してユーザー エージェントが確認され、その結果に基づいてソースが設定される可能性があります。

**syntheticTransaction**

    boolean context.data.issynthetic      
* 
    テレメトリ項目が実際のユーザー アクティビティではなく、代理テストで生成されたこと示すインジケーター。 

    *既定:* Null の場合、代理エージェントの既知の一覧に対してユーザー エージェントが確認されます。一致が見つかると、値は true に設定されます。<br/>ユーザー エージェントが Null の場合は false に設定されます。

**session.Id**

    String context.session.id      Max: 100
* 
    実際のユーザーとアプリケーションとの相互動作を示す一意の識別子。この相互動作は、""セッション"" です。同じキーが対象のアプリケーションによって生成されるすべてのテレメトリには、この一意の識別子を含める必要があります。<br/><br/>セッションは、同じユーザー操作内で連続するいくつかのイベントによって定義されます。テレメトリ イベントなしに 30 分以上経過すると、セッションの終了が通知されます。   

    *既定:* MetricType、BillingType では使用できません

    *例*<br/> CFFC8B21-9828-4F56-AD7C-B6B5AC26B133

**accountAcquisitionDate**

    datetime context.user.accountAcquisitionDate  
    
**anonUserId**

    string context.user.anonId      Max: 100
* 
    アプリ内で匿名ユーザーを定義する一意の識別子。SDK を使用する場合は自動生成され、クライアント上で保持されます。同じログインでデバイスを共有する複数の実際のユーザーは区別されませんが、別のログインを使用し、OS でプロファイルがサポートされている場合、複数の実際のユーザーは区別されます。認証されたことがない一意のユーザーが使用する場合には、最適なプロキシです。 

    *例*<br/> f23854a1b01c4b1fa79fa2d9a5768526

**anonymousUserAcquisitionDate**

    datetime context.user.anonAcquisitionDate      

**authenticatedUserAcquisitionDate**

    datetime context.user.authAcquisitionDate     
 
**authUserId**

    string context.user.authId      Max: 100
* 
    アプリ内で認証されたユーザーを定義する一意の識別子。これは、開発者が指定します。認証されたユーザーの利点は、識別子がアプリ内の複数のデバイス間でローミングしても、その一意性を維持できる点にあります。 

**storeRegion**

    string context.user.storeregion      Max: 100
* 
    アプリが指定されたストア領域。 

**userAcountId**

    string context.user.accountId      Max: 100
* 
    アプリ内でアカウントを定義する一意の識別子。これは、開発者が指定します。 

### カスタム メトリック

    context.custom.metrics.<metric-name>

      double value
      double count
      double min
      double max
      double stdDev
      double sampledValue
      double sum


## remotedependency

**async**

    boolean remotedependency.async      
* 
    リモートの依存関係の呼び出しが非同期だった場合のインジケーター 

**commandName**

    string remotedependency.commandname      Max: 2048
* 
    リモートの依存関係が SQL の場合の、リモートの依存関係の SQL コマンド名 

**dependencyTypeName**

    string remotedependency.dependencytypename      Max: 2048
* 
    リモートの依存関係のリモート依存関係タイプ名 

**remoteDependencyName**

    string remotedependency.remotedependencyname      Max: 2048
* 
    リモートの依存関係の名前 

    *派生:* &lt;telemetryType.name&gt; に標準化します。

**type**

    string remotedependency.remotedependencytype      Max: 100
* 
    リモートの依存関係のタイプ。サポートされているタイプは、SQL、AZURE リソース (ストレージやキューなど)、HTTP です。 

**成功**

    boolean remotedependency.success      
* 
    リモートの依存関係の呼び出しが成功したか失敗したかを示すインジケーター。 


## request

**hasdetaileddata**

    boolean request.hasdetaileddata      
* 
    operation.id に基づいて関連するテレメトリ項目がある場合に識別するインジケーター。 

**httpMethod**

    string request.httpmethod      Max: 100
* 
    要求で使用される HTTP メソッド。   

**id**

    string request.id      Max: 100
* 
    要求の一意の識別子。SDK によって生成されます。この ID を操作 ID プロパティに設定して、同じ要求に起因するテレメトリ項目に関連付けることもできます。 

**responseCode**

    long request.responsecode      
* 
    要求の応答コード 

**成功**

    boolean request.success      
* 
    要求が成功した場合のインジケーター。200 の応答コードは成功と見なされます。 

    *既定:* Null の場合、true に設定されます


## sessionmetric

**anonymousUserDurationSinceLastVisit**

    Long sessionmetric.anonymoususerdurationsincelastvisit      
* 
    この匿名のユーザー識別子が最後にアクセスしてから経過した時間。最初のアクセス時は、この値は空です。その後アクセスするたびに、アクセス間隔の時間が日単位で表されます。値 3 は、前回のセッション インスタンスから今回のセッション インスタンスまでに 3 日間経過したことを示します 

**anonymousUserSessionCount**

    Long sessionmetric.anonymoususersessioncount      
* 
    匿名ユーザーのアクセス数。この匿名ユーザーの一意の識別子に対する履歴セッション合計数の増分カウンターです。この識別子によるセッションごとにカウンターが増分されます。このカウンターは、30 日以内にユーザー識別子が確認できない場合はクリアされ、その時点でカウンターはリセットされるので、同じユーザー識別子が次回アクセスした際には新規ユーザーとして見なされます。 

**authenticatedAccountDurationSinceLastVisit**

    Long sessionmetric.authenticatedaccountdurationsincelastvisit      
* 
    このアカウント識別子が最後にアクセスしてから経過した時間。最初のアクセス時は、この値は空です。その後アクセスするたびに、アクセス間隔の時間が日単位で表されます。値 3 は、前回のセッション インスタンスから今回のセッション インスタンスまでに 3 日間経過したことを示します 

**authenticatedAccountSessionCount**

    Long sessionmetric.authenticatedaccountsessioncount      
* 
    認証された アカウント識別子のアクセス数。この一意のアカウント識別子に対する履歴セッション合計数の増分カウンターです。この識別子によるセッションごとにカウンターが増分されます。このカウンターは、30 日以内にユーザー識別子が確認できない場合はクリアされ、その時点でカウンターはリセットされるので、同じユーザー識別子が次回アクセスした際には新規ユーザーとして見なされます。 

**authenticatedUserDurationSinceLastVisit**

    Long sessionmetric.authenticateduserdurationsincelastvisit      
* 
    この認証されたユーザー識別子が最後にアクセスしてから経過した時間。最初のアクセス時は、この値は空です。その後アクセスするたびに、アクセス間隔の時間が日単位で表されます。値 3 は、前回のセッション インスタンスから今回のセッション インスタンスまでに 3 日間経過したことを示します 

**authenticatedUserSessionCount**

    Long sessionmetric.authenticatedusersessioncount      
* 
    認証された ユーザー識別子のアクセスの数。この認証されたユーザーの一意の識別子に対する履歴セッション合計数の増分カウンターです。この識別子によるセッションごとにカウンターが増分されます。このカウンターは、30 日以内にユーザー識別子が確認できない場合はクリアされ、その時点でカウンターはリセットされるので、同じユーザー識別子が次回アクセスした際には新規ユーザーとして見なされます。 

**crashCount**

    Long sessionmetric.crashcount      
* 
    このセッション インスタンス中に発生したクラッシュの数。 

**duration**

    timespan sessionmetric.duration      
* 
    セッション インスタンスの実行時間 

**entryEvent**

    string sessionmetric.entryevent      Max: 200
* 
    セッションの最初のイベント。これは event.name から指定され、sessionMetric メトリックのセグメント化/集計として使用できます。 

    *派生:* event.name から指定されます

**entryUrl**

    string sessionmetric.entryurl      Max: 2048
* 
    セッションの最初の URL。これは urlData.base から指定され、sessionMetric メトリックのセグメント化/集計として使用できます。 

    *派生:* &lt;telemetryType&gt;.Url から指定されます

**eventCount**

    Long sessionmetric.eventcount      
* 
    このセッション インスタンス中に発生したイベントの数。 

**exceptionCount**

    Long sessionmetric.exceptioncount      
* 
    このセッション インスタンス中に発生した例外の数。 

**exitEvent**

    string sessionmetric.exitevent      Max: 200
* 
    セッションの最後のイベント。これは event.name から指定され、sessionMetric メトリックのセグメント化/集計として使用できます。 

    *派生:* event.name から指定されます

**exitUrl**

    string sessionmetric.exiturl      Max: 2048
* 
    セッションの最後の URL。これは urlData.base から指定され、sessionMetric メトリックのセグメント化/集計として使用できます。 

    *派生:* &lt;telemetryType&gt;.Url から指定されます

**pageBounceCount**

    boolean sessionmetric.pagebouncecount      
* 
    この sessionMetric テレメトリ項目が表すバウンス セッションの数。バウンス セッションとは、1 つのビューのテレメトリ項目に基づいて作成されるセッションです。 

    *派生:* sessionMetric.viewCount + sessionMetric.requestCount = 1 の場合は 1、それ以外は 0 になります。

**pageCount**

    Long sessionmetric.pagecount      
* 
    このセッション インスタンス中に発生したビューの数。 

**requestCount**

    Long sessionmetric.requestcount      
* 
    このセッション インスタンス中に発生した要求の数。 

**sessionCount**

    Long sessionmetric.sessioncount      
* 
    テレメトリのこの sessionMetric インスタンスが表すセッションの数。 


## trace

**context**

    string trace.context      Max: 100
* 
    トレース/例外時の capp のコンテキスト 

**exception**

    string trace.exception      Max: 10240
* 
    トレース テレメトリ項目に関連付けられた例外 

**excerpt**

    string trace.excerpt      Max: 100
* 
    トレース テレメトリ項目の短いテキスト メッセージ 

**formatMessage**

    string trace.formatmessage      Max: 100
* 
    トレース テレメトリ項目のメッセージの書式設定 

**formatProvider**

    string trace.formatprovider      Max: 100
* 
    トレース テレメトリ項目の書式プロバイダー 

**hasStackTrace**

    boolean trace.hasstacktrace      
* 
    トレース テレメトリ項目にスタック トレースが含まれていることを示すインジケーター 

**level**

    string trace.level      Max: 100
* 
    トレース メッセージのレベル 

**loggerName**

    string trace.loggername      Max: 100
* 
    トレースのロガー名 

**loggerShortName**

    string trace.loggershortname      Max: 100
* 
    ロガーの短い名前 

**message**

    string trace.message      Max: 10240
* 
    トレース テレメトリ項目のフルテキスト メッセージ 

**messageId**

    string trace.messageId      Max: 100
* 
    トレース テレメトリ項目の一意の識別子 

**parameters**

    string trace.parameters      Max: 100
* 
    トレース テレメトリ項目のトレース レコーダーに提供されるパラメーター 

**processId**

    string trace.processId      Max: 100
* 
    テレメトリ項目記録時のアプリのプロセス ID 

**sourceType**

    string trace.sourceType      Max: 100
* 
    トレース テレメトリ項目のソースの種類 

**sqquenceId**

    string trace.sequenceid      Max: 100
* 
    トレース テレメトリ項目のシーケンスを記録する際にトレース プロバイダーが使用するシーケンス識別子 

**stacktrace**

    string trace.stacktrace      Max: 100
* 
    トレース テレメトリ項目用にキャプチャされるスタック トレース 

**threadId**

    string trace.threadId      Max: 100
* 
    テレメトリ項目の記録時のアプリのスレッド ID 

**userStackframe**

    string trace.userstackframe      Max: 100
* 
    トレース テレメトリ項目のユーザー スタック フレーム 

**userStackframNum**

    string trace.userstackframenum      Max: 100
* 
    トレース テレメトリ項目のユーザー スタック フレーム番号 


## view

**referrerDataUrl**

    string view.referralurl      Max: 2048
* 
    印刷ページの参照元の URL です。完全な URL です。フルテキスト検索とエクスポートでサポートされます。このフィールドには高基数を指定できます。これは属性です。メトリック エクスプローラーで集計に使用できる referralData データ項目のセットに解析されます。 

**referrerData.base**

    string view.referrerdata.base      Max: 2048
* 
    ホストのクエリ parms を除く参照元の URL の一部です。ルート URI です。この値は、セグメント化/集計に使用できます。 

    *派生:* URL の変換については付録をご覧ください。

**referrerData.hashTag**

    string view.referrerdata.hashtag      Max: 100
* 
    参照元の URL のハッシュタグのテキスト 

    *派生:* URL の変換については付録をご覧ください。

**referrerData.host**

    string view.referrerdata.host      Max: 200
* 
    参照元の URL のホスト。URL がローカル URI の場合、空で表されます。 

    *派生:* URL の変換については付録をご覧ください。

**referrerData.port**

    string view.referrerdata.port      Max: 100
* 
    完全な URL で表される場合、参照元の URL のポートです。それ以外の場合は、空になります。 

    *派生:* URL の変換については付録をご覧ください

**referrerData.protocol**

    string view.referrerdata.protocol      Max: 100
* 
    参照元の URL のプロトコル (HTTP、FTP など) 

    *派生:* URL の変換については付録をご覧ください。

    *例*<br/> http<br/>https

**referrerData.queryParameters.parameter**

    string view.referrerdata.queryparameters.parameter      Max: 100
* 
    参照元の URL のクエリ パラメーター名の配列 

    *派生:* URL の変換については付録をご覧ください。

**referrerData.queryParameters.value**

    string view.referrerdata.queryparameters.value      Max: 100
* 
    referringData URL から解析されたクエリ パラメーターの値の配列 

    *派生:* URL の変換については付録をご覧ください。



## 関連項目

* [Application Insights](app-insights-overview.md) 
* [連続エクスポート](app-insights-export-telemetry.md)
* [コード サンプル](app-insights-export-telemetry.md#code-samples)

<!---HONumber=AcomDC_0302_2016-->