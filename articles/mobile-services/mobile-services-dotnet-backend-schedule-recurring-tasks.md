<properties 
	pageTitle="Azure Mobile Services での繰り返し発生するジョブのスケジュール" 
	description="Azure Mobile Services Scheduler を使用して、モバイル アプリケーション用のジョブをスケジュールします。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="glenga"/>

# Mobile Services での繰り返し発生するジョブのスケジュール 

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [(Any | Javascript)](mobile-services-schedule-recurring-tasks.md)
 
このトピックでは、管理ポータルのジョブ スケジューラ機能を使用して、定義したスケジュールに基づいて実行されるサーバー スクリプト コードを定義する方法について説明します。このスクリプトは、リモート サービス (ここでは Twitter) に対する確認を定期的に行い、結果を新しいテーブルに格納します。スケジュールできる定期的なタスクには、次のようなものがあります。

+ 古いデータ レコードまたは重複しているデータ レコードの取得。
+ 外部データ (ツイート、RSS エントリ、場所情報など) の要求と格納。
+ 格納されている画像の処理とサイズ変更。

このチュートリアルでは、ジョブ スケジューラを使用してスケジュールされたジョブを作成する手順について説明します。このジョブでは、Twitter にツイート データを要求し、そのツイートを新しい Updates テーブルに格納します。

>[AZURE.NOTE]このチュートリアルでは、サード パーティの LINQ to Twitter ライブラリを使用して、Twitter v1.1 API への OAuth 2.0 アクセスを簡単にします。このチュートリアルを完了するには、LINQ to Twitter の NuGet パッケージをダウンロードしてインストールする必要があります。詳細については、[LINQ to Twitter の CodePlex プロジェクトに関するページ]を参照してください。

##<a name="get-oauth-credentials"></a>Twitter v1.1 API へのアクセスを登録して資格情報を保存する

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

&nbsp;&nbsp;7.Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクトの web.config ファイルを開き、`MS_TwitterConsumerKey` と `MS_TwitterConsumerSecret` の各アプリケーション設定を見つけて、これらのキーの値を、ポータルで設定した Twitter のコンシューマー キーとコンシューマー シークレットの値に置き換えます。

&nbsp;&nbsp;8.同じセクションで、次の新しいアプリケーション設定を追加し、プレースホルダーを、ポータルのアプリケーション設定で設定した Twitter のアクセス トークンとアクセス トークン シークレットの値に置換します。

	<add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
	<add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />

モバイル サービスをローカル コンピューター上で実行するときに、モバイル サービスはこれら保存されている設定を使用するため、スケジュールされたジョブを発行する前にテストすることができます。Azure 内で実行する場合は、モバイル サービスは代わりにポータル内で設定した値を使用し、これら プロジェクト内の設定を無視します。

##<a name="install-linq2twitter"></a>LINQ to Twitter ライブラリをダウンロードしてインストールする

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 左側のウィンドウで、**[オンライン]** カテゴリを選択し、`linq2twitter` を探します。**linqtotwitter** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

  	![][1]

  	これにより、Linq to Twitter ライブラリがモバイル サービス プロジェクトに追加されます。

次に、ツイートを格納するための新しいテーブルを作成する必要があります。

##<a name="create-table"></a>新しい Updates テーブルを作成する

1. Visual Studio のソリューション エクスプローラーで、DataObjects フォルダーを右クリックし、**[追加]** を展開します。**[クラス]** をクリックし、**[名前]** として「`Updates`」と入力してから **[追加]** をクリックします。

	これにより、Updates クラスに対応する新しいプロジェクト ファイルが作成されます。

2. **[参照]** を右クリックし、**[参照の追加]** をクリックして **[アセンブリ]** から **[Framework]** を選択します。**[System.ComponentModel.DataAnnotations]** をオンにし、**[OK]** をクリックします。

	![][7]

	これで新しいアセンブリの参照が追加されます。

2. この新しいクラスで、次の **using** ステートメントを追加します。
 
		using Microsoft.WindowsAzure.Mobile.Service;
		using System.ComponentModel.DataAnnotations;

3. **Updates** クラス定義を次のコードで置き換えます。

		public class Updates 
	    {
	        [Key]
	        public int UpdateId { get; set; }
	        public long TweetId { get; set; }
	        public string Text { get; set; }
	        public string Author { get; set; }
	        public DateTime Date { get; set; }
    	}

4. [モデル] フォルダーを展開し、データ モデルのコンテキスト ファイル (*service\_name*Context.cs という名前) を開き、**DbSet** という型指定された値を返す、次のプロパティを追加します。

		public DbSet<Updates> Updates { get; set; }

	DbSet に最初にアクセスしたときにデータベース内に作成される Updates テーブルは、ツイート データを格納する目的でサービスによって使用されます。

	>[AZURE.NOTE]データベースの既定の初期化子を使用する場合は、Code First のモデル定義内でのデータ モデルの変更が検出されるたびに、Entity Framework がデータベースを削除して再作成します。このようなデータ モデルの変更を行ってデータベース内で既存のデータを保持するには、Code First Migrations を使用する必要があります。Azure 内の SQL Database に対して、既定の初期化子を使用することはできません。詳細については、[Code First Migrations を使用してデータ モデルを更新する方法に関するページ](mobile-services-dotnet-backend-use-code-first-migrations.md)を参照してください。

次に、Twitter にアクセスしてツイート データを新しい Updates テーブルに格納することを目的とした、スケジュールされたジョブを作成します。

##<a name="add-job"></a>新しいスケジュール済みジョブを作成する  

1. ScheduledJobs フォルダーを展開し、SampleJob.cs プロジェクト ファイルを開きます。

	**ScheduledJob** から継承されたこのクラスは、スケジュール可能なジョブを表し、そのジョブは Azure の管理ポータルで定期的なスケジュールで、またはオンデマンドで実行されます。

2. SampleJob.cs の内容を次のコードに置き換えます。
 
		using System;
		using System.Linq;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Web.Http;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.ScheduledJobs;
		using LinqToTwitter;
		using todolistService.Models;
		using todolistService.DataObjects;
		
		namespace todolistService
		{
		    // A simple scheduled job which can be invoked manually by submitting an HTTP
		    // POST request to the path "/jobs/sample".
		    public class SampleJob : ScheduledJob
		    {
		        private todolistContext context;
		        private string accessToken;
		        private string accessTokenSecret;
		
		        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, 
					CancellationToken cancellationToken)
		        {
		            base.Initialize(scheduledJobDescriptor, cancellationToken);
		
		            // Create a new context with the supplied schema name.
		            context = new todolistContext();
		        }
		
		        public async override Task ExecuteAsync()
		        {            
		            // Try to get the stored Twitter access token from app settings.  
		            if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
		            Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
		            {
		                Services.Log.Error("Could not retrieve Twitter access credentials.");
		            }
		
		            // Create a new authorizer to access Twitter v1.1 APIs
		            // using single-user OAUth 2.0 credentials.
		            MvcAuthorizer auth = new MvcAuthorizer();
		            SingleUserInMemoryCredentialStore store = 
		                new SingleUserInMemoryCredentialStore()
		            {
		                ConsumerKey = Services.Settings.TwitterConsumerKey,
		                ConsumerSecret = Services.Settings.TwitterConsumerSecret,
		                OAuthToken = accessToken,
		                OAuthTokenSecret = accessTokenSecret
		            };
		
		            // Set the credentials for the authorizer.
		            auth.CredentialStore = store;
		
		            // Create a new LINQ to Twitter context.
		            TwitterContext twitter = new TwitterContext(auth);
		
		            // Get the ID of the most recent stored tweet.
		            long lastTweetId = 0;
		            if (context.Updates.Count() > 0)
		            {
		                lastTweetId = (from u in context.Updates
		                               orderby u.TweetId descending
		                               select u).Take(1).SingleOrDefault()
		                                            .TweetId;
		            }
		
		            // Execute a search that returns a filtered result.
		            var response = await (from s in twitter.Search
		                                  where s.Type == SearchType.Search
		                                  && s.Query == "%23mobileservices"
		                                  && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
		                                  && s.ResultType == ResultType.Recent
		                                  select s).SingleOrDefaultAsync();
		
		            // Remove retweets and replies and log the number of tweets.
		            var filteredTweets = response.Statuses
		                .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
		            Services.Log.Info("Fetched " + filteredTweets.Count()
		                + " new tweets from Twitter.");
		
		            // Store new tweets in the Updates table.
		            foreach (Status tweet in filteredTweets)
		            {
		                Updates newTweet =
		                    new Updates
		                    {
		                        TweetId = Convert.ToInt64(tweet.StatusID),
		                        Text = tweet.Text,
		                        Author = tweet.User.Name,
		                        Date = tweet.CreatedAt
		                    };
		
		                context.Updates.Add(newTweet);
		            }
		
		            await context.SaveChangesAsync();
		        }
		        protected override void Dispose(bool disposing)
		        {
		            base.Dispose(disposing);
		            if (disposing)
		            {
		                context.Dispose();
		            }
		        }
		    }
		}

	上記のコードで、文字列 _todolistService_ と _todolistContext_ を、ダウンロードしたプロジェクトの名前空間と DbContext (それぞれ *mobile&\#95;service&\#95;name*Service と *mobile&\#95;service&\#95;name*Context) に置き換える必要があります。
   	
	上記のコードで、**ExecuteAsync** オーバーライド メソッドは保存された資格情報を使用して Twitter のクエリ API を呼び出し、`#mobileservices` というハッシュタグを含む最近のツイートを要求します。テーブルに格納される前に、重複しているツイートやリプライが結果から削除されます。

##<a name="run-job-locally"></a>スケジュールされたジョブをローカルでテストする

スケジュールされたジョブを Azure に発行してポータルに登録する前に、ローカルでテストすることができます。

1. Visual Studio で、モバイル サービス プロジェクトがスタートアップ プロジェクトとして設定された状態で、F5 キーを押します。

	これにより、モバイル サービス プロジェクトが開始され、開始ページで新しいブラウザー ウィンドウが表示されます。

2. **[実際に使ってみる]** をクリックして、**[POST jobs/{jobName}]** をクリックします。

	![][8]
 
4. **[実際にこれを使ってみる]** をクリックして、「`Sample`」を **{jobName}** パラメーター値として入力し、**[送信]** をクリックします。

	![][9]

	これで新しい POST 要求がサンプル ジョブ エンドポイントに送信されます。ローカル サービスで **ExecuteAsync** メソッドが起動します。このメソッドにブレークポイントを設定してコードをデバッグできます。

3. サーバー エクスプローラーで、**[データ接続]**、**MSTableConnectionString**、**[テーブル]** の順に展開し、**[更新]** を右クリックしてから **[テーブルのデータの表示]** をクリックします。

	この新しいツイートは、データ テーブル内の行として入力されます。

##<a name="register-job"></a>サービスを発行して新しいジョブを登録する 

このジョブを **スケジューラ** タブ内に登録する必要があります。その結果、定義したスケジュールに従って、Mobile Services がこのジョブを実行できるようになります。

3. モバイル サービス プロジェクトを Azure に対して再発行します。 

4. [Azure の管理ポータル]で、[モバイル サービス] をクリックして、アプリケーションをクリックします。

2. **[スケジューラ]** タブをクリックし、**[作成]** をクリックします。

    >[AZURE.NOTE]モバイル サービスを<em> Free </em>レベルで運用している場合は、スケジュールされた複数のジョブを同時に実行することはできません。有料レベルでは、10 個までのスケジュールされたジョブを同時に実行できます。

3. [スケジューラ] ダイアログ ボックスで、**[ジョブ名]** に「_Sample_」と入力し、スケジュールの間隔と単位を設定して、チェック ボタンをクリックします。
   
   	![][4]

   	これにより、**Sample** という名前の新しいジョブが作成されます。

4. 作成した新しいジョブをクリックし、**[一度だけ実行する]** をクリックしてスクリプトをテストします。

   	これにより、ジョブが実行されます。ただし、スケジューラ内では無効の状態のままです。このページから、いつでもジョブを有効にし、スケジュールを変更することができます。

	>[AZURE.NOTE]引き続き POST 要求を使用して、スケジュールされたジョブを開始することができます。ただし、認証の既定はユーザーを対象にしており、要求のヘッダーにアプリケーション キーを含める必要があることを意味します。

4. (省略可能) [Azure の管理ポータル]で、モバイル サービスに関連付けられたデータベースの [管理] をクリックします。

    ![][6]

5. 管理ポータルでクエリを実行して、アプリケーションによって加えられた変更を表示します。クエリは次のようになりますが、スキーマ名として `todolist` の代わりにモバイル サービスの名前を使用します。

        SELECT * FROM [todolist].[Updates]

これで、スケジュールされた新しいジョブがモバイル サービスに作成されました。このジョブは、無効化または変更するまで、スケジュールに従って実行されます。

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Download and install the LINQ to Twitter library]: #install-linq2twitter
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Test the scheduled job locally]: #run-job-locally
[Publish the service and register the job]: #register-job
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
[7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
[8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
[9]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png

<!-- URLs. -->
[Azure の管理ポータル]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[LINQ to Twitter の CodePlex プロジェクトに関するページ]: http://linqtotwitter.codeplex.com/

<!---HONumber=August15_HO6-->