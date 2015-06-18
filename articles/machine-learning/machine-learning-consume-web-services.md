<properties 
	pageTitle="Machine Learning  の実験から発行された Machine Learning の Web サービスを使用する方法 | Azure" 
	description="機械学習サービスが発行されると、利用可能になっている RESTFul Web サービスを、要求応答サービスまたはバッチ実行サービスのいずれかとして使用できます。" 
	services="machine-learning" 
	solutions="big-data" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/20/2015" 
	ms.author="bradsev" />


# 発行済みの Azure Machine Learning Web サービスを使用する方法

## はじめに

Azure Machine Learning の実験は、Web サービスとして発行されると、様々なデバイスやプラットフォームが使用できる REST API を提供します。これは、単純な REST API では JSON 形式のメッセージを使用して受け入れと応答がなされるからです。Azure Machine Learning ポータルは、R、C#、および Python で Web サービスを呼び出すために使用できるコードを提供します。ただし、これらのサービスは、次の 3 つの条件を満たす任意のプログラミング言語とデバイスから呼び出すことができます。

* ネットワークに接続している
* HTTPS 要求を実行する SSL の機能がある
* JSON を解析する機能がある (手動またはサポート ライブラリによる)

つまり、Web アプリケーション、モバイル アプリケーション、カスタム デスクトップ アプリケーション、および Excel からもサービスを利用できます。  

Azure Machine Learning の Web サービスは、要求応答サービスまたはバッチ実行サービスの 2 つの異なる方法で使用できます。各シナリオでは、実験が発行されてから使用できる RESTFul Web サービスを通じて機能が提供されます。Azure Web サービス エンドポイントを使用して Azure で Machine Learning  Web サービスを配置すると、サービスが利用状況に基づいて自動的に規模変更されるので、ハードウェア リソースに対する先行投資と継続的なコストを回避できます。

<!-- この記事を公開する場合は、リンクを修正してコメントを解除してください
REST API を使用して Azure Machine Learning  の Web サービスのエンドポイントを管理する方法の詳細については、「**Azure Machine Learning Web サービスのエンドポイント**」を参照してください。 
-->

Azure Machine Learning  Web サービスの作成と公開方法の詳細については、 
「[Azure Machine Learning  Web サービスを発行する][publish]」を参照してください。Machine Learning  の実験の作成と発行に関する詳しい手順は、「[Azure Machine Learning を使用した予測ソリューションの開発][walkthrough]」を参照してください。

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## 要求応答サービス (RRS)

要求応答サービス (RRS) は、待ち時間が短く、拡張性の高い Web サービスであり、Azure Machine Learning Studio の実験で作成および発行されたステートレスなモデルへのインターフェイスを提供するために使用されます。

RRS は、単一行の入力パラメーターを受け取り、単一行を出力パラメーターとして生成します。出力行には複数列が含まれる可能性があります。

RRS の例では、アプリケーションの信頼性を検証しています。この例では、数百万のアプリケーションのインストールが予想されます。アプリケーションの開始時に、関連する入力を使用して RRS サービスへの呼び出しを実行します。次にアプリケーションは、アプリケーションの実行を許可またはブロックするサービスからの検証応答を受信します。


## バッチ実行サービス (BES)
 
バッチ実行サービス (BES) は、データ レコードのバッチに対して、大量に非同期でスコリングを処理するためのサービスです。BES の入力には、blob、Azure のテーブル、SQL Azure、HDInsight (Hive クエリの結果など)、HTTP のソースなど、さまざまなソースからのレコードのバッチが含まれています。BES の出力には、スコアの結果が含まれます。結果は、Azure blob ストレージ内のファイルに出力し、記憶域のエンドポイントからのデータは、応答で返されます。

BES は、個人やモノのインターネット (IOT) の定期的にスケジュールされたスコア付けなど、応答がすぐには必要でない場合に役立ちます。

## 例
RRS と BES の両方の動作方法を示すために、Azure の Web サービスの例を使用します。このサービスは、IOT (モ ノのインターネット) のシナリオで使用されます。ここではシンプルにするために、デバイスは、 `cog_speed` という値を 1 つだけ送信し、1 つの応答を取得します。 

RR または BES のいずれかのサービスを呼び出すために必要な 4 つの情報があります。この情報は、実験を発行したら、[Azure Machine Learning サービスのページ](https://studio.azureml.net)からすぐに利用できます。画面の左側にある WEB サービスのリンクをクリックすると、公開されたサービスが表示されます。特定のサービスに関する情報を検索するために、RRS と BES の両方の API ヘルプ ページのリンクがあります。

1.	サービスのメインページで利用できる**サービスの API キー**
2.	選択したサービスの API ヘルプ ページで利用できる**サービス URI**
3.	選択したサービスの API ヘルプ ページで利用できる、予想される**API 要求の本文**
4.	選択したサービスの API ヘルプ ページで利用できる、予想される**API 応答の本文**

次の 2 つの例では、必要なコードを説明するために c# 言語が使用されており、対象プラットフォームは、Windows 8 デスクトップです。 

### RRS の例
URI のほかに、API ヘルプ ページで、定義とコード サンプルを入力および出力します。API の入力が特にこのサービスに対して呼び出され、API 呼び出しのペイロードになります。 

**要求のサンプル**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


同様に、API の応答も特にこのサービスに対して呼び出されます。

**応答のサンプル**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

ページの下部に、コード例が表示されます。c# の実装のコード サンプルを次に示します。 
                   
**サンプル コード**
	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;
	
	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () { 
	                        { 
	                            "input1", 
	                            new StringTable() 
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };
	                
	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	
	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");
	                
	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

### BES の例
API ヘルプ ページで、URI だけでなく、使用できるいくつかの呼び出しに関する情報が表示されます。RR サービスとは異なり、BES サービスは非同期です。これは、BES API は実行されるジョブをキューに入れるだけであることを意味します。API 応答が受信される前に実際に実行することはありません。BES サービスを使用して開発者が実行できることが 3 つありますが、それらについて以下で説明します。

1. バッチ実行ジョブを送信する
1. バッチ実行ジョブの状態または結果を取得する
1. バッチ実行ジョブを削除する  

**1.バッチ実行ジョブを送信する**

バッチ実行ジョブを送信するには、バッチのデータの格納場所に関する情報を指定します。この例では、バッチ レコードを必要とするレコードが、ストレージ アカウントの blob ファイルにあるものとします。

ジョブは非同期で実行されるため、バッチ ジョブへの応答は、ここでもジョブ ID です。ジョブ ID を使用して、ジョブの状態と結果を後で取得します。

**要求のサンプル**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Output": null,
	  "GlobalParameters": {}
	}

**応答のサンプル**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**サンプル コード**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	
	using System;
	using System.Collections.Generic;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Net.Http.Headers;
	
	namespace CallBatchExecutionService
	{
	    internal class Program
	    {
	        private static void Main(string[] args)
	        {
	            InvokeBatchExecutionService().Wait();
	        }
	
	        private static async Task InvokeBatchExecutionService()
	        {
	            // API Information
	            const string BESUrl = "[BES URI]";
	            const string ApiKey = "abc123"; 
	            // The storage account information
	            const string StorageAccountName = @"mystorageacct"; 
	            const string StorageAccountKey = @"Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==";
	            // Storage file with the batch of records
	            const string StorageInputFile = @"/mycontainermydatablob.csv"; 
	
	
	            String connString = String.Format(
	                "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
	                StorageAccountName,
	                StorageAccountKey);
	
	            BatchRequest request = new BatchRequest();
	            request.Input.RelativeLocation = StorageInputFile;
	            request.Input.ConnectionString = connString;
	
	            using (var client = new HttpClient())
	            {
	                client.BaseAddress = new Uri(BESUrl);
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.PostAsJsonAsync("", request);
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Job ID: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	
	    public class BatchInput
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	
	        public BatchInput()
	        {
	            ConnectionString = null;
	            RelativeLocation = null;
	            BaseLocation = null;
	            SasBlobToken = null;
	        }
	    }
	
	    public class BatchRequest
	    {
	        public BatchInput Input { get; set; }
	
	        public Object Output { get; set; }
	
	        public Dictionary<string, string> GlobalParameters { get; set; }
	
	        public BatchRequest()
	        {
	            this.GlobalParameters = new Dictionary<string, string>();
	            Input = new BatchInput();
	            Output = null;
	        }
	    }
	}
	
**2.バッチ実行ジョブの状態または結果を取得する**

ジョブの結果を取得するには、まずジョブの送信への応答内にあるジョブ ID が必要です。この API 呼び出しに実際の入力はありません。BES URI を若干変更したものと、要求メソッドを使用します。POST 要求の代わりに、API ヘルプ ページで定義されている URI に従って GET 要求を使用します。
 
ただし、応答は階層化されます。

**応答ペイロード**

	{
	    "StatusCode": STATUS_CODE,
	    "Result": RESULT,
	    "Details": DETAILS
	}

`StatusCode` の可能な値は 0、1、2、3、または 4 です。その意味は、以下のとおりです。

* 0	開始されていません
* 1	実行中です
* 2	失敗しました
* 3	取り消されました
* 4	完了しました

ジョブが完了していない場合、 `Result` は **null** です。ジョブが完了したら場合、 `Result` は次の形式になります。 

	{
	  "ConnectionString": null,
	  "RelativeLocation": "RELATIVE_LOCATION",
	  "BaseLocation": "BASE_LOCATION",
	  "SasBlobToken": "SAS_BLOB_TOKEN"
	}

詳細では、エラーが存在する場合にその詳細を表示します。

**サンプル コード**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	//
	// Also, add a reference to Microsoft.WindowsAzure.Storage.dll for reading from and writing to the Azure blob storage
	
	using System;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Headers;
	using System.Threading.Tasks;
	using Newtonsoft.Json;
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            String jobId = "123";
	            InvokeBatchExecutionService(jobId).Wait();
	        }
	
	        static async Task InvokeBatchExecutionService(String JobId)
	        {
	            Console.WriteLine(String.Format("Getting job status for job {0}", JobId));
	
	            // BES Information
	            const string BaseUrl = @"[BES Job Id]/{0}";
	            const string ApiKey = "abc123"; 
	            // Replace this with the location you would like to use for your output file
	            const string OutputFileLocation = @"myresults.csv"; 
	
	            using (var client = new HttpClient())
	            {
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.GetAsync(String.Format(BaseUrl, JobId));
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    BatchResponseStructure responseStruct = JsonConvert.DeserializeObject<BatchResponseStructure>(result);
	
	                    switch (responseStruct.StatusCode)
	                    {
	                        case (int)BatchScoreStatusCode.NotStarted:
	                            Console.WriteLine("Not started...");
	                            break;
	                        case (int)BatchScoreStatusCode.Running:
	                            Console.WriteLine("Running...");
	                            break;
	                        case (int)BatchScoreStatusCode.Failed:
	                            Console.WriteLine("Failed!");
	                            Console.WriteLine(string.Format(@"Error details: {0}", status.Details));
	                            break;
	                        case (int)BatchScoreStatusCode.Cancelled:
	                            Console.WriteLine("Cancelled!");
	                            break;
	                        case (int)BatchScoreStatusCode.Finished:
	                            Console.WriteLine("Finished!");
	                            var credentials = new StorageCredentials(status.Result.SasBlobToken);
	                            var cloudBlob = new CloudBlockBlob(new Uri(new Uri(responseStruct.Result.BaseLocation), 
	                                                                                               responseStruct.Result.RelativeLocation), credentials);
	                            cloudBlob.DownloadToFile(OutputFileLocation, FileMode.Create);
	                            Console.WriteLine(string.Format(@"The results have been written to the file {0}", OutputFileLocation));
	                            break;
	                    }
	                }
	                else
	                {
	                    Console.WriteLine(String.Format("Batch Result : Failed with status code: {0}", response.StatusCode));
	                }
	            }
	        }
	    }
	
	    public enum BatchScoreStatusCode : int
	    {
	        NotStarted = 0,
	        Running = 1,
	        Failed = 2,
	        Cancelled = 3,
	        Finished = 4
	    }
	
	    public class BatchResult
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	    }
	
	    public class BatchResponseStructure
	    {
	        public int StatusCode { get; set; }
	        public BatchResult Result { get; set; }
	        public String Details { get; set; }
	        public BatchResponseStructure()
	        {
	            this.Result = new BatchResult();
	        }
	    }
	}

**3.バッチ実行ジョブを削除する**              
ジョブは開始されると、削除することもできます。これは、ジョブが完了までにかかる時間が長すぎるなど、様々な理由で実行されます。ジョブの結果を削除するには、まずジョブ送信への応答内に含まれていたジョブ ID が必要です。

この API 呼び出しに実際の入力はありません。BES URI を若干変更したものと、要求メソッドを使用します。POST 要求の代わりに、API ヘルプ ページで定義されている URI に従って DELETE 要求を使用します。このコード サンプルは、非常に簡単です。


<!--HONumber=49--> 