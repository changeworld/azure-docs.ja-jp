<properties 
	pageTitle="HDInsight の Hadoop を使用した Twitter データの分析 | Azure" 
	description="HDInsight の Hadoop に格納されている Twitter データを Hive で分析し、特定の単語の使用頻度を調べる方法について説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/29/2015" 
	ms.author="jgao"/>

# HDInsight での Hadoop を使用した Twitter データの分析

ビッグ データの多くはソーシャル Web サイトからもたらされます。Twitter などのサイトが公開している API を介して収集したデータは、現在の動向を分析して把握するための有益な情報源となります。このチュートリアルでは、Twitter streaming API を使用して複数のツイートを取得します。さらに、HDInsight の Apache Hive を使用して、特定の単語を含むツイートを多く送信した Twitter ユーザーの一覧を取得します。

> [WACOM.NOTE] 同様のサンプルは、HDInsight のサンプル ギャラリーに追加されます。次の Channel 9 ビデオでは、サンプルを実行する方法を示します。

<center><iframe width="854" height="510" src="https://www.youtube.com/embed/7ePbHot1SN4" frameborder="0" allowfullscreen></iframe></center>

##この記事の内容

- [前提条件](#prerequisites)
- [Twitter Feed の取得](#feed)
- [HiveQL スクリプトの作成](#script)
- [Hive を使用したデータの処理](#process)
- [次のステップ](#nextsteps)

##<a id="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure PowerShell がインストールされ構成されたワークステーション**。手順については、[Azure PowerShell のインストールおよび構成][powershell-install]に関するページを参照してください。PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを  *RemoteSigned* に設定する必要があります。[Windows PowerShell スクリプトの実行][powershell-script]に関するページを参照してください。

	PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。

		Add-AzureAccount

	Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。

		Select-AzureSubscription <AzureSubscriptionName>



- **Azure HDInsight クラスター**。クラスターのプロビジョニングの手順については、[HDInsight での Hadoop の使用][hdinsight-get-started]または [HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページを参照してください。このチュートリアルでは、クラスター名は後で必要になります。

**HDInsight ストレージについて**

HDInsight はデータ ストレージとして Azure BLOB ストレージを使用します。  これは、 *WASB* または  *Azure Storage - Blob* と呼ばれます。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]に関するページを参照してください。 

HDInsight クラスターをプロビジョニングする際、HDFS と同様に、既定のファイル システムとして BLOB ストレージ コンテナーが指定されます。プロビジョニング プロセスを実行するときに、このコンテナーに加えて、同じ Azure ストレージ アカウントまたは別の Azure ストレージ アカウントに属する付加的なコンテナーを追加することもできます。付加的なストレージ アカウントを追加する方法の詳細については、[HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページを参照してください。 

> [AZURE.NOTE] このチュートリアルで使用する PowerShell スクリプトを簡単にするために、ファイルはすべて、*/tutorials/twitter* にある既定のファイル システム コンテナーに格納されています。既定で、このコンテナーの名前は、HDInsight クラスター名と同じです。別のコンテナーにファイルを保存する場合は、適宜スクリプトを調整してください。

WASB の構文は次のとおりです。

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] HDInsight クラスター バージョン 3.0 では、 *wasb://* 構文のみがサポートされます。旧バージョンの  *asv://* 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターではサポートされず、以降のバージョンでもサポートされません。

> WASB のパスは、仮想パスです。  詳細については、[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]に関するページを参照してください。 

既定のファイル システム コンテナーに格納されているファイルの場合、次の URI のどれを使用しても HDInsight からアクセスできます (例として tweets.txt を使用しています)。

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
	wasb:///tutorials/twitter/tweets.txt
	/tutorials/twitter/tweets.txt

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

	tutorials/twitter/tweets.txt

このチュートリアルで使用するファイルを次の表に示します。

<table border="1">
<tr><th>ファイル</th><th>説明</th></tr>
<tr><td>/tutorials/twitter/data/tweets.txt</td><td>Hive ジョブのソース データです。</td></tr>
<tr><td>/tutorials/twitter/output</td><td>Hive ジョブの出力フォルダーです。既定のファイルの Hive ジョブ出力ファイル名は <strong>000000_0 です</strong>。 </td></tr>
<tr><td>tutorials/twitter/twitter.hql</td><td>HiveQL スクリプト ファイルです。</td></tr>
<tr><td>/tutorials/twitter/jobstatus</td><td>Hadoop ジョブの状態です。</td></tr>
</table>

##<a id="feed"></a>Twitter Feed の取得

このチュートリアルでは、[Twitter streaming APIs][twitter-streaming-api] を使用します。使用する特定の Twitter steaming API は [statuses/filter][twitter-statuses-filter] です。

>[WACOM.NOTE] 10,000 のツイートを含むファイルと Hive スクリプト ファイルは (次のセクションで説明) は、パブリック BLOB コンテナーにアップロードされています。このセクションは、アップロードしたファイルを使用する場合は省略できます。 

[ツイート データ](https://dev.twitter.com/docs/platform-objects/tweets)は、複雑なネスト構造の JSON 形式で格納されます。従来のプログラミング言語を使用して多数のコード行を記述する代わりに、このネスト構造を Hive テーブルに変換し、SQL によく似た HiveQL という言語で照会するようにできます。 

Twitter は OAuth を使用して、API への承認されたアクセスを提供します。OAuth は、パスワードを共有せずに代理でアプリケーションが動作することをユーザーが承認できるようにする認証プロトコルです。詳細については、[oauth.net](http://oauth.net/)、または Hueniverse の便利な「[Beginner's Guide to OAuth (OAuth 初心者向けガイド)](http://hueniverse.com/oauth/)」で確認できます。

OAuth を使用するための最初の手順は、Twitter 開発者サイトで新しいアプリケーションを作成することです。

**Twitter アプリケーションを作成するには**

1. [https://apps.twitter.com/](https://apps.twitter.com/) にサインインします。Twitter アカウントを持っていない場合は、**[Sign up now]** リンクをクリックします。
2. **[Create New App]** をクリックします。
3. **[Name]**、**[Description]**、**[Website]** を入力します。Web サイト フィールドの URL を構成することができます。次のテーブルは使用する値のサンプルを示しています。

	<table border="1">
	<tr><th>フィールド</th><th>値</th></tr>
	<tr><td>名前</td><td>MyHDInsightApp</td></tr>
	<tr><td>説明</td><td>MyHDInsightApp</td></tr>
	<tr><td>Web サイト</td><td>http://www.myhdinsightapp.com</td></tr>
	</table>
4. **[Yes, I agree]** をオンにして、**[Create your Twitter application]** をクリックします。
5. **[Permissions]** タブをクリックします。既定のアクセス許可は**読み取り専用**です。このチュートリアルにはこれで十分です。 
6. **[Keys and Access Tokens]** タブをクリックします。
7. **[Create my access token]** をクリックします。
8. ページの右上隅にある **[Test OAuth]** をクリックします。
9. **コンシューマー キー**、**コンシューマー シークレット**、**アクセス トークン**、**アクセス トークン シークレット**を書き留めます。これらの値は後で必要になります。

このチュートリアルでは、PowerShell を使用して Web サービスを呼び出します。.NET の c# サンプルについては、「[HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析する][hdinsight-hbase-twitter-sentiment]」を参照してください。Web サービスを呼び出すその他の一般的なツールは [*Curl*][curl] です。Curl は[ここ][curl-download]からダウンロードできます。

>[AZURE.NOTE] Windows で curl コマンドを使用する場合、オプション値には一重引用符の代わりに二重引用符を使用します。

**ツイートを取得するには**

1. Windows PowerShell ISE を開きます (Windows 8 のスタート画面で、「**PowerShell_ISE**」と入力し、**[Windows PowerShell ISE]** をクリックします。[Windows 8 と Windows での Windows PowerShell の起動][powershell-start]に関するページを参照してください)。

2. 次のスクリプトをスクリプト ウィンドウにコピーします。

		#region - variables and constants
		$clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name
		
		# Enter the Oauth information for your Twitter application
		$oauth_consumer_key = "<TwitterAppConsumerKey>";
		$oauth_consumer_secret = "<TwitterAppConsumerSecret>";
		$oauth_token = "<TwitterAppAccessToken>";
		$oauth_token_secret = "<TwitterAppAccessTokenSecret>";
		
		$destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob. 
		
		$trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
		$track = [System.Uri]::EscapeDataString($trackString);
		$lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
		#endregion
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Add-AzureAccount
		#endregion
			
		#region - Create a block blob object for writting Tweets into Blob storage
		Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureHDInsightCluster -Name $clusterName
		$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
		$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
		
		Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow
		
		Write-Host "Create block blob object ..." -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		#end region
				
		# region - Format oauth strings	
		Write-Host "Format oauth strings ..." -ForegroundColor Green
		$oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
		$ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
		$oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();
				
		$signature = "POST&";
		$signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
		$signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&"); 
		$signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
		$signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
		$signature += [System.Uri]::EscapeDataString("track=" + $track);
				
		$signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);
				
		$hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
		$hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
		$oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));
				
		$oauth_authorization = 'OAuth ';
		$oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
		$oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
		$oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
		$oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
		$oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
		$oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
		$oauth_authorization += 'oauth_version="1.0"';
				
		$post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track); 
		#endregion 
					
		#region - Read tweets	
		Write-Host "Create HTTP web request ..." -ForegroundColor Green
		[System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
		$request.Method = "POST";
		$request.Headers.Add("Authorization", $oauth_authorization);
		$request.ContentType = "application/x-www-form-urlencoded";
		$body = $request.GetRequestStream();
				
		$body.write($post_body, 0, $post_body.length);
		$body.flush();
		$body.close();
		$response = $request.GetResponse() ;
				
		Write-Host "Start stream reading ..." -ForegroundColor Green
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
				
		$sReader = New-Object System.IO.StreamReader($response.GetResponseStream())
				
		$inrec = $sReader.ReadLine()
		$count = 0
		while (($inrec -ne $null) -and ($count -le $lineMax))
		{
			if ($inrec -ne "")
			{
				Write-Host "`n`t $count tweets received." -ForegroundColor Yellow
				
				$writeStream.WriteLine($inrec)
				$count ++
			}
				
			$inrec=$sReader.ReadLine()
		}
		#endregion
				
		#region - Write tweets to Blob storage
		Write-Host "Write to the destination blob ..." -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream) 
				
		$sReader.close()
		#endregion
		
		Write-Host "Completed!" -ForegroundColor Green

3. スクリプトに、最初の 5 ～ 8 個の変数を設定します。	

	<table border="1">
	<tr><th>変数</th><th>説明</th></tr>
	<tr><td>$clusterName</td><td>アプリケーションを実行する HDInsight クラスターの名前です。</td></tr><tr><td>$oauth_consumer_key</td><td>Twitter アプリケーションを作成したときに <strong>書き留めた</strong> Twitter アプリケーションのコンシューマー キーです。</td></tr>
	<tr><td>$oauth_consumer_secret</td><td>前に書き留めた <strong>Twitter アプリケーションの</strong> コンシューマー シークレットです。</td></tr>
	<tr><td>$oauth_token</td><td>前に書き留めた <strong>Twitter アプリケーションの</strong> アクセス トークンです。</td></tr>
	<tr><td>$oauth_token_secret</td><td>前に書き留めた <strong>Twitter アプリケーションの</strong> アクセス トークン シークレットです。</td></tr>	
	<tr><td>$destBlobName</td><td>出力 BLOB 名です。  既定値は <strong>/tutorials/twitter/data/tweets.txt です</strong>。既定値を変更する場合は、適宜 PowerShell スクリプトを更新する必要があります。</td></tr>
	<tr><td>$trackString</td><td>Web サービスはこれらのキーワードに関連するツイートを返します。  既定値は <strong>Azure, Cloud, HDInsight です</strong>。既定値を変更する場合は、PowerShell スクリプトを更新します。</td></tr>
	<tr><td>$lineMax</td><td>この値によってスクリプトが読み取るツイートの数が決まります。100 個のツイートを読み取るに約 3 分かかります。大きな数値を設定してもかまいませんが、ダウンロードに時間がかかります。</td></tr>

	</table>

5. **F5** キーを押して、スクリプトを実行します。問題が発生した場合は、回避策としてすべての行を選択し、**F8** キーを押します。
6. 出力の最後に "Complete!" と表示されます。エラー メッセージが赤色で表示されます。

検証手順として、Azure ストレージ エクスプローラーまたは Azure PowerShell を使用して Azure BLOB ストレージ上で出力ファイル **/tutorials/twitter/data/tweets.txt** を確認できます。  一覧表示するファイルのサンプル PowerShell スクリプトについては、[HDInsight での BLOB ストレージの使用]に関するページを参照してください。 



##<a id="script"></a>HiveQL スクリプトの作成

Azure PowerShell を使用して、複数の HiveQL ステートメントを一度に実行することも、HiveQL ステートメントをスクリプト ファイルにまとめることもできます。このチュートリアルでは、HiveQL スクリプトを作成します。スクリプト ファイルは、WASB にアップロードする必要があります。次のセクションでは、Azure PowerShell を使用してスクリプト ファイルを実行します。

>[WACOM.NOTE] Hive スクリプト ファイルと 10,000 のツイートが含まれているファイルは、パブリック BLOB コンテナーにアップロードされています。このセクションは、アップロードしたファイルを使用する場合は省略できます。

HiveQL スクリプトは、次の作業を実行します。

1. **tweets_raw テーブルを削除します** (テーブルが既に存在する場合)。
2. **tweets_raw Hive テーブルを作成します**。この一時的な Hive 構造テーブルには、さらに ETL 処理のデータが保持されます。  パーティションの詳細については、[Hive のチュートリアル][apache-hive-tutorial]に関するページを参照してください。  
3. **データの読み込み。**ソース フォルダー (/tutorials/twitter/data) からデータを読み込みます。JSon ネスト形式の大規模なツイート データセットが、一時的な Hive テーブル構造に変換されました。
3. **ツイート テーブルを削除します** (テーブルが既に存在する場合)。
4. **ツイート テーブルを作成します**。Hive を使用してツイート データセットを照会するには、別の ETL 処理を実行する必要があります。この ETL 処理は、"twitter_raw" テーブルに保存したデータのための詳細なテーブル スキーマを定義します。  
5. **上書きテーブルを挿入します**。この複雑な Hive スクリプトは、Hadoop クラスターによる一連の長い Map Reduce ジョブを開始します。  使用するデータセットおよびクラスターのサイズによっては、10 分ほどかかる場合があります。
6. **上書きディレクトリを挿入します**。クエリを実行し、データセットをファイルに出力します。このクエリは、"Azure" という単語が含まれるツイートを送信した Twitter ユーザーの一覧を返します。

**Hive スクリプトを作成して Azure にアップロードするには**

1. Windows PowerShell ISE を開きます。
2. 次のスクリプトをスクリプト ウィンドウにコピーします。

		#region - variables and constants
		$clusterName = "<HDInsightClusterName>" # Enter your HDInsight cluster name
		
		$sourceDataPath = "/tutorials/twitter/data"
		$outputPath = "/tutorials/twitter/output"
		$hqlScriptFile = "tutorials/twitter/twitter.hql"
				
		$hqlStatements = @"
		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
				
		DROP TABLE tweets_raw;
		CREATE EXTERNAL TABLE tweets_raw (
			json_response STRING
		) 
		STORED AS TEXTFILE LOCATION '$sourceDataPath';
				
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
				
		INSERT OVERWRITE DIRECTORY '$outputPath'
		SELECT name, screen_name, count(1) as cc 
			FROM tweets 
			WHERE text like "%Azure%" 
			GROUP BY name,screen_name 
			ORDER BY cc DESC LIMIT 10;
		"@
		#endregion		
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Add-AzureAccount
		#endregion
			
		#region - Create a block blob object for writting the Hive script file
		Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureHDInsightCluster -Name $clusterName
		$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
		$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
				
		Write-Host "Define the connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
				
		Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
				
		Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		$writeStream.Writeline($hqlStatements)
		#endregion
		
		#region - Write the Hive script file to Blob storage		
		Write-Host "Write to the destination blob ... " -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$hqlScriptBlob.UploadFromStream($memStream)
		#endregion
				
		Write-Host "Completed!" -ForegroundColor Green


4. スクリプトの最初の 2 個の変数を設定します。	

	<table border="1">
	<tr><th>変数</th><th>説明</th></tr>
	<tr><td>$clusterName</td><td>アプリケーションを実行する HDInsight クラスター名を入力します。</td></tr>
	<tr><td>$sourceDataPath</td><td>Hive クエリがデータを読み取る WASB の場所です。この変数を変更する必要はありません。</td></tr>
	<tr><td>$outputPath</td><td>Hive クエリが結果を出力する WASB の場所です。この変数を変更する必要はありません。</td></tr>
	<tr><td>$hqlScriptFile</td><td>HiveQL スクリプト ファイルの場所とファイル名です。この変数を変更する必要はありません。</td></tr>
	</table>

5. **F5** キーを押して、スクリプトを実行します。問題が発生した場合は、回避策としてすべての行を選択し、**F8** キーを押します。
6. 出力の最後に "Complete!" と表示されます。エラー メッセージが赤色で表示されます。

検証手順として、Azure ストレージ エクスプローラーまたは Azure PowerShell を使用して Azure BLOB ストレージ上で出力ファイル **/tutorials/twitter/twitter.hql** を確認できます。  一覧表示するファイルのサンプル PowerShell スクリプトについては、[HDInsight での BLOB ストレージの使用][hdinsight-storage-powershell]に関するページを参照してください。  


##<a name="process"></a> Hive を使用して Twitter データを処理する

すべての準備作業が完了しました。Hive スクリプトを呼び出して、結果を確認できます。

### Hive ジョブを送信する

次の PowerShell スクリプトを使用して Hive スクリプトを実行します。最初の変数を設定する必要があります。

>[WACOM.NOTE] 最後の 2 つのセクションで、アップロードしたツイートと HiveQL スクリプトを使用するには、$hqlScriptFile を "/tutorials/twitter/twitter.hql" に指定します。パブリック BLOB にアップロードしたツイートと HiveQL スクリプトを使用するには、$hqlScriptFile を "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql" に設定します。

	#region variables and constants
	$clusterName = "<HDInsightClusterName>"
	
	#use one of the following
	$hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
	$hqlScriptFile = "/tutorials/twitter/twitter.hql"

	$statusFolder = "/tutorials/twitter/jobstatus"
	#endregion
	
	#region - Invoke Hive
	Write-Host "Invoke Hive ... " -ForegroundColor Green
	Use-AzureHDInsightCluster $clusterName
	$response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable
	
	Write-Host "Display the standard error log ... " -ForegroundColor Green
	$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace '\s*$' -replace '.*\s'
	Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 
	#endregion

### 結果を確認する

次の PowerShell スクリプトを使用して Hive ジョブ出力を確認します。最初の 2 つの変数を設定する必要があります。

	#region variables and constants
	$clusterName = "<HDInsightClusterName>"
	
	$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
	#engregion
	
	#region - Create an azure storage context object
	Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
	$myCluster = Get-AzureHDInsightCluster -Name $clusterName
	$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
	$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
	Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
	Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	#endregion 
	
	#region - Download blob and display blob
	Write-Host "Download the blob ..." -ForegroundColor Green
	cd $HOME
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "Display the output ..." -ForegroundColor Green
	Write-Host "==================================" -ForegroundColor Green
	cat "./$blob"
	Write-Host "==================================" -ForegroundColor Green
	#end region

> [AZURE.NOTE] Hive テーブルでは \001 をフィールド区切り記号として使用します。区切り記号は出力には表示されません。 

分析結果が WASB に配置されると、Azure SQL Database/SQL Server へのデータのエクスポート、Power Query を使用してのデータの Excel へのエクスポート、または Hive ODBC ドライバーを使用してのアプリケーションのデータへの接続ができます。  詳細については、[HDInsight での Sqoop の使用][hdinsight-use-sqoop]、[HDInsight を使用したフライトの遅延データの分析][hdinsight-analyze-flight-delay-data]、[Power Query を使用した Excel から HDInsight への接続][hdinsight-power-query]、[Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][hdinsight-hive-odbc]に関するページを参照してください。

##<a id="nextsteps"></a>次のステップ

このチュートリアルでは、Azure 上で HDInsight を使用し、Twitter から収集したデータを照会、探索、分析するため、構造化されていない Json データセットを構造化された Hive テーブルへ変換する方法を学習しました。詳細については、次を参照してください。

- [HDInsight の概要][hdinsight-get-started]に関するページ
- [HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析する][hdinsight-hbase-twitter-sentiment]
- [HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-delay-data]に関するページ
- [Power Query を使用した Excel から HDInsight への接続][hdinsight-power-query]に関するページ
- [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][hdinsight-hive-odbc]に関するページ
- [HDInsight での Sqoop の使用][hdinsight-use-sqoop]に関するページ

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

<!--HONumber=42-->
