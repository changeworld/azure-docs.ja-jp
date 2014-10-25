<properties linkid="develop-mobile-tutorials-schedule-backend-tasks" urlDisplayName="Schedule Backend Tasks" pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# モバイル サービスでの繰り返し発生するジョブのスケジュール

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET バックエンド">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>

このトピックでは、管理ポータルのジョブ スケジューラ機能を使用して、定義したスケジュールに基づいて実行されるサーバー スクリプト コードを定義する方法について説明します。このスクリプトは、リモート サービス (ここでは Twitter) に対する確認を定期的に行い、結果を新しいテーブルに格納します。スケジュールできる定期的なタスクには、次のようなものがあります。

-   古いデータ レコードまたは重複しているデータ レコードの取得。
-   外部データ (ツイート、RSS エントリ、場所情報など) の要求と格納。
-   格納されている画像の処理とサイズ変更。


このチュートリアルでは、ジョブ スケジューラを使用してスケジュールされたジョブを作成する手順について以降に説明します。このジョブでは、Twitter からのツイート データを要求し、新しい Updates テーブルにツイートを格納します。

-   [Twitter アクセスを登録して資格情報を保存する][Twitter アクセスを登録して資格情報を保存する]
-   [新しい Updates テーブルを作成する][新しい Updates テーブルを作成する]
-   [新しいスケジュール済みジョブを作成する][新しいスケジュール済みジョブを作成する]

## <a name="get-oauth-credentials"></a>Twitter v1.1 API へのアクセスを登録して資格情報を保存する

[WACOM.INCLUDE [mobile-services-register-twitter-access][mobile-services-register-twitter-access]]

## <a name="create-table"></a>新しい Updates テーブルを作成する

次に、ツイートを格納するための新しいテーブルを作成する必要があります。

1.  管理ポータルで、モバイル サービスの **[データ]** タブをクリックし、**[作成]** をクリックします。

    ![][]

    **[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

2.  **[テーブル名]** に「*Updates*」と入力し、チェック ボタンをクリックします。

    ![][1]

    これにより、**Updates** という新しいストレージ テーブルが作成されます。

## <a name="add-job"></a>新しいスケジュール済みジョブを作成する

次に、Twitter にアクセスしてツイート データを新しい Updates テーブルに格納するための、スケジュールされたジョブを作成します。

1.  **[スケジューラ]** タブをクリックし、**[作成]** をクリックします。

    ![][2]

    > [WACOM.NOTE] モバイル サービスを*無料*レベルで運用している場合は、スケジュールされた複数のジョブを同時に実行することはできません。有料レベルでは、10 個までのスケジュールされたジョブを同時に実行できます。

2.  [スケジューラ] ダイアログ ボックスで、**[ジョブ名]** に「*getUpdates*」と入力し、スケジュールの間隔と単位を設定して、チェック ボタンをクリックします。

    ![][3]

    これにより、**getUpdates** という名前の新しいジョブが作成されます。

3.  作成した新しいジョブをクリックし、**[スクリプト]** をクリックします。

    ![][4]

4.  プレースホルダー関数である **getUpdates** を次のコードに置き換えます。

        var updatesTable = tables.getTable('Updates');
        var request = require('request');
        var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret. 
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.    
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

        function getUpdates() {   
            // Check what is the last tweet we stored when the job last ran
            // and ask Twitter to only give us more recent tweets
            appendLastTweetId(
                twitterUrl, 
                function twitterUrlReady(url){            
                    // Create a new request with OAuth credentials.
                    request.get({
                        url: url,                
                        oauth: {
                            consumer_key: consumerKey,
                            consumer_secret: consumerSecret,
                            token: accessToken,
                            token_secret: accessTokenSecret
                        }},
                        function (error, response, body) {
                        if (!error && response.statusCode == 200) {
                            var results = JSON.parse(body).statuses;
                            if(results){
                                console.log('Fetched ' + results.length + ' new results from Twitter');                       
                                results.forEach(function (tweet){
                                    if(!filterOutTweet(tweet)){
                                        var update = {
                                            twitterId: tweet.id,
                                            text: tweet.text,
                                            author: tweet.user.screen_name,
                                            date: tweet.created_at
                                        };
                                        updatesTable.insert(update);
                                    }
                                });
                            }            
                        } else { 
                            console.error('Could not contact Twitter');
                        }
                    });

                });
         }
        // Find the largest (most recent) tweet ID we have already stored
        // (if we have stored any) and ask Twitter to only return more
        // recent ones
        function appendLastTweetId(url, callback){
            updatesTable
            .orderByDescending('twitterId')
            .read({success: function readUpdates(updates){
                if(updates.length){
                    callback(url + '&since_id=' + (updates[0].twitterId + 1));           
                } else {
                    callback(url);
                }
            }});
        }

        function filterOutTweet(tweet){
            // Remove retweets and replies
            return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
        }

    このスクリプトでは、保存された資格情報を使用して Twitter クエリ API を呼び出し、`#mobileservices` というハッシュタグが含まれる最近のツイートを要求します。テーブルに格納される前に、重複しているツイートやリプライが結果から削除されます。

    > [WACOM.NOTE] このサンプルでは、スケジュールされた実行が行われるたびに、テーブルに数行のみ挿入されることを想定しています。ループで多数の行が挿入される場合には、無料レベルで実行すると、接続数を使い果たす可能性があります。このような場合、挿入をバッチで実行する必要があります。詳細については、「[方法: 一括挿入を実行する][方法: 一括挿入を実行する]」を参照してください。

5.  スクリプトをテストするには、**[一度だけ実行する]** をクリックします。

    ![][5]

    これにより、ジョブが保存され、実行されます。ただし、スケジューラ内では無効の状態のままです。

6.  [戻る] ボタンをクリックし、**[データ]**、**Updates** テーブル、**[参照]** の順にクリックして、Twitter データがテーブルに挿入されたことを確認します。

    ![][6]

7.  [戻る] ボタン、**[スケジューラ]** の順にクリックし、**getUpdates** を選択して、**[有効化]** をクリックします。

    ![][7]

    これにより、指定されたスケジュール (ここでは 1 時間ごと) でジョブが実行されるようになります。

これで、スケジュールされた新しいジョブがモバイル サービスに作成されました。このジョブは、無効化または変更するまで、スケジュールに従って実行されます。

## <a name="nextsteps"> </a> 次のステップ

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]<br />
    サーバー スクリプトの登録および使用について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-schedule-recurring-tasks/ "JavaScript バックエンド"
  [Twitter アクセスを登録して資格情報を保存する]: #get-oauth-credentials
  [新しい Updates テーブルを作成する]: #create-table
  [新しいスケジュール済みジョブを作成する]: #add-job
  [mobile-services-register-twitter-access]: ../includes/mobile-services-register-twitter-access.md
  []: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
  [1]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
  [2]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
  [3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
  [4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
  [方法: 一括挿入を実行する]: /ja-jp/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts
  [5]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
  [6]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
  [7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
