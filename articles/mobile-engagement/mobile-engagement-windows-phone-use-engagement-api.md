<properties 
	pageTitle="Windows Phone Silverlight で Engagement API を使用する方法" 
	description="Windows Phone Silverlight で Engagement API を使用する方法"	
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede"
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/03/2016" 
	ms.author="piyushjo" />

#Windows Phone Silverlight で Engagement API を使用する方法

このドキュメントは、「[Windows Phone Silverlight アプリにモバイル サービスを統合する方法](mobile-engagement-windows-phone-integrate-engagement.md)」の追加ドキュメントです。エンゲージメント API を使用してアプリケーションの統計情報を報告する方法についての詳細を提供しています。

エンゲージメントでアプリケーションのセッション、アクティビティ、クラッシュ、技術情報についてのみ報告する場合は、すべての `PhoneApplicationPage` サブクラスを `EngagementPage` クラスから継承するのが最も簡単な方法です。

他の操作を実行する場合、たとえば、アプリケーションの特定のイベント、エラー、ジョブを報告する場合や、`EngagementPage` クラスに実装されているのとは別の方法でアプリケーションのアクティビティを報告する必要がある場合は、エンゲージメント API を使用する必要があります。

エンゲージメント API は `EngagementAgent` クラスによって提供されます。これらのメソッドには `EngagementAgent.Instance` からアクセスできます。

エージェントのモジュールが初期化されていない場合でも、API に対する各呼び出しが遅延されると、エージェントが使用可能な場合にもう一度実行されます。

##エンゲージメントの概念

次のパートでは、Windows Phone プラットフォーム向けのモバイル エンゲージメントの概念について説明します。

### `Session` と `Activity`

*アクティビティ*は通常、アプリケーションの 1 つのページに関連付けられています。つまり、*アクティビティ*はページが表示されるときに開始され、閉じるときに停止されます。これは、エンゲージメント SDK が `EngagementPage` クラスを使用して統合されるときの場合です。

ただし、*アクティビティ*はエンゲージメント API を使用して手動で制御することも可能です。これにより、このページの使用状況に関する詳しい情報を表示するために、いくつかのサブ部分で特定のページ (たとえばこのページ内でのダイアログの使用頻度と使用時間を知るために) を分割できます。

##アクティビティを報告する

### ユーザーが新しいアクティビティを開始する

#### リファレンス

			void StartActivity(string name, Dictionary<object, object> extras = null)

ユーザー アクティビティが変更されるたびに `StartActivity()` を呼び出す必要があります。この関数の最初の呼び出しで、新しいユーザー セッションが開始します。

> [AZURE.IMPORTANT] アプリケーションが終了すると SDK は EndActivity メソッドを自動的に呼び出します。したがって、EndActivity メソッドを呼び出すと現在のセッションが強制的に終了されるので、このメソッドを呼び出さないようにし、ユーザーのアクティビティが変わる際には常に StartActivity メソッドを呼び出すことを強くお勧めします。

#### 例

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### ユーザーが現在のアクティビティを終了する

#### リファレンス

			void EndActivity()

ユーザーが最後のアクティビティを終了する際には、`EndActivity()` を少なくとも 1 回呼び出す必要があります。これにより、ユーザーが現在休止状態にあり、セッション タイムアウトの期限が終了したタイミングでユーザー セッションを閉じる必要があることを Engagement SDK に通知します (セッション タイムアウトの期限が終了する前に `StartActivity()` を呼び出すと、そのセッションが再開されます)。

#### 例

			EngagementAgent.Instance.EndActivity();

##ジョブを報告する

### ジョブを開始する

#### リファレンス

			void StartJob(string name, Dictionary<object, object> extras = null)

ジョブを使用して、一定期間、特定のタスクを追跡できます。

#### 例

			// An upload begins...
			
			// Set the extras
			var extras = new Dictionary<object, object>();
			extras.Add("title", "avatar");
			extras.Add("type", "image");
			
			EngagementAgent.Instance.StartJob("uploadData", extras);

### ジョブを終了する

#### リファレンス

			void EndJob(string name)

ジョブによって追跡されるタスクが終了するとすぐに、ジョブ名を指定して、このジョブの EndJob メソッドを呼び出す必要があります。

#### 例

			// In the previous section, we started an upload tracking with a job
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##イベントを報告する

イベントには 3 種類あります。

-   スタンドアロン イベント
-   セッション イベント
-   ジョブ イベント

### スタンドアロン イベント

#### リファレンス

			void SendEvent(string name, Dictionary<object, object> extras = null)

スタンドアロン イベントは、セッションのコンテキストの外で発生します。

#### 例

			EngagementAgent.Instance.SendEvent("event", extra);

### セッション イベント

#### リファレンス

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

通常、セッション イベントは、セッション中にユーザーによって実行されるアクションの報告に使用されます。

#### 例

**データなし:**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**データあり:**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### ジョブ イベント

#### リファレンス

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

通常、ジョブ イベントは、ジョブ中にユーザーによって実行されるアクションの報告に使用されます。

#### 例

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##エラーの報告

エラーには 3 種類あります。

-   スタンドアロン エラー
-   セッション エラー
-   ジョブ エラー

### スタンドアロン エラー

#### リファレンス

			void SendError(string name, Dictionary<object, object> extras = null)

セッション エラーとは反対に、スタンドアロン エラーはセッションのコンテキストの外で発生します。

#### 例

			EngagementAgent.Instance.SendError("errorName", extras);

### セッション エラー

#### リファレンス

			void SendSessionError(string name, Dictionary<object, object> extras = null)

通常、セッション エラーは、セッション中にユーザーに影響するエラーの報告に使用されます。

#### 例

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### ジョブ エラー

#### リファレンス

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

エラーは、現在のユーザー セッションに関連付ける代わりに、実行中のジョブに関連付けることができます。

#### 例

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##クラッシュを報告する

エージェントは、クラッシュに対処する 2 つのメソッドを提供します。

### 例外を送信する

#### リファレンス

			void SendCrash(Exception e, bool terminateSession = false)

#### 例

呼び出すことで、いつでも例外を送信できます。

			EngagementAgent.Instance.SendCrash(aCatchedException);

オプションのパラメーターを使用して、クラッシュを送信するよりも、同時にエンゲージメント セッションを終了することも可能です。それには次のように呼び出します。

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

その場合、セッションとジョブは、クラッシュを送信した後にだけ閉じられます。

### 未処理の例外を送信する

#### リファレンス

			void SendCrash(ApplicationUnhandledExceptionEventArgs e)

また、エンゲージメントは未処理の例外を送信するメソッドも提供します。これは特に、silverlight UnhandledException イベント ハンドラー内で使用する場合に便利です。

このメソッドは、呼び出された後に、**常に**エンゲージメントのセッションとジョブを終了します。

#### 例

これを使用して、独自の UnhandledException ハンドラーを実装できます (特にエンゲージメントの自動クラッシュ報告を無効にしている場合)。たとえば、`App.xaml.cs` ファイルの `Application_UnhandledException` メソッド:

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

アプリケーションが廃棄済みになったら、App.xaml.cs ファイルから `EngagementAgent.Instance.OnActivated(e)` を `Application_Activated` メソッドに挿入してエンゲージメント エージェントをリセットする必要があります。

### 例

			// Inside your App.xaml.cs file
			
			// Code to execute when the application is activated (brought to foreground)
			// This code will not execute when the application is first launched
			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			  EngagementAgent.Instance.OnActivated(e);
			}

##デバイス Id

			String GetDeviceId()

このメソッドを呼び出すことで、エンゲージメント デバイス id を取得できます。

##Extras パラメーター

任意のデータをイベント、エラー、アクティビティ、ジョブにアタッチできます。これらのデータは、ディクショナリを使用して構造化できます。任意の型のキーと値を指定できます。

Extras データはシリアル化されるため、Extras に独自の型を挿入する場合、この型のデータ コントラクトを追加する必要があります。

### 例

新しい "Person" クラスを作成します。

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

次に、`Person` インスタンスを Extra に追加します。

			Person person = new Person("Engagement Haddock", 51);
			var extras = new Dictionary<object, object>();
			extras.Add("people", person);
			
			EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] その他の型のオブジェクトを配置する場合、ToString() メソッドは、人間が判読できる文字列を返すように実装されていることを確認します。

### 制限

#### 構成する

オブジェクト内の各キーは、次の正規表現と一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*$`

キーは、文字、数字、アンダー スコア (\_) が後に続く、少なくとも 1 つの文字で始まる必要があることを意味します。

#### サイズ

Extras はセルあたり **1024** 文字に制限されています。

##アプリケーションの情報を報告する

### リファレンス

			void SendAppInfo(Dictionary<object, object> appInfos)

SendAppInfo() 関数を使用して追跡情報 (またはその他のアプリケーション固有情報) を手動で報告できます。

これらの情報は段階的に送信される可能性があることにご注意ください。特定のキーの最新の値のみが特定のデバイスに保持されます。イベント Extras のように、Dictionary<object, object> を使用して情報をアタッチします。

### 例

			Dictionary<object, object> appInfo = new Dictionary<object, object>()
			{
			   {"subscription", "2013-12-07"},
			   {"premium", "true"}
			};
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### 制限

#### 構成する

オブジェクト内の各キーは、次の正規表現と一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*$`

キーは、文字、数字、アンダー スコア (\_) が後に続く、少なくとも 1 つの文字で始まる必要があることを意味します。

#### サイズ

アプリケーションの情報は、呼び出しあたり **1024** 文字に制限されています。

前の例では、サーバーに送信される JSON は 44 文字です。

			{"subscription":"2013-12-07","premium":"true"}
 
##ログの記録
###ログの有効化

IDE コンソールにテスト ログを生成するように SDK を構成できます。このテスト ログは既定では有効になっていません。これをカスタマイズするには、次の例のように `EngagementAgent.Instance.TestLogEnabled` プロパティを `EngagementTestLogLevel` 列挙型の使用可能な値の 1 つに更新します。

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();

<!---HONumber=AcomDC_0504_2016-->