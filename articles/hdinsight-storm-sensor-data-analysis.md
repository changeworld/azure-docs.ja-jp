<properties 
	pageTitle="Apache Storm と Microsoft Azure HDInsight (Hadoop) を使用したセンサー データの分析" 
	description="HDInsight (Hadoop) で  リアルタイムにセンサー データを処理するために Apache Storm を使用する方法を説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# HDInsight (Hadoop) での Storm と HBase を使用したセンサー データの分析

Azure Event Hub で発生したセンサー データを処理するために HDInsight での Storm クラスターを使用するソリューションを作成する方法を説明します。処理中、Storm のトポロジは、HBase クラスターに受信データを格納します。トポロジは、Azure Websites でホストされている Web ベースのダッシュボードを介してほぼリアルタイムの情報を提供するために、SignalR も使用します。

> [AZURE.NOTE] このプロジェクトの完全なバージョンは、[https://github.com/Blackmist/hdinsight-eventhub-example](https://github.com/Blackmist/hdinsight-eventhub-example)　で利用できます。

## 前提条件

* Azure サブスクリプション

* Visual Studio と [Microsoft Azure SDK for .NET](http://azure.microsoft.com/downloads/archive-net-downloads/)

* [Java と JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE] Java、JDK、Maven、および Git は、[Chocolatey NuGet](http://chocolatey.org/) パッケージ マネージャーからも入手できます。

## ダッシュボードの作成

ダッシュボードは、ほぼリアルタイムのセンサー情報を表示するために使用されます。ここでは、ダッシュボードは Azure Web サイトにホストされている ASP.NET アプリケーションを意味します。アプリケーションの主目的は、メッセージが処理されるときに Storm トポロジから情報を受信する [SignalR](http://www.asp.net/signalr/overview/getting-started/introduction-to-signalr) のハブとして機能することです。

Web サイトには静的な index.html ファイルも含まれます。このファイルは SignalR にも接続し、D3.js を使用して、Storm トポロジから送信されたデータをグラフ化します。

> [AZURE.NOTE] SignalR の代わりに未加工の WebSockets を使用することもできますが、Web サイトをスケール アウトする必要があったとしても、WebSockets には組み込みのスケーリング メカニズムがありません。SignalR では、Azure Service Bus ([http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus](http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus)) を使用してスケールを変更することができます。
>
> Storm トポロジで未加工の WebSockets を使用して Python Web サイトと通信する例については、「[Storm Tweet Sentiment D3 Visualization (Storm Tweet の評判の D3 視覚化)](https://github.com/P7h/StormTweetsSentimentD3Viz)」プロジェクトを参照してください。

1. Visual Studio で、**ASP.NET Web アプリケーション** プロジェクト テンプレートを使用して新しい C# アプリケーションを作成します。新しいアプリケーションに "**ダッシュボード**" という名前を付けます。

2. **[新しい ASP.NET プロジェクト]** ウィンドウで、**空**のアプリケーション テンプレートを選択します。**[Windows Azure]** セクションで、**[クラウドでのホスト]** と **[Web サイト]** を選択します。最後に、**[OK]** をクリックします。

	> [AZURE.NOTE] サインインを求められた場合は、Azure サブスクリプションにサインインしてください。

3. **[Windows Azure サイトの構成]** ダイアログで、Web サイトの**サイト名**と**リージョン**を入力し、**[OK]** をクリックします。これにより、ダッシュボードをホストする Azure Web サイトが作成されます。

4. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[追加]、[SignalR Hub クラス (v2)]** の順に選択します。クラスに「**DashHub.cs**」という名前を付けて、プロジェクトに追加します。これには、HDInsight とダッシュボード Web ページ間のデータ通信に使用される SignalR ハブが含まれます。

	> [AZURE.NOTE] Visual Studio 2012 を使用している場合、**SignalR Hub クラス (v2)** テンプレートは利用できません。DashHub と呼ばれるプレーンな**クラス**を代わりに追加できます。また、**[ツール]、[ライブラリ パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に開き、次のコマンドを実行して、SignalR パッケージを手動でインストールする必要があります。
	>
	> `install-package Microsoft.AspNet.SignalR`

5. **DashHub.cs** のコードを次のコードに置き換えます。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		using Microsoft.AspNet.SignalR;

		namespace dashboard
		{
		    public class DashHub : Hub
		    {
		        public void Send(string message)
		        {
		            // Call the broadcastMessage method to update clients.
		            Clients.All.broadcastMessage(message);
		        }
		    }
		}

6. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[追加]、[OWIN Startup クラス]** の順に選択します。新しいクラスに "**Startup.cs**" という名前を付けます。

	> [AZURE.NOTE] Visual Studio 2012 を使用している場合、**OWIN Startup クラス (v2)** テンプレートは利用できません。Startup という**クラス**を代わりに作成できます。

7. **Startup.cs** の内容を次の内容に置き換えます。

		using System;
		using System.Threading.Tasks;
		using Microsoft.Owin;
		using Owin;

		[assembly: OwinStartup(typeof(dashboard.Startup))]

		namespace dashboard
		{
		    public class Startup
		    {
		        public void Configuration(IAppBuilder app)
		        {
		            // For more information on how to configure your application, visit http://go.microsoft.com/fwlink/?LinkID=316888
		            app.MapSignalR();
		        }
		    }
		}

8. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[追加]、[HTML ページ]** の順に選択します。新しいページに "**index.html**" という名前を付けます。このページには、このプロジェクトのリアルタイム ダッシュボードが表示されます。DashHub から情報を受信して、D3.js を使用したグラフを表示します。

9. **ソリューション エクスプローラー**で、**[index.html]** を右クリックし、**[スタート ページに設定]** を選択します。

10. **index.html** ファイル内のコードを次のコードに置き換えます。

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Dashboard</title>
		    <style>

		        .x.axis line {
		            shape-rendering: auto;
		        }

		        .line {
		            fill: none;
		            stroke-width: 1.5px;
		        }

		    </style>
		    <!--Script references. -->
		    <!--Reference the jQuery library. -->
		    <script src="Scripts/jquery-1.10.2.min.js"></script>
		    <!--Reference the SignalR library. -->
		    <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
		    <!--Reference the autogenerated SignalR hub script. -->
		    <script src="signalr/hubs"></script>
		    <!--Reference d3.js.-->
		    <script src="http://d3js.org/d3.v3.min.js"></script>
		</head>
		<body>
		    <script>
		        $(function () {
		            //Huge thanks to Mike Bostok for his Path Transitions article - http://bost.ocks.org/mike/path/
		            var n = 243,                                 //number of x coordinates in the graph
		            duration = 750,                          //duration for transitions
		            deviceValue=[0,0,0,0,0,0,0,0,0,0],       //temp holding for each device value
		            now = new Date(Date.now() - duration),   //Now
		            //fill an array of arrays with dummy data to start the chart
		            //each item in the top-level array is a line
		            //each item in the line arrays represents the X coordinate across a graph
		            //The 'value' within each line array represents the Y coordinate for that point
		            data = [
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; })
		            ];

		            //Color scale for 10 items
		            var color = d3.scale.category10();
		            //The domain for color (the device IDs)
		            var devices = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];
		            //This will auto-generate colors for this range of IDs
		            color.domain(devices);

		            //set margins and figure out width/height
		            var margin = {top: 6, right: 0, bottom: 20, left: 40},
		                width = 960 - margin.right,
		                height = 240 - margin.top - margin.bottom;

		            //the time scale for the X axis
		            var x = d3.time.scale()
		                .domain([now - (n - 2) * duration, now - duration])
		                .range([0, width]);

		            //the numerical scale for the Y axis
		            var y = d3.scale.linear()
		                .domain([100, 0])
		                .range([0, height]);

		            //The line, which is really just a
		            //couple functions that we can pass data to
		            //in order to get back x/y coords.
		            var line = d3.svg.line()
		                .interpolate("basis")
		                .x(function (d, i) { return x(now - (n - 1 - i) * duration); })
		                .y(function (d, i) { return y(d.value); });

		            //Find the HTML body element and add a child SVG element
		            var svg = d3.select("body").append("svg")
		                .attr("width", width + margin.left + margin.right)
		                .attr("height", height + margin.top + margin.bottom)
		              .append("g")
		                .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

		            //Define a clipping path, because we need to clip
		            //the graph to render only the bits we want to see
		            //as it moves
		            svg.append("defs").append("clipPath")
		                .attr("id", "clip")
		              .append("rect")
		                .attr("width", width)
		                .attr("height", height);

		            //Append the x axis
		            var axis = svg.append("g")
		                .attr("class", "x axis")
		                .attr("transform", "translate(0," + height + ")")
		                .call(x.axis = d3.svg.axis().scale(x).orient("bottom"));

		            //append the y axis
		            var yaxis = svg.append("g")
		                .attr("class", "y axis")
		                .call(y.axis = d3.svg.axis().scale(y).orient("left").ticks(5));

		            //append the clipping path
		            var linegroup = svg.append("g")
		              .attr("clip-path", "url(#clip)");

		            //magic. Select all paths with a class of .line
		            //if they don't exist, make them.
		            //use the points in the line object to define
		            //the paths
		            //set the color to the cooresponding auto-generated coclor
		            var path = linegroup.selectAll(".line")
		              .data(data)
		              .enter().append("path")
		              .attr("class", "line")
		              .attr("d", line)
		              .style("stroke", function (d, i) { return color(i); });

		            //We need to transition the graph after all
		            //lines have been updated. There's no
		            //built-in for this, so this function
		            //does reference counting on end events
		            //for each line, then applies whatever
		            //callback when all are finished.
		            function endall(transition, callback) {
		                var n = 0;
		                transition
		                    .each(function () { ++n; })
		                    .each("end", function () { if (!--n) callback.apply(this, arguments); });
		            }

		            //wire up the SignalR client and listen for messages
		            var chat = $.connection.dashHub;
		            chat.client.broadcastMessage = function (message) {
		                //parse the JSON data
		                var incomingData = JSON.parse(message);
		                //stuff it in the global array slot for the device ID
		                deviceValue[incomingData.device] = incomingData.temperature;

		            };
		            //start listening
		            $.connection.hub.start();
		            //tick for D3 graphics
		            tick();

		            function tick() {
		                // update the domains
		                now = new Date();
		                x.domain([now - (n - 2) * duration, now - duration]);

		                //push the (presumably) fresh data deviceValue array onto
		                //the arrays that define the lines.
		                for (i = 0; i < 10; i++) {
		                    data[i].push({ value: deviceValue[i] });
		                    //data[1].push({ value: maxValue });
		                }
		                //slide the x-axis left
		                axis.transition()
		                    .duration(duration)
		                    .ease("linear")
		                    .call(x.axis);

		                //Update the paths based on the updated line data
		                //and slide left
		                path
		                    .attr("d", line)
		                    .attr("transform", null)
		                .transition()
		                    .duration(duration)
		                    .ease("linear")
		                    .attr("transform", "translate(" + x(now - (n - 1) * duration) + ",0)")
		                    .call(endall, tick);

		                // pop the old data point off the front
		                // of the arrays
		                for (var i = 0; i < data.length; i++) {
		                    data[i].shift();
		                };
		            };
		         })()
		        </script>
		    </body>
		</html>

	> [AZURE.NOTE] パッケージ マネージャーによって、新しいバージョンの SignalR スクリプトがインストールされる場合があります。以降に示すスクリプト リファレンスが、プロジェクトのスクリプト ファイルのバージョンに対応していることを確認してください (ハブを追加する代わりに NuGet を使用して SignalR を追加した場合は異なります)。

11. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[追加]、[HTML ページ]** の順に選択します。新しいページに "**test.html**" という名前を付けます。このページは、メッセージの送受信による DashHub とダッシュボードのテストに使用できます。

12. **test.html** ファイルのコードを次のコードに置き換えます。

		<!DOCTYPE html>
		<html>
		<head>
		    <title>Test</title>
		    <style type="text/css">
		        .container {
		            background-color: #99CCFF;
		            border: thick solid #808080;
		            padding: 20px;
		            margin: 20px;
		        }
		    </style>
		</head>
		<body>
		    <div class="container">
		        <input type="text" id="message" />
		        <input type="button" id="sendmessage" value="Send" />
		        <input type="hidden" id="displayname" />
		        <ul id="discussion"></ul>
		    </div>
		    <!--Script references. -->
		    <!--Reference the jQuery library. -->
		    <script src="Scripts/jquery-1.10.2.min.js"></script>
		    <!--Reference the SignalR library. -->
		    <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
		    <!--Reference the autogenerated SignalR hub script. -->
		    <script src="signalr/hubs"></script>
		    <!--Add script to update the page and send messages.-->
		    <script type="text/javascript">
		        $(function () {
		            // Declare a proxy to reference the hub.
		            var chat = $.connection.dashHub;
		            // Create a function that the hub can call to broadcast messages.
		            chat.client.broadcastMessage = function (message) {
		                // Html encode display the message.
		                var encodedMsg = $('<div />').text(message).html();
		                // Add the message to the page.
		                $('#discussion').append('<li>' + encodedMsg + '</li>');
		            };
		            // Set initial focus to message input box.
		            $('#message').focus();
		            // Start the connection.
		            $.connection.hub.start().done(function () {
		                $('#sendmessage').click(function () {
		                    // Call the Send method on the hub.
		                    chat.server.send($('#message').val());
		                    // Clear text box and reset focus for next comment.
		                    $('#message').val('').focus();
		                });
		            });
		        });
		    </script>
		</body>
		</html>

13. **[すべて保存]** をプロジェクトでクリックします。

14. **ソリューション エクスプローラー**で、**ダッシュボード** プロジェクトを右クリックして **[発行]** をクリックします。このプロジェクトのために作成した Web サイトを右クリックし、**[発行]** をクリックします。

15. サイトが発行されると Web ページが開き、タイムラインの動きが表示されます。

### ダッシュボードのテスト

1. SignalR が動作していることと、SignalR に送信されたデータのグラフの線がダッシュボードに表示されることを確認するには、新しいブラウザー ウィンドウでこの Web サイトの **test.html** ページを開きます。たとえば、**http://mydashboard.azurewebsites.net/test.html** のように指定します。

2. ダッシュボードは、**デバイス ID** と**温度**の値を持つ JSON 形式のデータを想定しています。たとえば、**{"device":0, "temperature":80}** という形式です。ダッシュボードが別のページで開いている間に、デバイス ID に 0 から 9 を使用して、**test.html** ページにテスト用の値をいくつか入力します。各デバイス ID の行は、別の色で表示されます。

## Event Hub の構成

Event Hub は、センサーから発生するメッセージ (イベント) の受信に使用されます。新しい Event Hub を作成するには、次の手順に従います。

1. [Azure ポータル](https://manage.windowsazure.com)から、**[新規作成]、[Service Bus]、[Event Hub]、[カスタム作成]** の順に選択します。

2. **[新しい Event Hub の追加]** ダイアログで **Event Hub 名**を入力し、ハブを作成する **[リージョン]** を選択して、新しい名前空間を作成するか、既存の名前空間を選択します。最後に、**矢印**をクリックします。

3. **[Event Hub の構成]** ダイアログで、**パーティション カウント**と**メッセージ保持の値**を入力します。この例では、パーティション カウントに 10 を、メッセージ保持に 1 を使用します。

4. Event Hub が作成されたら、名前空間を選択し、**[Event Hubs]** を選択します。最後に、先ほど作成した Event Hub を選択します。

5. **[構成]** を選択し、次の情報を使用して新しいアクセス ポリシーを 2 つ作成します。

	<table>
	<tr><th>名前</th><th>アクセス許可</th></tr>
	<tr><td>デバイス</td><td>Send</td></tr>
	<tr><td>Storm</td><td>Listen</td></tr>
	</table>

	アクセス許可の作成後、ページの下部にある **[保存]** アイコンをクリックします。これにより、このハブへのメッセージの送信や、このハブから受信したメッセージの読み取りに使用される共有アクセス ポリシーが作成されます。

6. ポリシーの保存後、ページの下部にある **[共有アクセス キー生成コンポーネント]** を使用して、**デバイス**と **Storm** のポリシーの両方のキーを取得します。後で使用できるように、これらを保存します。

### Event Hub へのメッセージ送信

だれでも利用できるセンサーの簡単な標準セットがないため、.NET アプリケーションを乱数の生成に使用します。次の手順で作成した .NET アプリケーションは、キーを押してアプリケーションが停止されるまで、毎秒 10 個のデバイスに対してイベントを生成します。

1. Visual Studio で、新しい **Windows デスクトップ** プロジェクトを作成し、**コンソール アプリケーション** プロジェクト テンプレートを選択します。プロジェクトに "**SendEvents**" という名前を付けて **[OK]** をクリックします。

2. **ソリューション エクスプローラー**で **SendEvents** を右クリックし、**[NuGet パッケージの管理]** を選択します。

3. **[NuGet パッケージの管理]** で、次のパッケージを検索してインストールします。

	* **Microsoft Azure Service Bus**
	* **JSON.Net**

	パッケージをインストールしたら、パッケージ マネージャーのウィンドウを**閉じ**ます。

4. **Program.cs** の内容を次の内容に置き換えます。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.ServiceBus.Messaging;
		using Newtonsoft.Json;
		using Microsoft.ServiceBus;
		using System.Threading;

		namespace SendEvents
		{
		    class Program
		    {

		        static int numberOfDevices = 10;
		        static string eventHubName = "temperature";
		        static string eventHubNamespace = "namespace";
		        static string sharedAccessPolicyName = "devices";
		        static string sharedAccessPolicyKey = "key for devices policy";

		        static void Main(string[] args)
		        {
		            var settings = new MessagingFactorySettings()
		            {
		                TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(sharedAccessPolicyName, sharedAccessPolicyKey),
		                TransportType = TransportType.Amqp
		            };
		            var factory = MessagingFactory.Create(
		                 ServiceBusEnvironment.CreateServiceUri("sb", eventHubNamespace, ""), settings);

		            EventHubClient client = factory.CreateEventHubClient(eventHubName);

		            try
		            {

		                List<Task> tasks = new List<Task>();
		                // Send messages to Event Hub
		                Console.WriteLine("Sending messages to Event Hub {0}", client.Path);
		                Random random = new Random();
		                //for (int i = 0; i < numberOfMessages; ++i)
		                while(!Console.KeyAvailable)
		                {
		                    // One event per device
		                    for(int devices = 0; devices < numberOfDevices; devices++)
		                    {
		                        // Create the device/temperature metric
		                        Event info = new Event() {
		                            TimeStamp = DateTime.UtcNow,
		                            DeviceId = random.Next(numberOfDevices),
		                            Temperature = random.Next(100)
		                        };
		                        // Serialize to JSON
		                        var serializedString = JsonConvert.SerializeObject(info);
		                        Console.WriteLine(serializedString);
		                        EventData data = new EventData(Encoding.UTF8.GetBytes(serializedString))
		                        {
		                            PartitionKey = info.DeviceId.ToString()
		                        };

		                        // Send the metric to Event Hub
		                        tasks.Add(client.SendAsync(data));
		                    }
		                    // Sleep a second
		                    Thread.Sleep(1000);
		                };

		                Task.WaitAll(tasks.ToArray());
		            }
		            catch (Exception exp)
		            {
		                Console.WriteLine("Error on send: " + exp.Message);
		            }

		        }
		    }
		}

	この時点では、Event クラスを参照する行で警告が発生します。これらは、この時点では無視してください。

5. **Program.cs** ファイルで、ファイルの先頭にある次の変数の値を Azure の管理ポータルで Event Hub から取得した対応する値に設定します。

	<table>
	<tr><th>設定する変数</th><th>設定する値</th></tr>
	<tr><td>eventHubName</td><td>イベント ハブの名前。たとえば、 <strong>temperature</strong> など。</td></tr>
	<tr><td>eventHubNamespace</td><td>イベント ハブの名前空間。たとえば、 <strong>sensors-ns</strong> など。</td></tr>
	<tr><td>sharedAccessPolicyName</td><td>Send アクセス許可を使用して作成したポリシー。たとえば、 <strong>デバイス</strong>など。</td></tr>
	<tr><td>sharedAccessPolicyKey</td><td>Send アクセス許可を使用して作成したポリシーのキー。</td></tr>
	</table>

6. **ソリューション エクスプローラー**で、**SendEvents** を右クリックし、**[追加]、[クラス]** の順にクリックします。新しいクラスに "**Event.cs**" という名前を付けます。これは、Event Hub に送信されるメッセージを表します。

7. **Event.cs** の内容を次の内容に置き換えます。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Runtime.Serialization;
		using System.Text;
		using System.Threading.Tasks;

		namespace SendEvents
		{
		    [DataContract]
		    public class Event
		    {
		    	[DataMember]
		    	public DateTime TimeStamp { get; set; }
		        [DataMember]
		        public DateTime TimeStamp { get; set; }
		        [DataMember]
		        public int DeviceId { get; set; }
		        [DataMember]
		        public int Temperature { get; set; }
		    }
		}

	このクラスは、送信するデータ (TimeStamp、DeviceID、Temperature の値) を表します。

8. **[すべて保存]** をクリックし、アプリケーションを実行すると、Event Hub にメッセージが入力されます。

## Azure Virtual Network の作成

Storm クラスターで実行しているトポロジが HBase と直接通信できるように、両方のサーバーを Azure Virtual Network にプロビジョニングする必要があります。

1. [Azure 管理ポータル][azure-portal]にサインインします。

2. ページ下部で **[+ 新規]**、**[ネットワーク サービス]**、**[Virtual Network]** の順にクリックし、さらに **[簡易作成]** をクリックします。

3. 次の値を入力または選択します。

	- **名前**:仮想ネットワークの名前
	- **アドレス空間**:クラスター内のすべてのノードにアドレスを提供するために十分な大きさの、仮想ネットワークのアドレス空間を選択します。そうでないと、プロビジョニングは失敗します。
	- **最大 VM 数**:最大 VM 数のいずれかを選択します。
	- **場所**:場所は作成する HBase クラスターと同じである必要があります。
	- **DNS サーバー**:この記事では、Azure が提供する内部 DNS サーバーを使用するため、**[なし]** を選択します。カスタム DNS サーバーを使用した、より高度なネットワーク構成もサポートされています。詳細なガイダンスについては、[http://msdn.microsoft.com/library/azure/jj156088.aspx](http://msdn.microsoft.com/library/azure/jj156088.aspx) を参照してください。

4. **[仮想ネットワークの作成]** をクリックします。新しい仮想ネットワーク名が一覧に表示されます。[ステータス] 列に **[作成済み]** が表示されるまで待機します。

5. メイン ウィンドウで、作成した仮想ネットワークをクリックします。

6. ページの上部で、**[ダッシュボード]** をクリックします。

7. **[概要]** の下の **[仮想ネットワーク ID]** を書き留めます。この ID は、Storm と HBase クラスターのプロビジョニング時に必要です。

8. ページの上部で、**[構成]** をクリックします。

9. ページの下部に示される既定のサブネット名は、**Subnet-1** です。**[サブネットの追加]** をクリックして "**Subnet-2**" を追加します。これらのサブネットには、Storm クラスターと HBase クラスターが収容されます。

	> [AZURE.NOTE] この記事では、ノードが 1 つのみのクラスターを使用します。マルチノード クラスターを作成する場合は、クラスターに使用されるサブネットの **[CIDR (アドレス数)]** を確認する必要があります。アドレス数は、ワーカー ノード数に 7 (ゲートウェイ:2、ヘッドノード:2、Zookeeper:3)。たとえば、10 ノードの HBase クラスターが必要な場合、サブネットのアドレス数は、17 (10+7) を超えている必要があります。17 以下の場合、デプロイメントは失敗します。
	>
	> 1 つのクラスターには単一のサブネットを指定することを強くお勧めします。

11. ページの下部にある **[保存]** をクリックします。

## HDInsight での Storm クラスターの作成

1. [Azure 管理ポータル][azureportal]にサインインします。

2. 左側にある **[HDInsight]** をクリックし、ページの左下隅にある **[+ 新規]** をクリックします。

3. 2 番目の列にある [HDInsight] アイコンをクリックし、**[カスタム]** を選択します。

4. **[クラスターの詳細]** ページで、新しいクラスターの名前を入力し、**[クラスターの種類]** に **[Storm]** を選択します。矢印を選択して続行します。

5. このクラスターで使用する**データ ノード**の数に 1 を入力します。**[リージョン/仮想ネットワーク]** に、先ほど作成した Azure Virtual Network を選択します。**[仮想ネットワーク サブネット]** に **Subnet-2** を選択します。

	> [AZURE.NOTE] この記事で使用するクラスターのコストを最小限に抑えるには、**クラスター サイズ**を 1 に削減し、使用終了後にクラスターを削除します。

6. 管理者の**ユーザー名**と**パスワード**を入力し、矢印を選択して続行します。

7. **ストレージ アカウント**の場合、**[新しいストレージを作成する]** を選択するか、既存のストレージ アカウントを選択します。使用する**アカウント名**と**既定のコンテナー**を選択するか、入力します。左下にあるチェック アイコンをクリックして、Storm クラスターを作成します。

## HDInsight HBase クラスターの作成

1. [Azure 管理ポータル][azureportal]にサインインします。

2. 左側にある **[HDInsight]** をクリックし、ページの左下隅にある **[+ 新規]** をクリックします。

3. 2 番目の列にある [HDInsight] アイコンをクリックし、**[カスタム]** を選択します。

4. **[クラスターの詳細]** ページで、新しいクラスターの名前を入力し、**[クラスターの種類]** に **[HBase]** を選択します。矢印を選択して続行します。

5. このクラスターで使用する**データ ノード**の数に 1 を入力します。**[リージョン/仮想ネットワーク]** に、先ほど作成した Azure Virtual Network を選択します。**[仮想ネットワーク サブネット]** に **Subnet-1** を選択します。

	> [AZURE.NOTE] この記事で使用するクラスターのコストを最小限に抑えるには、**クラスター サイズ**を 1 に削減し、使用終了後にクラスターを削除します。

6. 管理者の**ユーザー名**と**パスワード**を入力し、矢印を選択して続行します。

7. **ストレージ アカウント**の場合、**[新しいストレージを作成する]** を選択するか、既存のストレージ アカウントを選択します。使用する**アカウント名**と**既定のコンテナー**を選択するか、入力します。左下にあるチェック アイコンをクリックして、Storm クラスターを作成します。

	> [AZURE.NOTE] Storm クラスターに使用されるコンテナーとは別のコンテナーを使用する必要があります。

### リモート デスクトップを有効にする

このチュートリアルでは、リモート デスクトップを使用して Storm クラスターと HBase クラスターにアクセスする必要があります。次の手順に従って、両方のリモート デスクトップを有効にします。

1. [Azure 管理ポータル][azureportal]にサインインします。

2. 左側にある **[HDInsight]** を選択して、リストから Storm クラスターを選択します。最後に、ページの上部にある **[構成]** をクリックします。

3. ページの下部にある **[リモートを有効にする]** をクリックします。メッセージが表示されたら、ユーザー名、パスワード、およびリモート デスクトップ アクセスが期限切れになる日付を入力します。チェックマークをクリックしてリモート デスクトップを有効にします。

リモート デスクトップが有効になったら、ページの下部にある **[接続]** をクリックできます。メッセージに従ってクラスターに接続します。

### HBase DNS サフィックスの探索

Storm クラスターから HBase に書き込むためには、HBase クラスターに完全修飾ドメイン名 (FQDN) を使用する必要があります。次の手順に従って、この情報を探索します。

1. リモート デスクトップを使用して HBase クラスターに接続します。

2. クラスターに接続したら、Hadoop コマンド ラインを開き、**ipconfig** コマンドを実行して DNS サフィックスを取得します。**接続固有の DNS サフィックス**にサフィックスの値が含まれています。たとえば、**mycluster.b4.internal.cloudapp.net** などです。この情報は保存してください。

## Storm トポロジの開発

> [AZURE.NOTE] このセクションの手順は、ローカル開発環境で実行する必要があります。

### 外部依存関係のダウンロードとビルド

このプロジェクトで使用する依存関係のいくつかは、個別にダウンロードしてビルドしてから、開発環境の Maven のローカル リポジトリにインストールする必要があります。このセクションでは、以下のものをダウンロードしてインストールします。

* Event Hub からのメッセージを読み取る Event Hub スパウト

* SignalR Java クライアント SDK

#### Event Hub スパウトのダウンロードとビルド

Event Hub からデータを受信するために、**eventhubs-storm-spout** を使用します。

1. リモート デスクトップを使用して Storm クラスターに接続し、**%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar** ファイルをローカル開発環境にコピーします。これには **events-storm-spout** が含まれています。

2. 次のコマンドを使用して Maven のローカル ストアにパッケージをインストールします。これにより、Storm プロジェクトの参照として、後の手順で容易に追加できます。

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

#### SignalR クライアントのダウンロードとビルド

ASP.NET ダッシュボードにメッセージを送信するには、[SignalR client SDK for Java (SignalR Java クライアント SDK)](https://github.com/SignalR/java-client) を使用します。

1. コマンド プロンプトを開きます。

2. SignalR クライアント SDK プロジェクトをダウンロードして格納する場所に、ディレクトリを変更します。

3. 次のコマンドを使用して GitHub からプロジェクトをダウンロードします。

	git clone https://github.com/SignalR/java-client

4. ディレクトリを **java-client\signalr-client-sdk** ディレクトリに変更し、次のコマンドを使用してプロジェクトを JAR ファイルにコンパイルします。

		cd java-client\signalr-client-sdk
		mvn package

	> [AZURE.NOTE] **gson** 依存関係をダウンロードできないというエラーが発生した場合は、**java-client\signalr-client-sdk\pom.xml** ファイルの次の行を削除します。
	> 
<repository>
<id>central</id>
<name>Central</name>
<url>http://maven.eclipse.org/build</url>
</repository>
</repositories>

	> これらの行を削除すると Maven は中央リポジトリからファイルをプルします (既定の動作)。Maven で強制的にリポジトリからプルし直すには、`-U` コマンドを使用します。たとえば、 `mvn package -U`　のように指定します。

5. 次のコマンドを使用して Maven のローカル ストアにパッケージをインストールします。これにより、Storm プロジェクトの参照として、後の手順で容易に追加できます。

		mvn install:install-file -Dfile=target/signalr-client-sdk-1.0.jar -DgroupId=microsoft.aspnet.signalr -DartifactId=signalr-client-sdk -Dversion=1.0 -Dpackaging=jar

### Storm トポロジ プロジェクトのスキャフォールディング

Event Hub スパウトと SignalR クライアントをローカル リポジトリにインストールした後は、Maven を使用して Storm トポロジ プロジェクトのスキャフォールディングを作成します。

1. コマンド プロンプト、Bash セッション、ターミナル セッションなど、システムでコマンド入力に使用する機能を開きます。

2. このプロジェクトを作成する場所にディレクトリを変更します。たとえば、すべてのコード プロジェクトを格納するディレクトリがある場合は、そのディレクトリに変更します。

3. 次の Maven コマンドを使用して、アプリケーションの基本的なスキャフォールディングを作成します。

		mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.examples -DartifactId=TemperatureMonitor -DinteractiveMode=false

	このコマンドは、次の処理を実行します。

	* 指定された  *artifactId* を使用して新しいディレクトリを作成します。例では **Temperature** が使用されます。
	* **pom.xml** ファイルを作成します。このファイルにはこのプロジェクトの Maven 情報が含まれます。
	* **src** ディレクトリ構造を作成します。このディレクトリ構造には基本的なコードとテストが含まれます。

### 依存関係とプラグインの追加

次に、このプロジェクトの依存関係、およびビルドとパッケージ化の際に使用する Maven プラグインを参照するように、**pom.xml** を変更します。

1. テキスト エディターを使用して **pom.xml** ファイルを開き、**&lt;dependencies>** セクション内に次のコードを追加します。このコードは、セクションの最後の、junit の後に追加します。

		<dependency>
	      <groupId>org.apache.storm</groupId>
	      <artifactId>storm-core</artifactId>
	      <version>0.9.2-incubating</version>
	      <!-- keep storm out of the jar-with-dependencies -->
	      <scope>provided</scope>
	    </dependency>
	    <dependency>
	      <groupId>microsoft.aspnet.signalr</groupId>
	      <artifactId>signalr-client-sdk</artifactId>
	      <version>1.0</version>
	    </dependency>
	    <dependency>
	      <groupId>com.microsoft.eventhubs</groupId>
	      <artifactId>eventhubs-storm-spout</artifactId>
	      <version>0.9</version>
	    </dependency>
	    <dependency>
	      <groupId>com.google.code.gson</groupId>
	      <artifactId>gson</artifactId>
	      <version>2.2.2</version>
	    </dependency>
		<dependency>
      	  <groupId>com.github.ptgoetz</groupId>
      	  <artifactId>storm-hbase</artifactId>
      	  <version>0.1.2</version>
    	</dependency>
	    <dependency>
	      <groupId>com.netflix.curator</groupId>
	      <artifactId>curator-framework</artifactId>
	      <version>1.3.3</version>
	      <exclusions>
	        <exclusion>
	          <groupId>log4j</groupId>
	            <artifactId>log4j</artifactId>
	          </exclusion>
	        <exclusion>
	          <groupId>org.slf4j</groupId>
	            <artifactId>slf4j-log4j12</artifactId>
	        </exclusion>
	      </exclusions>
	      <scope>provided</scope>
	    </dependency>

	これにより次の依存関係が追加されます。

	* eventhubs-storm-spout - Event Hub スパウト
	* signalr-client-sdk - SignalR クライアント
	* gson - これは SignalR クライアントの依存関係であり、SignalR に書き込むときに JSON の作成に使用されます。
	* storm-core - Storm アプリケーションの主要な機能を提供します。
	* slf4j - ログ機能を提供し、eventhubs-storm-spout で使用されます。
	* curator-framework - eventhubs-storm-spout で使用されます。
	* storm-core - Storm の主要なクラス
	* storm-hbase - HBase への書き込みを許可するクラス

	> [AZURE.NOTE] 一部の依存関係は、Maven リポジトリからダウンロードしてアプリケーションのビルドとテストにローカルで使用する必要があることを示すために、**provided** のスコープでマークされていますが、ランタイム環境でも利用できるようになるため、コンパイルしてこのプロジェクトで作成した JAR に含める必要はありません。

2. **pom.xml** ファイルの最後の、**&lt;/project>** エントリの直前に、次のコードを追加します。

		  <build>
		    <plugins>
		      <plugin>
		        <groupId>org.apache.maven.plugins</groupId>
		        <artifactId>maven-compiler-plugin</artifactId>
		        <version>2.3.2</version>
		        <configuration>
		          <source>1.7</source>
		          <target>1.7</target>
		        </configuration>
		      </plugin>
		      <plugin>
		        <groupId>org.apache.maven.plugins</groupId>
		        <artifactId>maven-shade-plugin</artifactId>
		        <version>2.3</version>
		        <configuration>
		          <transformers>
		            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		            </transformer>
		          </transformers>
		        </configuration>
		        <executions>
		          <execution>
		            <phase>package</phase>
		            <goals>
		              <goal>shade</goal>
		            </goals>
		          </execution>
		        </executions>
		      </plugin>
		      <plugin>
		        <groupId>org.codehaus.mojo</groupId>
		        <artifactId>exec-maven-plugin</artifactId>
		        <version>1.2.1</version>
		        <executions>
		          <execution>
		          <goals>
		            <goal>exec</goal>
		          </goals>
		          </execution>
		        </executions>
		        <configuration>
		          <executable>java</executable>
		          <includeProjectDependencies>true</includeProjectDependencies>
		          <includePluginDependencies>false</includePluginDependencies>
		          <classpathScope>compile</classpathScope>
		          <mainClass>${storm.topology}</mainClass>
		        </configuration>
		      </plugin>
		    </plugins>
		    <resources>
		      <resource>
		        <directory>${basedir}/conf</directory>
		        <filtering>false</filtering>
		        <includes>
		          <include>Config.properties</include>
				  <include>hbase-site.xml</include>
		        </includes>
		      </resource>
		    </resources>
		  </build>

	プロジェクトのビルド時、このコードは Maven に次の内容を実行するように伝えます。

	* **/conf/Config.properties** リソース ファイルを含める。このファイルは後で作成されますが、Azure Event Hub に接続するための構成情報を含みます。
	* **/conf/hbase-site.xml** リソース ファイルを含める。このファイルは後で作成されますが、HBase への接続方法に関する情報を含みます。
	* **maven-compiler-plugin** を使用してこのアプリケーションをコンパイルする。
	* **maven-shade-plugin** を使用して uberjar または fat jar をビルドする。このファイルには、このプロジェクトと必要な依存関係が含まれます。
	* **exec-maven-plugin** を使用する。これにより、Hadoop クラスターなしでローカルでアプリケーションを実行できます。

### 構成ファイルの追加

**eventhubs-storm-spout** は構成情報を **Config.properties** ファイルから読み取ります。これは、接続先の Event Hub を指定します。クラスターでトポロジを開始するときに構成ファイルを指定できますが、プロジェクトに構成ファイルを 1 つを含めておくと、既知の既定の構成を使用できます。

1. **Temperature** ディレクトリに、"**conf**" という名前の新しいディレクトリを作成します。

2. **conf** ディレクトリに、新しいファイルを 2 つ作成します。

	* **Config.properties** - Event Hub の設定を含む
	* **hbase-site.xml** - HBase への接続の設定を含む

3. **Config.properties** ファイルの内容として次のコードを使用します。

		eventhubspout.username = storm

		eventhubspout.password = <the key of the 'storm' policy>

		eventhubspout.namespace = <the event hub namespace>

		eventhubspout.entitypath = <the event hub name>

		eventhubspout.partitions.count = <the number of partitions for the event hub>

		# if not provided, will use storm's zookeeper settings
		# zookeeper.connectionstring=localhost:2181

		eventhubspout.checkpoint.interval = 10

		eventhub.receiver.credits = 1024

	**password** を、Event Hub で前に作成した **storm** ポリシーのキーで置き換えます。
	
	**namespace** を Event Hub の名前空間で置き換えます。
	
	**entitpath** を Event Hub の名前で置き換えます。

4. **hbase-site.xml** ファイルの内容として次のコードを使用します。

		<?xml version="1.0"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
		<!--
		/**
		 * Copyright 2010 The Apache Software Foundation
		 *
		 * Licensed to the Apache Software Foundation (ASF) under one
		 * or more contributor license agreements.  See the NOTICE file
		 * distributed with this work for additional information
		 * regarding copyright ownership.  The ASF licenses this file
		 * to you under the Apache License, Version 2.0 (the
		 * "License"); you may not use this file except in compliance
		 * with the License.  You may obtain a copy of the License at
		 *
		 *     http://www.apache.org/licenses/LICENSE-2.0
		 *
		 * Unless required by applicable law or agreed to in writing, software
		 * distributed under the License is distributed on an "AS IS" BASIS,
		 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		 * See the License for the specific language governing permissions and
		 * limitations under the License.
		 */
		-->
		<configuration>
		  <property>
		    <name>hbase.cluster.distributed</name>
		    <value>true</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.quorum</name>
		    <value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.property.clientPort</name>
		    <value>2181</value>
		  </property>
		</configuration>

5. **hbase-site.xml** ファイルで、zookeeper エントリの **suffix** の値を、HBase のために前に取得した DNS サフィックスで置き換えます。たとえば、"**zookeeper0.mycluster.b4.internal.cloudapp.net, zookeeper1.mycluster.b4.internal.cloudapp.net, zookeeper2.mycluster.b4.internal.cloudapp.net**" のように指定します。

6. ファイルを保存します。

### ヘルパーの追加

JSON との双方向のシリアル化をサポートするには、オブジェクト構造を定義するヘルパー クラスが必要になります。

1. **\temperaturemonitor\src\main\java\com\microsoft\examples** ディレクトリに、"**helpers**" という名前の新しいディレクトリを作成します。

2. **helpers** ディレクトリに、新しいファイルを 2 つ作成します。

	* **EventHubMessage.java** - Event Hub メッセージ形式を定義します。

	* **SignalRMessage.java** - SignalR に送信するメッセージの形式を定義します。

3. **EventHubMessage.java** ファイルの内容として次のコードを使用します。

		package com.microsoft.examples;

		public class EventHubMessage {
		  String TimeStamp;
		  int DeviceId;
		  int Temperature;
		}

4. **SignalRMessage.java** ファイルの内容として次のコードを使用します。

		package com.microsoft.examples;

		public class SignalRMessage {
		  int device;
		  int temperature;
		}

5. これらのファイルを保存して閉じます。

### ボルトの追加

トポロジの主要な処理を実行するのがボルトがです。このトポロジでは、3 つのボルトがあり、その 1 つが、プロジェクトのビルド時に自動的にダウンロードされる HBase ボルトです。

1. \**\temperaturemonitor\src\main\java\com\microsoft\examples** ディレクトリに、"**bolts**" という名前の新しいディレクトリを作成します。

2. **bolts** ディレクトリに、新しいファイルを 2 つ作成します。

	* **ParserBolt.java** - Event Hub から受信したメッセージを個別のフィールドへと解析し、2 つのストリームを発します。
	* **DashboardBolt.java** - SignalR を介して Web のダッシュボードに情報を記録します。

3. **ParserBolt.java** ファイルの内容として次のコードを使用します。

		package com.microsoft.examples;

		import backtype.storm.topology.base.BaseBasicBolt;
		import backtype.storm.topology.BasicOutputCollector;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.tuple.Tuple;
		import backtype.storm.tuple.Fields;
		import backtype.storm.tuple.Values;

		import com.google.gson.Gson;
		import com.google.gson.GsonBuilder;

		public class ParserBolt extends BaseBasicBolt {

		  //Declare output fields & streams
		  //hbasestream is all fields, and goes to hbase
		  //dashstream is just the device and temperature, and goes to the dashboard
		  @Override
		  public void declareOutputFields(OutputFieldsDeclarer declarer) {
		    declarer.declareStream("hbasestream", new Fields("timestamp", "deviceid", "temperature"));
		    declarer.declareStream("dashstream", new Fields("deviceid", "temperature"));
		  }

		  //Process tuples
		  @Override
		  public void execute(Tuple tuple, BasicOutputCollector collector) {
		    Gson gson = new Gson();
		    //Should only be one tuple, which is the JSON message from the spout
		    String value = tuple.getString(0);

			//Deal with cases where we get multiple
			//EventHub messages in one tuple
			String[] arr = value.split("}");
			for (String ehm : arr)
			{

			    //Convert it from JSON to an object
				EventHubMessage msg = new Gson().fromJson(ehm.concat("}"),EventHubMessage.class);

			    //Pull out the values and emit as a stream
			    String timestamp = msg.TimeStamp;
			    int deviceid = msg.DeviceId;
			    int temperature = msg.Temperature;
			    collector.emit("hbasestream", new Values(timestamp, deviceid, temperature));
			    collector.emit("dashstream", new Values(deviceid, temperature));
			}
		  }
		}

4. **DashboardBolt.java** ファイルの内容として次のコードを使用します。

		package com.microsoft.examples;

		import backtype.storm.topology.BasicOutputCollector;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.topology.base.BaseBasicBolt;
		import backtype.storm.tuple.Tuple;
		import backtype.storm.task.TopologyContext;
		import backtype.storm.Config;
		import backtype.storm.Constants;

		import microsoft.aspnet.signalr.client.Action;
		import microsoft.aspnet.signalr.client.ErrorCallback;
		import microsoft.aspnet.signalr.client.LogLevel;
		import microsoft.aspnet.signalr.client.Logger;
		import microsoft.aspnet.signalr.client.MessageReceivedHandler;
		import microsoft.aspnet.signalr.client.hubs.HubConnection;
		import microsoft.aspnet.signalr.client.hubs.HubProxy;

		import com.google.gson.Gson;
		import com.google.gson.GsonBuilder;

		import java.util.Map;

		public class DashboardBolt extends BaseBasicBolt {
		  //Connection and proxy for SignalR hub
		  private HubConnection conn;
		  private HubProxy proxy;

		  //Declare output fields
		  @Override
		  public void declareOutputFields(OutputFieldsDeclarer declarer) {
		    //no stream output - we talk directly to SignalR
		  }

		  @Override
		  public void prepare(Map config, TopologyContext context) {

		    // Connect to the DashHub SignalR server
		    conn = new HubConnection("http://dashboard.azurewebsites.net/");
		    // Create the hub proxy
		    proxy = conn.createHubProxy("DashHub");
		    // Subscribe to the error event
		    conn.error(new ErrorCallback() {
		      @Override
		      public void onError(Throwable error) {
		        error.printStackTrace();
		      }
		    });
		    // Subscribe to the connected event
		    conn.connected(new Runnable() {
		      @Override
		      public void run() {
		        System.out.println("CONNECTED");
		      }
		    });
		    // Subscribe to the closed event
		    conn.closed(new Runnable() {
		      @Override
		      public void run() {
		        System.out.println("DISCONNECTED");
		      }
		    });
		    // Start the connection
		    conn.start()
		      .done(new Action<Void>() {
		        @Override
		        public void run(Void obj) throws Exception {
		          System.out.println("Done Connecting!");
		        }
		    });
		  }

		  //Process tuples
		  @Override
		  public void execute(Tuple tuple, BasicOutputCollector collector) {
		    Gson gson = new Gson();
		    try {
		      //Get the deviceid and temperature by field name
		      int deviceid = tuple.getIntegerByField("deviceid");
		      int temperature = tuple.getIntegerByField("temperature");
		      //Construct the SignalR message
		      SignalRMessage srMessage = new SignalRMessage();
		      srMessage.device = deviceid;
		      srMessage.temperature = temperature;
		      // send it as JSON
		      proxy.invoke("send", gson.toJson(srMessage));
		    } catch (Exception e) {
		       // LOG.error("Bolt execute error: {}", e);
		       collector.reportError(e);
		    }
		  }
		}

	`http://dashboard.azurewebsites.net/` を、前にダッシュボードを発行した Azure Web サイトのアドレスで置き換えます。たとえば、http://mydashboard.azurewebsites.net です。

5. ファイルを保存して閉じます。

### トポロジの定義

トポロジは、トポロジ内のスパウトとボルトの間のデータ フローに加えて、トポロジの並列処理の次数およびトポロジ内のコンポーネントも表します。

1. **\temperaturemonitor\src\main\java\com\microsoft\examples** ディレクトリに、"**Temperature.java**" という名前の新しいディレクトリを作成します。

2. **Temperature.java** ファイルを開き、内容として次のコードを使用します。

		package com.microsoft.examples;

		import backtype.storm.Config;
		import backtype.storm.LocalCluster;
		import backtype.storm.StormSubmitter;
		import backtype.storm.generated.StormTopology;
		import backtype.storm.topology.TopologyBuilder;
		import backtype.storm.tuple.Fields;
		import com.microsoft.eventhubs.spout.EventHubSpout;
		import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

		import java.io.FileReader;
		import java.util.Properties;

		//hbase
		import org.apache.storm.hbase.bolt.mapper.SimpleHBaseMapper;
		import org.apache.storm.hbase.bolt.HBaseBolt;
		import java.util.Map;
		import java.util.HashMap;
		import backtype.storm.tuple.Fields;

		public class Temperature
		{
		  protected EventHubSpoutConfig spoutConfig;
		  protected int numWorkers;

		  // Reads the configuration information for the Event Hub spout
		  protected void readEHConfig(String[] args) throws Exception {
		    Properties properties = new Properties();
		    if(args.length > 1) {
		      properties.load(new FileReader(args[1]));
		    }
		    else {
		      properties.load(Temperature.class.getClassLoader().getResourceAsStream(
		        "Config.properties"));
		    }

		    String username = properties.getProperty("eventhubspout.username");
		    String password = properties.getProperty("eventhubspout.password");
		    String namespaceName = properties.getProperty("eventhubspout.namespace");
		    String entityPath = properties.getProperty("eventhubspout.entitypath");
		    String zkEndpointAddress = properties.getProperty("zookeeper.connectionstring");
		    int partitionCount = Integer.parseInt(properties.getProperty("eventhubspout.partitions.count"));
		    int checkpointIntervalInSeconds = Integer.parseInt(properties.getProperty("eventhubspout.checkpoint.interval"));
		    int receiverCredits = Integer.parseInt(properties.getProperty("eventhub.receiver.credits"));
		    System.out.println("Eventhub spout config: ");
		    System.out.println("  partition count: " + partitionCount);
		    System.out.println("  checkpoint interval: " + checkpointIntervalInSeconds);
		    System.out.println("  receiver credits: " + receiverCredits);
		    spoutConfig = new EventHubSpoutConfig(username, password,
		      namespaceName, entityPath, partitionCount, zkEndpointAddress,
		      checkpointIntervalInSeconds, receiverCredits);

		    //set the number of workers to be the same as partition number.
		    //the idea is to have a spout and a partial count bolt co-exist in one
		    //worker to avoid shuffling messages across workers in storm cluster.
		    numWorkers = spoutConfig.getPartitionCount();

		    if(args.length > 0) {
		      //set topology name so that sample Trident topology can use it as stream name.
		      spoutConfig.setTopologyName(args[0]);
		    }
		  }

		  // Create the spout using the configuration
		  protected EventHubSpout createEventHubSpout() {
		    EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
		    return eventHubSpout;
		  }

		  // Build the topology
		  protected StormTopology buildTopology(EventHubSpout eventHubSpout, SimpleHBaseMapper mapper) {
		    TopologyBuilder topologyBuilder = new TopologyBuilder();
		    // Name the spout 'EventHubsSpout', and set it to create
		    // as many as we have partition counts in the config file
		    topologyBuilder.setSpout("EventHub", eventHubSpout, spoutConfig.getPartitionCount())
		      .setNumTasks(spoutConfig.getPartitionCount());
		    // Create the parser bolt, which subscribes to the stream from EventHub
		    topologyBuilder.setBolt("Parser", new ParserBolt(), spoutConfig.getPartitionCount())
		      .localOrShuffleGrouping("EventHub").setNumTasks(spoutConfig.getPartitionCount());
		    // Create the dashboard bolt, which subscribes to the stream from Parser
		    topologyBuilder.setBolt("Dashboard", new DashboardBolt(), spoutConfig.getPartitionCount())
		      .fieldsGrouping("Parser", "dashstream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
		    // Create the HBase bolt, which subscribes to the stream from Parser
		    // WARNING - uncomment the following two lines when deploying
			// leave commented when testing locally
			// topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
		    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
		    return topologyBuilder.createTopology();
		  }

		  protected void submitTopology(String[] args, StormTopology topology, Config config) throws Exception {
		    // Config config = new Config();
		    config.setDebug(false);

		    //Enable metrics
		    config.registerMetricsConsumer(backtype.storm.metric.LoggingMetricsConsumer.class, 1);

		    // Is this running locally, or on an HDInsight cluster?
		    if (args != null && args.length > 0) {
		      config.setNumWorkers(numWorkers);
		      StormSubmitter.submitTopology(args[0], config, topology);
		    } else {
		      config.setMaxTaskParallelism(2);

		      LocalCluster localCluster = new LocalCluster();
		      localCluster.submitTopology("test", config, topology);

		      Thread.sleep(5000000);

		      localCluster.shutdown();
		    }
		  }

		  // Loads the configuration, creates the spout, builds the topology,
		  // and then submits it
		  protected void runScenario(String[] args) throws Exception{
		    readEHConfig(args);
		    Config config = new Config();

		    //hbase configuration
		    Map<String, Object> hbConf = new HashMap<String, Object>();
		    if(args.length > 0) {
		      hbConf.put("hbase.rootdir", args[0]);
		    }
		    config.put("hbase.conf", hbConf);
		    SimpleHBaseMapper mapper = new SimpleHBaseMapper()
		          .withRowKeyField("deviceid")
		          .withColumnFields(new Fields("timestamp", "temperature"))
		          .withColumnFamily("cf");

		    EventHubSpout eventHubSpout = createEventHubSpout();
		    StormTopology topology = buildTopology(eventHubSpout, mapper);
		    submitTopology(args, topology, config);
		  }

		  public static void main(String[] args) throws Exception {
		    Temperature scenario = new Temperature();
		    scenario.runScenario(args);
		  }
		}

	> [AZURE.NOTE] **HBaseBolt** の行はコメント アウトされています。これは、次の手順でトポロジをローカルで実行するためです。HBaseBolt は HBase と直接対話するため、有効になっているとエラーが返されます。DNS サーバーで仮想ネットワークを構成し、ローカル マシンを仮想ネットワークにも結合している場合は別です。

### ローカルでのトポロジのテスト

開発用コンピューターでファイルをコンパイルしてテストするには、次の手順を実行します。

1. Event Hub から何かを読み込むことができるように、**SendEvent** .NET アプリケーションを開始してイベントの送信を開始します。

2. Web ブラウザーを開き、Azure Web サイトに前にデプロイした Web ダッシュボードを表示します。これにより、値がトポロジを介して動くにつれて、グラフに表示されるのを確認できます。

3. 次のコマンドを使用してトポロジをローカルで開始します。

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	これでトポロジが開始され、Event Hub からファイルを読み取り、Azure Websites で実行されているダッシュボードにファイルを送信します。Web ダッシュボードに行が表示されます。

4. 動作することを確認したら、Ctrl キーを押しながら C キーを押してトポロジを停止します。SendEvent アプリケーションを停止するには、ウィンドウを選択して任意のキーを押します。

### HBaseBolt の有効化と HBase の準備

1. **Temperature.java** ファイルを開き、次の行からコメント (//) を削除します。

		//topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	//  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	これで、HBase ボルトが有効になります。

2. **Temperature.java** を保存します。

3. リモート デスクトップを使用して HBase クラスターに接続します。

4. デスクトップで HDInsight コマンド ラインを開始し、次のコマンドを入力します。

		cd %hbase_home%
		bin\hbase shell

5. HBase シェルから、次のコマンドを入力して、センサー データを格納するテーブルを作成します。

		create 'SensorData', 'cf'

6. 次のコマンドを入力して、テーブルにデータが格納されていないことを確認します。

		scan 'SensorData'

現時点では、HBase シェルでこのプロントを開いたままにします。

## HDInsight へのトポロジのパッケージ化とデプロイ

開発環境で、次の手順に従って Storm クラスターで Temperature トポロジを  実行します。

1. 次のコマンドを使用して、プロジェクトから JAR パッケージを作成します。

		mvn package

	これにより、プロジェクトの **target** ディレクトリに **TemperatureMonitor-1.0-SNAPSHOT.jar** という名前のファイルが作成されます。

2. イベントを読み込むことができるように、開発用のローカル コンピューターから **SendEvents** .NET アプリケーションを開始します。

3. リモート デスクトップを使用して HDInsight Storm クラスターに接続し、**TemperatureMonitor-1.0-SNAPSHOT.jar** ファイルを **c:\apps\dist\storm&lt;version number>** ディレクトリにコピーします。

4. クラスターのデスクトップにある **[HDInsight コマンド ライン]** アイコンを使用して新しいコマンド プロンプトを開き、次のコマンドを使用してトポロジを実行します。

		cd %storm_home%
		bin\storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature Temperature

5. トポロジが開始されてから項目が Web ダッシュボードに表示されるまでに、数秒かかる場合があります。

6. 項目がダッシュボードに表示されたら、HBase クラスターのリモート デスクトップ セッションに切り替えます。

7. HBase シェルから、次のコマンドを入力します。

		scan 'SensorData'

	これによって、Storm トポロジが書き込んだデータが数行返されます。

8. トポロジを停止するには、Storm クラスターのリモート デスクトップ セッションに移動し、HDInsight コマンド ラインに次のコードを入力します。

		bin\storm kill Temperature

	数秒でトポロジが停止します。

## まとめ

ここでは、Storm を使用して、Event Hub からデータを読み取り、HBase にデータを格納し、SignalR と D3.js を使用して Storm からの情報を外部ダッシュボードに表示する方法について説明しました。

* Apache Storm の詳細については、[https://storm.incubator.apache.org/](https://storm.incubator.apache.org/) を参照してください。

* HDInsight での HBase の詳細については、「[HDInsight HBase の概要](http://azure.microsoft.com/documentation/articles/hdinsight-hbase-overview/)」を参照してください。

* SignalR の詳細については、「[ASP.NET SignalR](http://signalr.net/)」を参照してください。

* D3.js の詳細については、「[D3.js - Data Driven Documents (D3.js - データ駆動ドキュメント)](http://d3js.org/)」を参照してください。

* .NET でのトポロジの作成の詳細については、「[HDInsight の Storm で SCP.NET と C# を使用したストリーミング データ処理アプリケーションの開発](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)」を参照してください。

[azure-portal]: https://manage.windowsazure.com/
<!--HONumber=42-->
