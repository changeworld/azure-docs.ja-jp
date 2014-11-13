<properties urlDisplayName="Analyze Twitter data with HDInsight Hadoop" pageTitle="HDInsight の Hadoop を使用した Twitter データの分析 | Azure" metaKeywords="" description="HDInsight の Hadoop に格納されている Twitter データを Hive で分析し、特定の単語の使用頻度を調べる方法について説明します。" metaCanonical="" services="HDInsight" documentationCenter="" title="HDInsight での Hadoop を使用した Twitter データの分析" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight での Hadoop を使用した Twitter データの分析

HDInsight で Hive を使用して、Hadoop で Twitter データを分析する方法について説明します。

ビッグ データの多くはソーシャル Web サイトからもたらされます。Twitter などのサイトが公開している API を介して収集したデータは、現在の動向を分析して把握するための有益な情報源となります。このチュートリアルでは、Twitter の Web サービスに接続し、Twitter streaming API を使用して複数のツイートを取得します。さらに、Hive を使用して、特定の単語を含むツイートを多く送信した Twitter ユーザーの一覧を取得します。

**所要時間:** 30 分

## この記事の内容

-   [前提条件][前提条件]
-   [Twitter Feed の取得][Twitter Feed の取得]
-   [HiveQL スクリプトの作成][HiveQL スクリプトの作成]
-   [Hive を使用したデータの処理][Hive を使用したデータの処理]
-   [チュートリアルのクリーンアップ][チュートリアルのクリーンアップ]
-   [次のステップ][次のステップ]

## <span id="prerequisites"></span></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   **コンピューター**。Azure PowerShell がインストールされ構成されている必要があります。手順については、[Azure PowerShell のインストールおよび構成に関するページ][Azure PowerShell のインストールおよび構成に関するページ]を参照してください。PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。「[Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)][Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)]」を参照してください。

    PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。

        Add-AzureAccount

    Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。

        Select-AzureSubscription <AzureSubscriptionName>

-   **Azure HDInsight クラスター**。クラスターのプロビジョニングの手順については、「[Azure HDInsight の概要][Azure HDInsight の概要]」または「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。このチュートリアルを読み進めるには、次のデータが必要です。

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">クラスター プロパティ</th>
    <th align="left">PowerShell 変数名</th>
    <th align="left">値</th>
    <th align="left">説明</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight クラスター名</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">HDInsight クラスター名。</td>
    </tr>
    <tr class="even">
    <td align="left">Azure Storage アカウント名</td>
    <td align="left">$storageAccountName</td>
    <td align="left"></td>
    <td align="left">HDInsight クラスターで利用できる Azure Storage アカウント。このチュートリアルでは、クラスターのプロビジョニング プロセス中に指定された既定のストレージ アカウントを使用します。</td>
    </tr>
    <tr class="odd">
    <td align="left">Azure BLOB コンテナー名。</td>
    <td align="left">$containerName</td>
    <td align="left"></td>
    <td align="left">この例では、既定の HDInsight クラスター ファイル システムで使用する Azure BLOB ストレージ コンテナーを使用します。既定では、HDInsight クラスターと同じ名前です。</td>
    </tr>
    </tbody>
    </table>

**HDInsight ストレージについて**

HDInsight はデータ ストレージとして Azure BLOB ストレージを使用します。これは *WASB* または *Azure ストレージ - BLOB* と呼ばれています。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

HDInsight クラスターをプロビジョニングする際、HDFS と同様に、既定のファイル システムとして BLOB ストレージ コンテナーが指定されます。プロビジョニング プロセスを実行するときに、このコンテナーに加えて、同じ Azure ストレージ アカウントまたは別の Azure ストレージ アカウントに属する付加的なコンテナーを追加することもできます。付加的なストレージ アカウントを追加する方法の詳細については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

> [WACOM.NOTE] このチュートリアルで使用する PowerShell スクリプトを簡単にするために、ファイルはすべて、*/tutorials/twitter* にある既定のファイル システム コンテナーに格納されています。既定で、このコンテナーの名前は、HDInsight クラスター名と同じです。別のコンテナーにファイルを保存する場合は、適宜スクリプトを調整してください。

WASB の構文は次のとおりです。

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight クラスター Version 3.0 では、*wasb://* 構文のみがサポートされます。旧バージョンの *"asv://"* 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターではサポートされず、以降のバージョンでもサポートされません。

> WASB のパスは、仮想パスです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

既定のファイル システム コンテナーに格納されているファイルの場合、次の URI のどれを使用しても HDInsight からアクセスできます (例として tweets.txt を使用しています)。

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
    wasb:///tutorials/twitter/tweets.txt
    /tutorials/twitter/tweets.txt

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

    tutorials/twitter/tweets.txt

このチュートリアルで使用するファイルを次の表に示します。

| ファイル                           | 説明                                                                                               |
|------------------------------------|----------------------------------------------------------------------------------------------------|
| /tutorials/twitter/data/tweets.txt | Hive ジョブのソース データです。                                                                   |
| /tutorials/twitter/output          | Hive ジョブの出力フォルダーです。既定のファイルの Hive ジョブ出力ファイル名は **000000\_0** です。 |
| tutorials/twitter/twitter.hql      | HiveQL スクリプト ファイルです。                                                                   |
| /tutorials/twitter/jobstatus       | Hadoop ジョブの状態です。                                                                          |

## <span id="feed"></span></a>Twitter Feed の取得

このチュートリアルでは、[Twitter streaming API][Twitter streaming API] を使用します。使用する特定の Twitter steaming API は [statuses/filter][statuses/filter] です。

[ツイート データ][ツイート データ]は、複雑なネスト構造の JSon 形式で格納されます。従来のプログラミング言語を使用して多数のコード行を記述する代わりに、このネスト構造を Hive テーブルに変換し、SQL によく似た HiveQL という言語で照会するようにできます。

Twitter は OAuth を使用して、API への承認されたアクセスを提供します。OAuth は、パスワードを共有せずに代理でアプリケーションが動作することをユーザーが承認できるようにする認証プロトコルです。詳細については、[oauth.net][oauth.net]、または Hueniverse の便利な「[Beginner's Guide to OAuth (OAuth 初心者向けガイド)][Beginner's Guide to OAuth (OAuth 初心者向けガイド)]」で確認できます。

OAuth を使用するための最初の手順は、Twitter 開発者サイトで新しいアプリケーションを作成することです。

**Twitter アプリケーションを作成するには**

1.  [][]<https://apps.twitter.com/></a> にサインインします。Twitter アカウントを持っていない場合は、**[今すぐ登録]** リンクをクリックします。
2.  **[Create New App]** をクリックします。
3.  **名前**、**説明**、**Web サイト**を入力します。Web サイト フィールドの URL を構成することができます。次のテーブルは使用する値のサンプルを示しています。

    | フィールド | 値                            |
    |------------|-------------------------------|
    | 名前       | MyHDInsightApp                |
    | 説明       | MyHDInsightApp                |
    | Web サイト | http://www.myhdinsightapp.com |

4.  **[Yes, I agree]** をオンにして、**[Create your Twitter application]** をクリックします。
5.  **[Permissions]** タブをクリックします。既定のアクセス許可は**読み取り専用**です。このチュートリアルにはこれで十分です。
6.  **[API Keys]** タブをクリックします。
7.  **[Create my access token]** をクリックします。
8.  ページの右上隅にある **[Test OAuth]** をクリックします。
9.  **コンシューマー キー**、**コンシューマー シークレット**、**アクセス トークン**、**アクセス トークン シークレット**を書き留めます。これらの値は後で必要になります。

このチュートリアルでは、PowerShell を使用して Web サービスを呼び出します。Web サービスを呼び出すその他の一般的なツールは [*Curl*][*Curl*] です。Curl は[ここ][ここ]からダウンロードできます。

> [WACOM.NOTE] Windows で curl コマンドを使用する場合、オプション値には一重引用符の代わりに二重引用符を使用します。

**ツイートを取得するには**

1.  Windows PowerShell ISE を開きます (Windows 8 のスタート画面で、「**PowerShell\_ISE**」と入力し、**[Windows PowerShell ISE]** をクリックします。「[Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)][Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)])」を参照してください。

2.  次のスクリプトをスクリプト ウィンドウにコピーします。

        Write-Host "Set variables ..." -ForegroundColor Green
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $destBlobName = "tutorials/twitter/data/tweets.txt"

        $trackString = "Azure, Cloud, HDInsight"
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 100  #about 3 minutes every 100 lines

        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

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

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host $count.ToString() ", " -NoNewline -ForegroundColor Green
                if ($count%10 -eq 0){
                    write-host " --- " -NoNewline
                    Get-Date -Format hh:mm:sstt
                }

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }

        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream) 

        $sReader.close()
        Write-Host "Complete!" -ForegroundColor Green

3.  スクリプトの最初の 9 個の変数を設定します。

    | 変数                     | 説明                                                                                                                                                                                |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | $storageAccountName      | 既定の HDInsight クラスター ファイル システムに使用する Azure Storage アカウントです。これはプロビジョニングで指定されるストレージ アカウントです。[前提条件][前提条件]を参照してください。 |
    | $containerName           | 既定の HDInsight クラスター ファイル システムに使用する BLOB コンテナーです。これはプロビジョニングで指定されるコンテナーです。[前提条件][前提条件]を参照してください。                     |
    | $destBlobName            | 出力 BLOB 名です。既定値は、**tutorials/twitter/data/tweets.txt** です。既定値を変更する場合は、適宜 PowerShell スクリプトを更新する必要があります。                                |
    | $trackString             | Web サービスはこれらのキーワードに関連するツイートを返します。既定値は、**Azure、クラウド、HDInsight** です。既定値を変更する場合は、PowerShell スクリプトを更新します。            |
    | $lineMax                 | この値によってスクリプトが読み取るツイートの数が決まります。100 個のツイートを読み取るに約 3 分かかります。大きな数値を設定してもかまいませんが、ダウンロードに時間がかかります。   |
    | $oauth\_consumer\_key    | Twitter アプリケーションを作成したときに書き留めた Twitter アプリケーションの**コンシューマー キー**です。                                                                          |
    | $oauth\_consumer\_secret | 前に書き留めた Twitter アプリケーションの**コンシューマー シークレット**です。                                                                                                      |
    | $oauth\_token            | 前に書き留めた Twitter アプリケーションの**アクセス トークン**です。                                                                                                                |
    | $oauth\_token\_secret    | 前に書き留めた Twitter アプリケーションの**アクセス トークン シークレット**です。                                                                                                   |

4.  **F5** キーを押して、スクリプトを実行します。問題が発生した場合は、回避策としてすべての行を選択し、**F8** キーを押します。
5.  出力の最後に "Complete!" と表示されます。エラー メッセージが赤色で表示されます。

検証手順として、Azure ストレージ エクスプローラーまたは Azure PowerShell を使用して Azure BLOB ストレージ上で出力ファイル **/tutorials/twitter/data/tweets.txt** を確認できます。一覧表示するファイルのサンプル PowerShell スクリプトについては、「[HDInsight での BLOB ストレージの使用][HDInsight での BLOB ストレージの使用]」を参照してください。

## <span id="script"></span></a>HiveQL スクリプトの作成

Azure PowerShell を使用して、複数の HiveQL ステートメントを一度に実行することも、HiveQL ステートメントをスクリプト ファイルにまとめることもできます。このチュートリアルでは、HiveQL スクリプトを作成します。スクリプト ファイルは、WASB にアップロードする必要があります。次のセクションでは、Azure PowerShell を使用してスクリプト ファイルを実行します。

HiveQL スクリプトは、次の作業を実行します。

1.  **tweets\_raw テーブルを削除します** (テーブルが既に存在する場合)。
2.  **tweets\_raw Hive テーブルを作成します**。この一時的な Hive 構造テーブルには、さらに ETL 処理のデータが保持されます。パーティションの詳細については、「[Hive tutorial (Hive のチュートリアル)][Hive tutorial (Hive のチュートリアル)]」を参照してください。
3.  **データの読み込み。**ソース フォルダー (/tutorials/twitter/data) からデータを読み込みます。JSon ネスト形式の大規模なツイート データセットが、一時的な Hive テーブル構造に変換されました。
4.  **ツイート テーブルを削除します** (テーブルが既に存在する場合)。
5.  **ツイート テーブルを作成します**。Hive を使用してツイート データセットを照会するには、別の ETL 処理を実行する必要があります。この ETL 処理は、"twitter\_raw" テーブルに保存したデータのための詳細なテーブル スキーマを定義します。
6.  **上書きテーブルを挿入します**。この複雑な Hive スクリプトは、Hadoop クラスターによる一連の長い Map Reduce ジョブを開始します。使用するデータセットおよびクラスターのサイズによっては、10 分ほどかかる場合があります。
7.  **上書きディレクトリを挿入します**。クエリを実行し、データセットをファイルに出力します。このクエリは、"Azure" という単語が含まれるツイートを送信した Twitter ユーザーの一覧を返します。

**Hive スクリプトを作成して Azure にアップロードするには**

1.  Windows PowerShell ISE を開きます。
2.  次のスクリプトをスクリプト ウィンドウにコピーします。

        Write-Host "Define variables ..." -ForegroundColor Green
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"

        $hqlScriptFile = "tutorials/twitter/twitter.hql"

        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;

        DROP TABLE tweets_raw;
        CREATE TABLE tweets_raw (
            json_response STRING
        ) 
        PARTITIONED BY (filesequence INT);

        LOAD DATA INPATH '$sourceDataPath'
        INTO TABLE tweets_raw
        PARTITION (filesequence = 1);

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
        )
        PARTITIONED BY (filesequence INT);

        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        PARTITION (filesequence)
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
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
            json_response,
            filesequence
        WHERE (length(json_response) > 500);

        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc 
            FROM tweets 
            WHERE text like "%Azure%" 
            GROUP BY name,screen_name 
            ORDER BY cc DESC LIMIT 10;
        "@

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

        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)

        Write-Host "Complete!" -ForegroundColor Green

3.  スクリプトの最初の 2 個の変数を設定します。

    | 変数                | 説明                                                                                                                                                                                |
    |---------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | $storageAccountName | 既定の HDInsight クラスター ファイル システムに使用する Azure Storage アカウントです。これはプロビジョニングで指定されるストレージ アカウントです。[前提条件][前提条件]を参照してください。 |
    | $containerName      | 既定の HDInsight クラスター ファイル システムに使用する BLOB コンテナーです。これはプロビジョニングで指定されるコンテナーです。[前提条件][前提条件]を参照してください。                     |
    | $sourceDataPath     | Hive クエリがデータを読み取る WASB の場所です。この変数を変更する必要はありません。                                                                                                 |
    | $outputPath         | Hive クエリが結果を出力する WASB の場所です。この変数を変更する必要はありません。                                                                                                   |
    | $hqlScriptFile      | HiveQL スクリプト ファイルの場所とファイル名です。                                                                                                                                  |

4.  **F5** キーを押して、スクリプトを実行します。問題が発生した場合は、回避策としてすべての行を選択し、**F8** キーを押します。
5.  出力の最後に "Complete!" と表示されます。エラー メッセージが赤色で表示されます。

検証手順として、Azure ストレージ エクスプローラーまたは Azure PowerShell を使用して Azure BLOB ストレージ上で出力ファイル **/tutorials/twitter/twitter.hql** を確認できます。一覧表示するファイルのサンプル PowerShell スクリプトについては、「[HDInsight での BLOB ストレージの使用][HDInsight での BLOB ストレージの使用]」を参照してください。

## <a name="process"></a> Hive を使用して Twitter データを処理する

すべての準備作業が完了しました。Hive スクリプトを呼び出して、結果を確認できます。

### Hive ジョブを送信する

次の PowerShell スクリプトを使用して Hive スクリプトを実行します。最初の変数を設定する必要があります。

    Write-Host "Set the variables ... " -ForegroundColor Green
    $clusterName = "<HDInsightClusterName>"

    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    $statusFolder = "/tutorials/twitter/jobstatus"

    Write-Host "Invoke Hive ... " -ForegroundColor Green
    Use-AzureHDInsightCluster $clusterName
    $response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable

    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 

### 結果を確認する

次の PowerShell スクリプトを使用して Hive ジョブ出力を確認します。最初の 2 つの変数を設定する必要があります。

    Write-Host "Set the variables ... " -ForegroundColor Green
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "Display the output ..." -ForegroundColor Green
    cat "./$blob"

> [WACOM.NOTE] Hive テーブルでは \\001 をフィールド区切り記号として使用します。区切り記号は出力には表示されません。

分析結果が WASB に配置されると、Azure SQL Database/SQL Server へのデータのエクスポート、Power Query を使用してのデータの Excel へのエクスポート、または Hive ODBC ドライバーを使用してのアプリケーションのデータへの接続ができます。詳細については、「[HDInsight での Sqoop の使用][HDInsight での Sqoop の使用]」、「[HDInsight を使用したフライト遅延データの分析][HDInsight を使用したフライト遅延データの分析]」、「[Power Query を使用した Excel から HDInsight への接続][Power Query を使用した Excel から HDInsight への接続]」、および「[Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続]」を参照してください。

## <span id="cleanup"></span></a>チュートリアルのクリーンアップ

チュートリアルを再実行する場合は、次の作業が必要です。

-   **ツイート データ ファイルを再作成します**。ソースのツイート データ ファイルは Hive ジョブで削除されています。新しいファイルを生成する必要があります。ファイル名は、**tutorials/twitter/data/tweets.txt** です。「[Twitter Feed の取得][Twitter Feed の取得]」を参照してください。

## <span id="nextsteps"></span></a> 次のステップ

このチュートリアルでは、Azure 上で HDInsight を使用し、Twitter から収集したデータを照会、探索、分析するため、構造化されていない Json データセットを構造化された Hive テーブルへ変換する方法を学習しました。詳細については、次を参照してください。

-   [HDInsight の使用][Azure HDInsight の概要]
-   [HDInsight を使用したフライトの遅延データの分析][HDInsight を使用したフライト遅延データの分析]
-   [Power Query を使用した Excel から HDInsight への接続][Power Query を使用した Excel から HDInsight への接続]
-   [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続]
-   [HDInsight での Sqoop の使用][HDInsight での Sqoop の使用]

  [前提条件]: #prerequisites
  [Twitter Feed の取得]: #feed
  [HiveQL スクリプトの作成]: #script
  [Hive を使用したデータの処理]: #process
  [チュートリアルのクリーンアップ]: #cleanup
  [次のステップ]: #nextsteps
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-configure-powershell
  [Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)]: http://technet.microsoft.com/ja-jp/library/ee176961.aspx
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [Twitter streaming API]: https://dev.twitter.com/docs/streaming-apis
  [statuses/filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
  [ツイート データ]: https://dev.twitter.com/docs/platform-objects/tweets
  [oauth.net]: http://oauth.net/
  [Beginner's Guide to OAuth (OAuth 初心者向けガイド)]: http://hueniverse.com/oauth/
  []: https://apps.twitter.com/
  [*Curl*]: http://curl.haxx.se
  [ここ]: http://curl.haxx.se/download.html
  [Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)]: http://technet.microsoft.com/ja-jp/library/hh847889.aspx
  [HDInsight での BLOB ストレージの使用]: ../hdinsight-use-blob-storage/#powershell
  [Hive tutorial (Hive のチュートリアル)]: https://cwiki.apache.org/confluence/display/Hive/Tutorial
  [HDInsight での Sqoop の使用]: ../hdinsight-use-sqoop/
  [HDInsight を使用したフライト遅延データの分析]: ../hdinsight-analyze-flight-delay-data/
  [Power Query を使用した Excel から HDInsight への接続]: ../hdinsight-connect-excel-power-query/
  [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続]: ../hdinsight-connect-excel-hive-ODBC-driver/
