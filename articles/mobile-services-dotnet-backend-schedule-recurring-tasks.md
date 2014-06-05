<properties  pageTitle="スケジューラを使用したバックエンド タスクのスケジュール - モバイル サービス" metaKeywords="" description="Windows Azure モバイル サービス スケジューラを使用して、モバイル アプリケーション用のジョブをスケジュールします。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスでの繰り返し発生するジョブのスケジュール" authors=""  solutions="" writer="" manager="" editor=""  />


# モバイル サービスでの繰り返し発生するジョブのスケジュール

<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET バックエンド" class="current">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript バックエンド" >JavaScript バックエンド</a>
</div>
 
このトピックでは、管理ポータルのジョブ スケジューラ機能を使用して、定義したスケジュールに基づいて実行されるサーバー スクリプト コードを定義する方法について説明します。このスクリプトは、リモート サービス (ここでは Twitter) に対する確認を定期的に行い、結果を新しいテーブルに格納します。スケジュールできる定期的なタスクには、次のようなものがあります。

+ 古いデータ レコードまたは重複しているデータ レコードのアーカイブ。
+ 外部データ (ツイート、RSS エントリ、場所情報など) の要求と格納。
+ 格納されている画像の処理とサイズ変更。

このチュートリアルでは、ジョブ スケジューラを使用してスケジュールされたジョブを作成する手順について以降に説明します。このジョブでは、Twitter からのツイート データを要求し、新しい Updates テーブルにツイートを格納します。

+ [Twitter アクセスを登録して資格情報を保存する]
+ [LINQ to Twitter ライブラリをダウンロードしてインストールする]
+ [新しい Updates テーブルを作成する]
+ [新しいスケジュール済みジョブを作成する]
+ [スケジュールされたジョブをローカルでテストする]
+ [サービスを発行してジョブを登録する]

>[WACOM.NOTE]このチュートリアルでは、サード パーティの LINQ to Twitter ライブラリを使用して、Twitter v1.1 への OAuth 2.0 アクセスを簡単にします。API。このチュートリアルを完了するには、LINQ to Twitter の NuGet パッケージをダウンロードしてインストールする必要があります。詳細については、[LINQ to Twitter の CodePlex プロジェクトに関するページ]を参照してください。

##<a name="get-oauth-credentials"></a>Twitter v1.1 API へのアクセスを登録して資格情報を保存する

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

<ol start="7">
<li><p>Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクトに対応する web.config ファイルを開き、<strong>MS_TwitterConsumerKey</strong> と <strong>MS_TwitterConsumerSecret</strong> の各アプリケーション設定を見つけて、これらのキーの値を、ポータルで設定した Twitter のコンシューマー キーとコンシューマー シークレットの値に置換します。</p></li>

<li><p>同じセクションで、次の新しいアプリケーション設定を追加し、プレースホルダーを、ポータルのアプリケーション設定で設定した Twitter のアクセス トークンとアクセス トークン シークレットの値に置換します。</p>

<pre><code>&lt;add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" /&gt;
&lt;add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" /&gt;</code></pre>

<p>モバイル サービスをローカル コンピューター上で実行するときに、モバイル サービスはこれら保存されている設定を使用するため、スケジュールされたジョブを発行する前にテストすることができます。Azure 内で実行する場合は、モバイル サービスは代わりにポータル内で設定した値を使用し、これら プロジェクト内の設定を無視します。</p></li>
</ol>

##<a name="install-linq2twitter"></a>LINQ to Twitter ライブラリをダウンロードしてインストールする

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 左側のウィンドウで **[オンライン]** カテゴリを選択し、``linq2twitter`` を見つけて **linqtotwitter** パッケージに対応する **[インストール]** をクリックし、使用許諾契約を読んで同意します。

  	![][1]

  	これにより、Linq to Twitter ライブラリがモバイル サービス プロジェクトに追加されます。

次に、ツイートを格納するための新しいテーブルを作成する必要があります。

##<a name="create-table"></a>新しい Updates テーブルを作成する

1. Visual Studio の [ソリューション エクスプローラーで、DataObjects フォルダーを右クリックし、**[追加]** をクリックします。**[クラス]** をクリックし、**[名前]** として「`Updates`」と入力してから **[追加]** をクリックします。

	これにより、Updates クラスに対応する新しいプロジェクト ファイルが作成されます。

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

4. [Models] フォルダーを展開し、データ モデルのコンテキスト ファイル (<em>service_name</em>Context.cs という名前) を開き、**DbSet** という型指定された値を返す、次のプロパティを追加します。

		public DbSet<Updates> Updates { get; set; }

	DbSet に最初にアクセスしたときにデータベース内に作成される Updates テーブルは、ツイート データを格納する目的でサービスによって使用されます。

	>[WACOM.NOTE] データベースの既定の初期化子を使用する場合は、Code First のモデル定義内でのデータ モデルの変更が検出されるたびに、Entity Framework がデータベースを削除して再作成します。このようなデータ モデルの変更を行ってデータベース内で既存のデータを保持するには、Code First Migrations を使用する必要があります。Azure 内の SQL データベースに対して、既定の初期化子を使用することはできません。詳細については、[Code First Migrations を使用してデータ モデルを更新する方法に関するページ](/ja-jp/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations)を参照してください。

次に、Twitter にアクセスしてツイート データを新しい Updates テーブルに格納することを目的とした、スケジュールされたジョブを作成します。

##<a name="add-job"></a>新しいスケジュール済みジョブを作成する

1. ScheduledJobs フォルダーを展開し、SampleJob.cs プロジェクト ファイルを開きます。

	**ScheduledJob** から継承されたこのクラスは、スケジュール可能なジョブを表し、そのジョブは Azure 管理ポータルで定期的なスケジュールで、またはオンデマンドで実行されます。

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
		
		        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, CancellationToken cancellationToken)
		        {
		            base.Initialize(scheduledJobDescriptor, cancellationToken);
		
		            // Create a new context with the supplied schema name.
		            context = new todolistContext(Services.Settings.Name);
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

	上記のコードで、_todolistService_ と _todolistContext_ の各文字列を、ダウンロードしたプロジェクトの名前空間と DbContext に置き換える必要があります。これらはそれぞれ、<em>mobile&#95;service&#95;name</em>Service と <em>mobile&#95;service&#95;name</em>Context です。
   	
	上記のコードで、**ExecuteAsync** オーバーライド メソッドは保存された資格情報を使用して Twitter のクエリ API を呼び出し、`#mobileservices` というハッシュタグを含む最近のツイートを要求します。テーブルに格納される前に、重複しているツイートやリプライが結果から削除されます。

##<a name="run-job-locally"></a>スケジュールされたジョブをローカルでテストする

スケジュールされたジョブを Azure に発行してポータルに登録する前に、ローカルでテストすることができます。

1. Visual Studio で、モバイル サービス プロジェクトがスタートアップ プロジェクトとして設定された状態で、F5 キーを押します。

	これにより、モバイル サービス プロジェクトが開始され、開始ページで新しいブラウザー ウィンドウが表示されます。

2. 開いているブラウザー ウィンドウからモバイル サービスの URL をコピーし、`tables/samplejob` パスをその URL に追加してから、Fiddler などの HTTP ユーティリティを使用して、この URL に対して新しい POST 要求を実行します。

	**ExecuteAsync* *メソッドが、ローカル コンピューターで開始されます。

3. サーバー エクスプローラーで、**[データ接続]**、**MSTableConnectionString**、**[テーブル]** の順に展開し、**[Udates]** を右クリックしてから **[テーブルのデータの表示]** をクリックします。

	この新しいツイートは、データ テーブル内の行として入力されます。

##<a name="register-job"></a>サービスを発行して新しいジョブを登録する

このジョブを **スケジューラ** タブ内に登録する必要があります。その結果、定義したスケジュールに従って、モバイル サービスがこのジョブを実行できるようになります。

3. モバイル サービスを Azure に対して再発行します。

4. [Azure 管理ポータル]で、[モバイル サービス] をクリックして、アプリケーションをクリックします。
 
	![][2]

2. **[スケジューラ]** タブをクリックし、**[作成]** をクリックします。

   	![][3]

    >[WACOM.NOTE]モバイル サービスを<em>無料</em>レベルで運用している場合は、スケジュールされた複数のジョブを同時に実行することはできません。有料レベルでは、10 個までのスケジュールされたジョブを同時に実行できます。

3. [スケジューラ] ダイアログ ボックスで、**[ジョブ名]** に「_SampleJob_」と入力し、スケジュールの間隔と単位を設定して、チェック ボタンをクリックします。
   
   	![][4]

   	これにより、**SampleJob** という名前の新しいジョブが作成されます。

4. 作成した新しいジョブをクリックし、**[一度だけ実行する]** をクリックしてスクリプトをテストします。

  	![][5]

   	これにより、ジョブが実行されます。ただし、スケジューラ内では無効の状態のままです。このページから、いつでもジョブを有効にし、スケジュールを変更することができます。

	>[WACOM.NOTE]引き続き POST 要求を使用して、スケジュールされたジョブを開始することができます。ただし、認証の既定はユーザーを対象にしており、要求のヘッダーにアプリケーション キーを含める必要があることを意味します。

4. (省略可能) [Azure 管理ポータル]で、モバイル サービスに関連付けられたデータベースの [管理] をクリックします。

    ![][6]

5. 管理ポータルでクエリを実行して、アプリケーションによって加えられた変更を表示します。クエリは次のようになりますが、スキーマ名として `todolist` の代わりにモバイル サービスの名前を使用します。

        SELECT * FROM [todolist].[Updates]

これで、スケジュールされた新しいジョブがモバイル サービスに作成されました。このジョブは、無効化または変更するまで、スケジュールに従って実行されます。

<!-- Anchors. -->
[Twitter アクセスを登録して資格情報を保存する]: #get-oauth-credentials
[LINQ to Twitter ライブラリをダウンロードしてインストールする]: #install-linq2twitter
[新しい Updates テーブルを作成する]: #create-table
[新しいスケジュール済みジョブを作成する]: #add-job
[スケジュールされたジョブをローカルでテストする]: #run-job-locally
[サービスを発行してジョブを登録する]: #register-job
[次のステップ]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png

<!-- URLs. -->
[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービスでの Twitter ログイン用のアプリケーションの登録]: /ja-jp/documentation/articles/mobile-services-how-to-register-twitter-authentication
[Twitter デベロッパー]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[アプリケーション設定]: http://msdn.microsoft.com/ja-jp/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[LINQ to Twitter の CodePlex プロジェクトに関するページ]: http://linqtotwitter.codeplex.com/

