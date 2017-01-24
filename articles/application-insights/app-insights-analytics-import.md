---
title: "Azure Application Insights の Analytics にデータをインポートする | Microsoft Docs"
description: "静的データをインポートしてアプリのテレメトリと結合したり、個別のデータ ストリームをインポートして Analytics でクエリを実行できます。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 98ace6ab2bc2a55bc0101284f5c7675fb1bbed68
ms.openlocfilehash: 510a25415fd264eee994e16cac9ae55de8e740f6


---
# <a name="import-data-into-analytics"></a>Analytics へのデータのインポート

任意の表形式データを [Analytics](app-insights-analytics.md) にインポートして、アプリの [Application Insights](app-insights-overview.md) テレメトリと結合させたり、個別のストリームとして分析できるようにすることができます。 Analytics は、テレメトリのタイムスタンプ付きストリームを大量に分析するのに適した、強力なクエリ言語です。

Analytics には、独自のスキーマを使用してデータをインポートすることができます。 標準の Application Insights スキーマ (要求やトレースなど) を使用する必要はありません。

現時点では、CSV (コンマ区切り) ファイルのほか、タブまたはセミコロンを区切り文字に使用した類似の形式をインポートできます。

Analytics へのインポートは、次の 3 つの状況で役に立ちます。

* **アプリのテレメトリと結合する。** たとえば、Web サイトの URL を読みやすいページ タイトルにマップするテーブルをインポートすることもできます。 Analytics では、Web サイト内で特に人気のある上位 10 件のページを表示する、ダッシュボード チャート レポートを作成することができます。 これからは、URL の代わりにページ タイトルを 表示できるようになりました。
* **アプリケーションのテレメトリを、他のソース (ネットワーク トラフィック、サーバー データ、または CDN ログ ファイルなど) に関連付ける。**
* **Analytics を個別のデータ ストリームに適用する。** Application Insights の Analytics は、タイムスタンプ付きのスパース ストリームと効果的に連携できる強力なツールです。これは多くの場合、SQL よりもはるかに効果的です。 他のソースからこの種のストリームが送られる場合は、それらを Analytics で分析できます。

データ ソースへのデータ送信は簡単です。 

1. (1 回) データ ソース内のデータのスキーマを定義します。
2. (定期的) Azure ストレージにデータをアップロードし、REST API を呼び出して、新しいデータが取り込みを待機していることを Microsoft に通知します。 数分後には、それらのデータに対するクエリを Analytics で実行できるようになります。

アップロードの頻度は、データをいつまでにクエリで使用できるようにしたいかに応じて、お客様が定義します。 データは大きなチャンクでアップロードしたほうが効率的ですが、1 GB が上限となります。

> [!NOTE]
> *分析するデータ ソースの数が多い場合* [*Application Insights へのデータ送信に *logstash* を使用することを検討してください。*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>開始する前に

必要なもの:

1. Microsoft Azure での Application Insights リソース。

 * その他のテレメトリからデータを個別に分析する場合は、[新規の Application Insights リソースを作成](app-insights-create-new-resource.md)してください。
 * データを、既に Application Insights で設定されているアプリのテレメトリと結合または比較する場合は、そのアプリのリソースを使用できます。
 * そのリソースには、共同作成者または所有者がアクセスできます。
 
2. Azure ストレージ。 Azure ストレージにデータをアップロードすると、Analytics がそこからデータを取得します。 

 * 対象の BLOB に対して専用のストレージ アカウントを作成することをお勧めします。 BLOB が他のプロセスと共有されている場合は、BLOB を読み取るプロセスの所要時間が長くなります。

2. この機能がプレビュー中である間は、アクセス権を申請する必要があります。

 * [Azure ポータル](https://portal.azure.com)の Application Insights リソースから、Analytics を開きます。 
 * [スキーマ] ペインの下部で、**[Other Data Sources (他のデータ ソース)]** の [お問い合わせ] リンクをクリックします。 
 * [データ ソースの追加] が表示された場合は、既にアクセス権が付与されています。


## <a name="define-your-schema"></a>スキーマの定義

データをインポートする前に、*データ ソース*を定義する必要があります。これにより、データのスキーマを指定します。

1. データ ソース ウィザードを起動します。

    ![新しいデータ ソースの追加](./media/app-insights-analytics-import/add-new-data-source.png)

2. 指示に従って、サンプル データ ファイルをアップロードします。

 * サンプルの最初の行は、列ヘッダーにすることができます (フィールド名は次の手順で変更できます)。
 * サンプルには、少なくとも 10 行のデータを含める必要があります。

3. サンプルから推論されたスキーマを確認します。 必要に応じて、推論された列のタイプを調整することもできます。

4. タイムスタンプを選択します。 Analytics では、すべてのデータにタイムスタンプ フィールドが必要です。 タイプは `datetime` である必要がありますが、名前を 'timestamp' にする必要はありません。 データに ISO 形式の日時を含んだ列がある場合は、それをタイムスタンプ列として選択します。 それ以外の場合は、[as data arrived (データの到着時)] を選択すると、インポート プロセスによってタイムスタンプ フィールドが追加されます。

    ![スキーマの確認](./media/app-insights-analytics-import/data-source-review-schema.png)

5. データ ソースを作成します。


## <a name="import-data"></a>Import data

データをインポートするには、Azure ストレージにデータをアップロードし、データのアクセス キーを作成した後、REST API 呼び出しを実行します。

![新しいデータ ソースの追加](./media/app-insights-analytics-import/analytics-upload-process.png)

次のプロセスは、手動で実行するか、または自動化されたシステムを設定して定期的に実行することができます。 インポートするデータのブロックごとに、これらの手順を実行する必要があります。

1. [Azure BLOB ストレージ](../storage/storage-dotnet-how-to-use-blobs.md)にデータをアップロードします。 

 * BLOB のサイズは、非圧縮で 1 GB が上限となります。 パフォーマンスの観点から言うと、数百 MB の BLOB が最適なサイズです。
 * Gzip で圧縮すれば、アップロード時間が短縮されるだけでなく、データがクエリで使用できるようになるまでの時間も短縮されます。 ファイル名拡張子は `.gz` を使用してください。
 
2. [BLOB の Shared Access Signature キーを作成します](../storage/storage-dotnet-shared-access-signature-part-2.md)。 このキーでは、有効期限を 1 日とし、読み取りアクセスを提供する必要があります。
3. データが待機していることを Application Insights に通知するための REST 呼び出しを実行します。

 * エンドポイント: `https://eus-breeziest-in.cloudapp.net/v2/track`
 * HTTP メソッド: POST
 * ペイロード:

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Data source name>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

プレース ホルダーは次のとおりです。

* `Blob URI with Shared Access Key`: これは、キーを作成するためのプロシージャから取得します。 これは BLOB に固有のものです。
* `Data source name`: データ ソースに付けた名前です。 この BLOB 内のデータは、このソースに対して定義したスキーマに準拠している必要があります。
* `DateTime`: 要求が送信された時刻 (UTC) です。 受け付けられる形式は次のとおりです: ISO8601 ("2016-01-01 13:45:01" など)、RFC822 ("Wed, 14 Dec 16 14:57:01 +0000")、RFC850 ("Wednesday, 14-Dec-16 14:57:00 UTC")、RFC1123 ("Wed, 14 Dec 2016 14:57:00 +0000")。
* Application Insights リソースの `Instrumentation key`。

データは数分後に Analytics で利用可能になります。

## <a name="error-responses"></a>エラー応答

* **400 bad request**: 要求のペイロードが無効であることを示します。 次のことを確認してください。
 * インストルメンテーション キーが正しい。
 * 時刻値が有効である。 時刻は UTC である必要があります。
 * データがスキーマに準拠している。
* **403 Forbidden**: 送信した BLOB にアクセスできません。 共有アクセス キーが有効であることと、有効期限が切れていないことを確認してください。
* **404 Not Found**:
 * BLOB が存在しません。
 * データ ソース名が間違っています。

詳細は、応答エラー メッセージで確認できます。

## <a name="sample-code"></a>サンプル コード

このコードでは、[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) NuGet パッケージを使用しています。

### <a name="classes"></a>クラス

```C#


namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri breezeEndpoint = new Uri("https://eus-breeziest-in.cloudapp.net/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(breezeEndpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            HttpWebResponse response; 
            try 
            { 
                response = (HttpWebResponse)await request.GetResponseAsync(); 
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                } 
                return false; 
            } 

            return response.StatusCode == HttpStatusCode.OK; 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 

```

### <a name="ingest-data"></a>データの取り込み

このコードを各 BLOB に使用します。 

```C#


   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "tableId/sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);

```

## <a name="next-steps"></a>次のステップ

* [Analytics クエリ言語のツアー](app-insights-analytics-tour.md)
* [*Logstash* を使用して Application Insights にデータを送信する](https://github.com/Microsoft/logstash-output-application-insights)



<!--HONumber=Dec16_HO3-->


