<properties
	pageTitle="HDInsight の Apache Hive を使用した Twitter データの分析 | Microsoft Azure"
	description="Python を使用して特定のキーワードを含むツイートを格納してから、HDInsight で Hive と Hadoop を使用して未加工の Twitter データを検索可能な Hive テーブルに変換する方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>

# HDInsight での Hive を使用した Twitter データの分析

このドキュメントでは、Twitter streaming API を使用してツイートを取得し、Linux ベースの HDInsight クラスターで Apache Hive を使用して、JSON 形式のデータを処理します。結果として、特定の単語が含まれた最も多くのツイートを送信した Twitter ユーザーのリストが返されます。

> [AZURE.NOTE] このドキュメントの各部分は Windows ベースの HDInsight クラスター (Python など) で使用できますが、多くの手順は Linux ベースの HDInsight クラスターの使用に基づいています。Windows ベースのクラスターに固有の手順については、「[HDInsight での Hive を使用した Twitter データの分析](hdinsight-analyze-twitter-data.md)」をご覧ください。

###前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- __Linux ベースの Azure HDInsight クラスター__。クラスターの作成方法については、「[Hadoop チュートリアル: Linux 上の HDInsight で Hive と Hadoop を使用する (プレビュー)](hdinsight-hadoop-linux-tutorial-get-started.md)」をご覧ください。

- __SSH クライアント__。Linux ベースの HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ と [pip](https://pypi.python.org/pypi/pip)。

##Twitter Feed の取得

Twitter では、REST API を使用して、JavaScript Object Notation (JSON) ドキュメントとして[各ツイートのデータ](https://dev.twitter.com/docs/platform-objects/tweets)を取得できます。API の認証に [OAuth](http://oauth.net) が必要です。また、API へのアクセスに使用する設定を含む _Twitter アプリケーション_を作成する必要があります。

###Twitter アプリケーションを作成する

1. Web ブラウザーから [https://apps.twitter.com/](https://apps.twitter.com/) にサインインします。Twitter アカウントを持っていない場合は、**[今すぐ登録]** リンクをクリックします。
2. **[Create New App]** をクリックします。
3. **名前**、**説明**、**Web サイト**を入力します。**[Website]** フィールドの URL を構成することができます。次のテーブルは使用する値のサンプルを示しています。

	| フィールド | 値 |
	|:----- |:----- |
	| 名前 | MyHDInsightApp |
	| 説明 | MyHDInsightApp |
	| Web サイト | http://www.myhdinsightapp.com |
	
4. **[Yes, I agree]** をオンにして、**[Create your Twitter application]** をクリックします。
5. **[Permissions]** タブをクリックします。既定のアクセス許可は**読み取り専用**です。このチュートリアルにはこれで十分です。
6. **[Keys and Access Tokens]** タブをクリックします。
7. **[Create my access token]** をクリックします。
8. ページの右上隅にある **[Test OAuth]** をクリックします。
9. **コンシューマー キー**、**コンシューマー シークレット**、**アクセス トークン**、**アクセス トークン シークレット**を書き留めます。これらの値は後で必要になります。

>[AZURE.NOTE] Windows で curl コマンドを使用する場合、オプション値には一重引用符の代わりに二重引用符を使用します。

###ツイートをダウンロードする

次の Python コードは、Twitter から 10,000 個のツイートをダウンロードし、__tweets.txt__ という名前のファイルに保存します。

> [AZURE.NOTE] Python が既にインストールされているので、次の手順は HDInsight クラスターで実行します。

1. SSH を使用して HDInsight クラスターに接続します。

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	SSH ユーザー アカウントを保護するためにパスワードを使用している場合は、パスワードの入力を求められます。公開キーを使用している場合、`-i` パラメーターを使用して、対応する秘密キーを指定することが必要な場合があります。たとえば、「`ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`」のように入力します。
		
	Linux ベースの HDInsight での SSH の使用方法の詳細については、次の記事を参照してください。
	
	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows)
	
2. 既定では、__pip__ ユーティリティは HDInsight ヘッド ノードにインストールされません。次のコマンドを使用して、このユーティリティをインストールし、更新します。

		sudo apt-get install python-pip
		sudo pip install --upgrade pip

3. ツイートをダウンロードするコードでは、[Tweepy](http://www.tweepy.org/) と [Progressbar](https://pypi.python.org/pypi/progressbar/2.2) を使用します。これらをインストールするには、次のコマンドを使用します。

		sudo apt-get install python-dev libffi-dev libssl-dev
		sudo apt-get remove python-openssl
		sudo pip install tweepy progressbar pyOpenSSL requests[security]
		
	> [AZURE.NOTE] python-openssl の削除、python-dev、libffi-dev、libssl-dev、および pyOpenSSL のインストール、requests[security] に関する注意事項は、Python から SSL 経由で Twitter に接続するときに InsecurePlatform 警告を回避することです。
	>
	> ツイートを処理するときに発生する可能性のある[エラー](https://github.com/tweepy/tweepy/issues/576)を回避するために、Tweepy v3.2.0 が使用されます。

4. 次のコマンドを使用して、__gettweets.py__ という名前の新しいファイルを作成します。

		nano gettweets.py

5. __gettweets.py__ ファイルの内容として、次のコードを使用します。__consumer/_secret__、__consumer/_key_\_、__access/_token__、__access/_token/_secret__ のプレースホルダー情報を、Twitter アプリケーションの情報に置き換えます。

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. __Ctrl + X__ キーを押した後、__Y__ キーを押してファイルを保存します。

7. 次のコマンドを使用してファイルを実行し、ツイートをダウンロードします。

		python gettweets.py

	進行状況のインジケーターが表示され、ツイートのダウンロードとファイルへの保存の進行状況が 100% までカウントされます。

    > [AZURE.NOTE] 進行状況を示すバーが進むのに非常に時間がかかる場合は、トレンディング トピックを追跡するようにフィルターを変更する必要があります。フィルターしているトピックに関するツイートが多数存在すれば、必要な 10000 ツイートを直ちに取得できます。

###データのアップロード

WASB (HDInsight で使用される分散ファイル システム) にデータをアップロードするには、次のコマンドを使用します。

	hdfs dfs -mkdir -p /tutorials/twitter/data
	hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

クラスター内のすべてのノードがアクセスできる場所にデータが保存されます。

##HiveQL ジョブの実行


1. 次のコマンドを使用して、HiveQL ステートメントを含むファイルを作成します。

		nano twitter.hql
	
	このファイルの内容として、次のコードを使用します。

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response,	'$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
		
		
3. __Ctrl + X__ キーを押した後、__Y__ キーを押してファイルを保存します。

4. 次のコマンドを使用して、ファイルに含まれている HiveQL を実行します。

		beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql		
		
	Hive シェルが読み込まれ、__twitter.hql__ ファイル内の HiveQL が実行され、最後に `jdbc:hive2//localhost:10001/>` プロンプトが返されます。
	
5. Beeline プロンプトで、次のステートメントを使用して、__twitter.hql__ ファイル内の HiveQL によって作成された __tweets__ テーブルからデータを選択できることを確認します。
		
		SELECT name, screen_name, count(1) as cc
			FROM tweets
			WHERE text like "%Azure%"
			GROUP BY name,screen_name
			ORDER BY cc DESC LIMIT 10;

	メッセージ テキストに __Azure__ という単語が含まれた最大 10 個のツイートが返されます。

##次のステップ

このチュートリアルでは、Azure 上で HDInsight を使用し、Twitter から収集したデータを照会、探索、分析するため、構造化されていない JSON データセットを構造化された Hive テーブルへ変換する方法を学習しました。詳細については、次を参照してください。

- [HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md)
- [HDInsight を使用したフライト遅延データの分析](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

<!---HONumber=AcomDC_0713_2016-->