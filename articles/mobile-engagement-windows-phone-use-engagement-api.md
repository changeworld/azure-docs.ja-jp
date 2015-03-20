<properties 
	pageTitle="Azure モバイル エンゲージメント Windows Phone SDK 統合" 
	description="Windows Phone にエンゲージメント API を使用する方法" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" /> 

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kapiteir" />

#Windows Phone にエンゲージメント API を使用する方法

このドキュメントは、[Windows Phone アプリにモバイル サービスを統合する方法](../mobile-engagement-windows-phone-integrate-engagement/) の追加ドキュメントです。エンゲージメント API を使用してアプリケーションの統計情報を報告する方法についての詳細を提供しています。

エンゲージメントでアプリケーションのセッション、アクティビティ、クラッシュ、技術情報についてのみ報告する場合は、すべての  `PhoneApplicationPage` サブクラスを  `EngagementPage` クラスから継承するのが最も簡単な方法です。

さらに詳細に報告する場合、たとえば、アプリケーションに特有のイベント、エラーやジョブを報告する必要がある場合、または  `EngagementPage` クラスに実装されている方法とは別の方法でアプリケーションのアクティビティを報告する必要がある場合は、エンゲージメント API を使用する必要があります。

エンゲージメント API は  `EngagementAgent` クラスが提供します。これらのメソッドには  `EngagementAgent.Instance` からアクセスできます。

エージェントのモジュールが初期化されていない場合でも、API に対する各呼び出しは保留され、エージェントが使用可能になったときにもう一度実行されます。

##エンゲージメントの概念

次のパートでは、Windows Phone プラットフォーム向けのモバイル エンゲージメントの概念について説明します。

### 'セッション' と  `Activity`

 *アクティビティ* は通常、 アプリケーションの 1 つのページに関連付けられます。つまり、 *アクティビティ* はページが表示されたときに開始され、ページが終了したときに停止します: これは  `EngagementPage` クラスを使用してエンゲージメント SDK が統合されている場合です。

ただし、 *アクティビティ* は、エンゲージメント API を使用して手動で制御することもできます。れにより、特定のページをいくつかのサブ パーツに分割し、このページの使用状況についてより詳細に知ることができます (このページ内でダイアログがどのくらいの頻度で、またどのくらいの期間使用されているかについてなど)。

##アクティビティの報告

### ユーザーが、新しいアクティビティを開始します

#### リファレンス

			void StartActivity(string name, Dictionary<object, object> extras = null)

ユーザーがアクティビティを変更するたびに、 `StartActivity()` を呼び出す必要があります。この関数の最初の呼び出しによって最初のユーザー セッションが開始されます。

> [AZURE.IMPORTANT] アプリケーションが終了すると Windows Phone SDK は EndActivity メソッドを自動的に呼び出します。したがって、EndActivity メソッドを呼び出すと現在のセッションが強制的に終了されるので、このメソッドを呼び出さないようにし、ユーザーのアクティビティが変わる際には常に StartActivity メソッドを呼び出すことを強くお勧めします。

#### 例

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### ユーザーが、現在のアクティビティを終了

#### リファレンス

			void EndActivity()

これによってアクティビティとセッションが終了します。理解したうえでない限り、このメソッドを呼び出さないでください。

#### 例

			EngagementAgent.Instance.EndActivity();

##ジョブの報告

### ジョブの開始

#### リファレンス

			void StartJob(string name, Dictionary<object, object> extras = null)

ジョブを使用して、一定の期間にわたって特定のタスクを追跡できます。

#### 例

			// An upload begins...
			
			// Set the extras
			var extras = new Dictionary<object, object>();
			extras.Add("title", "avatar");
			extras.Add("type", "image");
			
			EngagementAgent.Instance.StartJob("uploadData", extras);

### ジョブの終了

#### リファレンス

			void EndJob(string name)

ジョブによって追跡されるタスクが終了したらすぐに、ジョブ名を指定して、このジョブの EndJob メソッドを呼び出す必要があります。

#### 例

			// In the previous section, we started an upload tracking with a job
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##イベントの報告

次の 3 つの種類のイベントがあります:

-   スタンドアロン イベント
-   セッション イベント
-   ジョブ イベント

### スタンドアロン イベント

#### リファレンス

			void SendEvent(string name, Dictionary<object, object> extras = null)

スタンドアロン イベントは、セッションのコンテキストの外部で発生します。

#### 例

			EngagementAgent.Instance.SendEvent("event", extra);

### セッション イベント

#### リファレンス

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

セッション イベントは、通常、セッション中にユーザーが実行したアクションを報告するために使用されます。

#### 例

**データなし :**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**With data :**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### ジョブ イベント

#### リファレンス

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

ジョブ イベントは、通常、ジョブ中にユーザーが実行したアクションを報告するために使用されます。

#### 例

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##エラーの報告

次の 3 つの種類のエラーがあります:

-   スタンドアロン エラー
-   セッション エラー
-   ジョブ エラー

### スタンドアロン エラー

#### リファレンス

			void SendError(string name, Dictionary<object, object> extras = null)

セッション エラーと異なり、スタンドアロン エラーはセッションのコンテキストの外部で発生する場合があります。

#### 例

			EngagementAgent.Instance.SendError("errorName", extras);

### セッション エラー

#### リファレンス

			void SendSessionError(string name, Dictionary<object, object> extras = null)

セッションのエラーは、通常、セッション中にユーザーに影響を与えるエラーを報告するのに使用されます。

#### 例

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### ジョブ エラー

#### リファレンス

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

エラーは、現在のユーザー セッションに関連しているのではなく、実行中のジョブに関連している場合があります。

#### 例

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##クラッシュの報告

エージェントは、クラッシュに対処する 2 つのメソッドを提供します。

### 例外の送信

#### リファレンス

			void SendCrash(Exception e, bool terminateSession = false)

#### 例

呼び出すことによって、いつでも例外を送信できます:

			EngagementAgent.Instance.SendCrash(aCatchedException);

オプションのパラメーターを使用することにより、クラッシュを送信して、同時にエンゲージメントを終了ができます。これを実行するには次のように呼び出します:

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

これを実行すると、セッションとジョブはクラッシュを送信した直後に終了します。

### 未処理の例外の送信

#### リファレンス

			void SendCrash(ApplicationUnhandledExceptionEventArgs e)

また、エンゲージメントは未処理の例外を送信するメソッドも提供します。これは特に、silverlight UnhandledException イベント ハンドラー内で使用する場合に便利です。

このメソッドでは、**常に**呼び出された後にセッションとメソッドを終了します。

#### 例

これを使用して、独自の UnhandledException ハンドラーを実装できます (特にエンゲージメントの自動クラッシュ報告を無効にしている場合)。たとえば、 `App.xaml.cs` ファイルの  `Application_UnhandledException` メソッド:

			// In your App.xaml.cs file
			
			// Code to execute on Unhandled Exceptions
			private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
			{
			  // your own code
			
			  EngagementAgent.Instance.SendCrash(e);
			}

##OnActivated

### リファレンス

			void OnActivated(ActivatedEventArgs e)

ユーザーがアプリケーションから移動すると、Deactivated イベントが発生した後で、オペレーティング システムはアプリケーションを休止状態にしようとします。アプリケーションは廃棄済み状態になります。このプロセスでは、アプリケーションは終了しますが、アプリケーションの状態とアプリケーション内の個々のページに関する一部のデータは保持されます。

アプリケーションが廃棄済みになったら、App.xaml.cs ファイルから  `EngagementAgent.Instance.OnActivated(e)` を `Application_Activated` メソッドに挿入してエンゲージメント エージェントをリセットする必要があります。

### 例

			// Inside your App.xaml.cs file
			
			// Code to execute when the application is activated (brought to foreground)
			// This code will not execute when the application is first launched
			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			  EngagementAgent.Instance.OnActivated(e);
			}

##デバイス ID

			String GetDeviceId()

このメソッドを呼び出して、エンゲージメント デバイス ID を取得できます。

##Extras パラメーター

任意のデータをイベント、エラー、アクティビティ、ジョブにアタッチできます。これらのデータはディクショナリを使用して構造化ができます。キーと値は任意の型を指定できます。

Extras データはシリアル化されるため、Extras に独自の型を挿入する場合は、この型のデータ コントラクトを追加する必要があります。

### 例

新しいクラス "Person" を作成します。

			using System.Runtime.Serialization;
			
			namespace Engagement.Agent
			{
			  [DataContract]
			  public class Person
			  {
			    public Person(string name, int age)
			    {
			      Age = age;
			      Name = name;
			    }
			
			    // Properties
			
			    [DataMember]
			    public int Age
			    {
			      get;
			      set;
			    }
			
			    [DataMember]
			    public string Name
			    {
			      get;
			      set; 
			    }
			  }
			}

Extra に  `Person` インスタンスを追加します。

			Person person = new Person("Engagement Haddock", 51);
			var extras = new Dictionary<object, object>();
			extras.Add("people", person);
			
			EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] 他の型のオブジェクトを配置する場合は、ToString() メソッドが実装されて人間が判読できる文字列を返すことを確認します。

### 制限

#### 構成する

オブジェクト内の各キーは、次の正規表現と一致する必要があります:

`^[a-zA-Z][a-zA-Z_0-9]*$`

キーは少なくとも 1 つの文字で始まり、その後に文字、数字、アンダー スコア (\) が続く必要があります。

#### サイズ

Extras は、呼び出しごとに最大で、**1024** 文字に制限されています。

##アプリケーション情報の報告

### リファレンス

			void SendAppInfo(Dictionary<object, object> appInfos)

SendAppInfo() 関数を使用して、追跡情報を手動で報告できます (またはその他のアプリケーションに固有の情報)。 

これらの情報は段階的に送信できます: 指定されたデバイスの指定されたキーの最新の値のみが保持されます。イベントの extras のように、Dictionary\<object, object\> を使用して情報をアタッチができます。

### 例

			Dictionary<object, object> appInfo = new Dictionary<object, object>()
			{
			   {"subscription", "2013-12-07"},
			   {"premium", "true"}
			};
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### 制限

#### 構成する

オブジェクト内の各キーは、次の正規表現と一致する必要があります:

`^[a-zA-Z][a-zA-Z_0-9]*$`

キーは少なくとも 1 つの文字で始まり、その後に文字、数字、アンダー スコア (\) が続く必要があります。

#### サイズ

アプリケーション情報は、呼び出しごとに、最大で **1024** 文字に制限されています。

前の例では、サーバーに送信された JSON は 44 文字です。

			{"subscription":"2013-12-07","premium":"true"}

<!--HONumber=47-->
