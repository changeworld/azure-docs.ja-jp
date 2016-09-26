<properties
	pageTitle="Machine Learning Web サービスを使用する | Microsoft Azure"
	description="機械学習サービスがデプロイされると、利用可能になっている RESTFul Web サービスを、要求応答サービスまたはバッチ実行サービスのいずれかとして使用できます。"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd"
	ms.date="08/19/2016"
	ms.author="garye" />


# Machine Learning の実験からデプロイされた Azure Machine Learning Web サービスを使用する方法

## はじめに

Azure Machine Learning の実験は、Web サービスとしてデプロイされると、さまざまなデバイスやプラットフォームが使用できる REST API を提供します。これは、単純な REST API では JSON 形式のメッセージを使用して受け入れと応答がなされるからです。Azure Machine Learning ポータルは、R、C#、および Python で Web サービスを呼び出すために使用できるコードを提供します。ただし、これらのサービスは、次の 3 つの条件を満たす任意のプログラミング言語とデバイスから呼び出すことができます。

* ネットワークに接続している
* HTTPS 要求を実行する SSL の機能がある
* JSON を解析する機能がある (手動またはサポート ライブラリによる)

つまり、Web アプリケーション、モバイル アプリケーション、カスタム デスクトップ アプリケーション、および Excel からもサービスを利用できます。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Azure Machine Learning の Web サービスは、要求応答サービスまたはバッチ実行サービスの 2 つの異なる方法で使用できます。各シナリオでは、実験がデプロイされてから使用できる RESTFul Web サービスを通じて機能が提供されます。Azure Web サービス エンドポイントを使用して Azure で Machine Learning Web サービスを配置すると、サービスが利用状況に基づいて自動的に規模変更されるので、ハードウェア リソースに対する先行投資と継続的なコストを回避できます。

> [AZURE.TIP] 予測 Web サービスにアクセスする Web アプリケーションを作成する簡単な方法については、[Web アプリケーション テンプレートを利用した Azure Machine Learning Web サービスの使用](machine-learning-consume-web-service-with-web-app-template.md)についてのページを参照してください。

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

Azure Machine Learning Web サービスの作成とデプロイの詳細については、「[Azure Machine Learning Web サービスをデプロイする][publish]」をご覧ください。Machine Learning の実験の作成とデプロイに関する詳しい手順については、「[チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する][walkthrough]」を参照してください。

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## 要求応答サービス (RRS)

要求応答サービス (RRS) は、待ち時間が短く、拡張性の高い Web サービスであり、Azure Machine Learning Studio の実験で作成およびデプロイされたステートレスなモデルへのインターフェイスを提供するために使用されます。このサービスを使用すると、使用側アプリケーションがリアルタイムでの応答を期待するシナリオに対応できます。

RRS は、1 行または複数行の入力パラメーターを受け取り、出力として 1 行または複数行を生成できます。出力行には複数の列を含めることができます。

RRS の例では、アプリケーションの信頼性を検証しています。この例では、数百万のアプリケーションのインストールが予想されます。アプリケーションの開始時に、関連する入力を使用して RRS サービスへの呼び出しを実行します。次にアプリケーションは、アプリケーションの実行を許可またはブロックするサービスからの検証応答を受信します。


## バッチ実行サービス (BES)

バッチ実行サービス (BES) は、データ レコードのバッチに対して、大量に非同期でスコアリングを処理するためのサービスです。BES の入力には、blob、Azure のテーブル、SQL Azure、HDInsight (Hive クエリの結果など)、HTTP のソースなど、さまざまなソースからのレコードのバッチが含まれています。BES の出力には、スコアの結果が含まれます。結果は、Azure blob ストレージ内のファイルに出力し、記憶域のエンドポイントからのデータは、応答で返されます。

BES は、個人やモノのインターネット (IOT) の定期的にスケジュールされたスコア付けなど、応答がすぐには必要でない場合に役立ちます。

## 例
RRS と BES の両方の動作方法を示すために、Azure Web サービスの例を使用します。このサービスは、IOT (モノのインターネット) のシナリオで使用されます。ここではシンプルにするために、デバイスは、`cog_speed` という値を 1 つのみ送信し、1 つの応答を取得します。

RR または BES のいずれかのサービスを呼び出すために必要な 4 つの情報があります。この情報は、実験をデプロイしたら、[Azure Machine Learning Studio](https://studio.azureml.net) のサービス ページからすぐに利用できます。画面の左側にある [Web サービス] タブをクリックすると、デプロイされたサービスが表示されます。RRS と BES 両方の以下のリンクと情報を見つけるには、サービスをクリックします。

1.	サービスの **API キー**。サービスのダッシュボードに表示されます。
2.	サービスの**要求 URI**。選択したサービスの API ヘルプ ページに表示されます。
3.	予想される API の**要求ヘッダー**と**本文**。選択したサービスの API ヘルプ ページに表示されます。
4.	予想される API の**応答ヘッダー**と**本文**。選択したサービスの API ヘルプ ページに表示されます。

次の 2 つの例では、必要なコードを説明するために c# 言語が使用されており、対象プラットフォームは、Windows 8 デスクトップです。

### RRS の例
API ヘルプ ページを表示するには、サービスのダッシュボードで、**[API ヘルプ ページ]** の **[要求/応答]** をクリックします。このページには、URI の他に、入出力定義とコード例が表示されます。特にこのサービスに対する API 入力を次に示します。これは API 呼び出しのペイロードになります。

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


同様に、このサービスの API 応答も次に示します。

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

ヘルプ ページの下部に、コード例が表示されます。c# の実装のコード例を次に示します。

**C# のコード サンプル**

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

**Java のコード サンプル**

次のサンプル コードでは、Java で REST API 要求を構築する方法を示しています。REST API が正しく予測できるように必要な API 詳細が変数 (apikey and apiurl) に指定され、正しい JSON オブジェクトが変数 jsonBody に指定されているものと想定されています。完全なコードは github ([https://github.com/nk773/AzureML\_RRSApp](https://github.com/nk773/AzureML_RRSApp)) からダウンロードできます。この Java サンプルは [apache http クライアント ライブラリ](https://hc.apache.org/downloads.cgi)を必要とします。

	/**
	 * Download full code from github - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp)
 	 */
    	/**
     	  * Call REST API for retrieving prediction from Azure ML 
     	  * @return response from the REST API
     	  */	
    	public static String rrsHttpPost() {
        
        	HttpPost post;
        	HttpClient client;
        	StringEntity entity;
        
        	try {
            		// create HttpPost and HttpClient object
            		post = new HttpPost(apiurl);
            		client = HttpClientBuilder.create().build();
            
            		// setup output message by copying JSON body into 
            		// apache StringEntity object along with content type
            		entity = new StringEntity(jsonBody, HTTP.UTF_8);
            		entity.setContentEncoding(HTTP.UTF_8);
            		entity.setContentType("text/json");

            		// add HTTP headers
            		post.setHeader("Accept", "text/json");
            		post.setHeader("Accept-Charset", "UTF-8");
        
            		// set Authorization header based on the API key
            		post.setHeader("Authorization", ("Bearer "+apikey));
            		post.setEntity(entity);

            		// Call REST API and retrieve response content
            		HttpResponse authResponse = client.execute(post);
            
            		return EntityUtils.toString(authResponse.getEntity());
            
        	}
        	catch (Exception e) {
            
            		return e.toString();
        	}
    
    	}
    
    	
 

### BES の例
RR サービスとは異なり、BES サービスは非同期です。つまり、BES API は単に実行対象のジョブをキューに登録し、呼び出し元はジョブの状態をポーリングして、完了すると表示します。バッチ ジョブで現在サポートされている操作を次に示します。

1. バッチ ジョブの作成 (送信)
1. そのバッチ ジョブの開始
1. バッチ ジョブの状態または結果の取得
1. 実行中のバッチ ジョブのキャンセル

**1.バッチ実行ジョブを作成する**

Azure Machine Learning サービス エンドポイントのバッチ ジョブを作成するときに、このバッチ実行を定義するさまざまなパラメーターを指定できます。

* **Input**: バッチ ジョブの入力が保存される BLOB 参照を表します。
* **GlobalParameters**: 実験用に定義できる一連のグローバル パラメーターを表します。Azure Machine Learning の実験では、サービスの実行をカスタマイズする必須パラメーターとオプション パラメーターを使用できます。該当する場合、呼び出し元は必要なすべての必須パラメーターを指定する必要があります。これらのパラメーターは、キーと値のペアのコレクションとして指定します。
* **Outputs**: サービスによって 1 つ以上の出力が定義されている場合、呼び出し元は Azure BLOB の場所にそれらをリダイレクトできます。これにより、サービスの出力を目的の場所に、予測可能な名前で保存できます。これを設定しなければ、出力 BLOB 名はランダムに生成されます。

    サービスでは、出力コンテンツのタイプに基づいて、サポートされる次の形式で出力コンテンツを保存する必要があることに注意してください。
  - データ セット出力: **.csv、.tsv、.arff** で保存可能
  - トレーニング済みモデル出力: **.ilearner** で保存可能

  出力先のオーバーライドは *<出力名, BLOB 参照>* ペアのコレクションとして指定されます。*出力名*は特定の出力ノードのユーザー定義の名前であり (サービスの API ヘルプ ページにも表示)、*BLOB 参照*は出力のリダイレクト先となる Azure BLOB の場所への参照です。

これらのすべてのジョブ作成パラメーターは、サービスの性質によっては省略できます。たとえば、入力ノードが定義されていないサービスでは、*Input* パラメーターを渡す必要はありません。同様に、出力先オーバーライド機能は完全に省略可能です。これは、この機能を設定しなければ、出力は Azure Machine Learning ワークスペース用に設定された既定のストレージ アカウントに保存されるからです。以下に、入力情報のみが提供されるサービスについて、REST API に渡されるサンプルの要求ペイロードを示します。

**要求のサンプル**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Outputs": null,
	  "GlobalParameters": null
	}

バッチ ジョブ作成 API への応答で、ジョブに関連付けられていた一意のジョブ ID が返されます。この ID は、他の操作に関してシステム内でこのジョブを参照するための唯一の手段であるため、非常に重要です。

**応答のサンプル**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2.バッチ実行ジョブを開始する**

バッチ ジョブを作成すると、システム内にジョブが登録され、状態が *Not started* に設定されます。ジョブが実行されるように実際にスケジュールを設定するには、サービス エンドポイントの API ヘルプ ページで説明されている **start** API を呼び出し、ジョブが作成されたときに取得されたジョブ ID を指定します。

**3.バッチ実行ジョブの状態を取得する**

ジョブの ID を GetJobStatus API に渡して、非同期バッチ ジョブの状態をいつでもポーリングできます。API 応答には、ジョブの現在の状態のインジケーターに加え、バッチ ジョブが正常に完了した場合は、その実際の結果も含まれます。エラーが発生した場合は、次に示すように、失敗の実際の理由に関する詳細情報が *Details* プロパティで返されます。

**応答ペイロード**

	{
	    "StatusCode": STATUS_CODE,
	    "Results": RESULTS,
	    "Details": DETAILS
	}

*StatusCode* は、次のいずれかになります。

* Not started
* 実行中
* Failed
* Cancelled
* Finished

*Results* プロパティには、ジョブが正常に完了した場合にのみデータが取り込まれます (それ以外は、**null** になります)。ジョブが完了し、サービスで少なくとも 1 つの出力ノードが定義されている場合、結果は *[出力名, BLOB 参照]* ペアのコレクションとして返されます。BLOB 参照は、実際の結果が格納されている BLOB への SAS 読み取り専用参照です。

**応答のサンプル**

	{
	    "Status Code": "Finished",
	    "Results":
	    {
	        "dataOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "outputs/dataOutput.csv",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },
	        "trainedModelOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "models/trainedModel.ilearner",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },           
	    },
	    "Details": null
	}

**4.バッチ実行ジョブをキャンセルする**

実行中のバッチ ジョブは、指定した CancelJob API を呼び出し、ジョブの ID で渡すことで、いつでもキャンセルできます。これは、ジョブが完了までにかかる時間が長すぎるなど、様々な理由で実行されます。



#### BES SDK の使用

[BES SDK Nugget パッケージ](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/)には、バッチ モードでスコア付けをするために BES を単に呼び出す機能が用意されています。Nuget パッケージをインストールするには、Visual Studio で **[ツール]** メニューの **[Nuget パッケージ マネージャー]** を選択し、**[パッケージ マネージャー コンソール]** をクリックします。

Web サービスとしてデプロイされる Azure Machine Learning の実験には、Web サービス入力モジュールを含めることができます。これは、実験では、入力が BLOB の場所への参照の形で Web サービスの呼び出しを通して提供されると予期していることを意味します。Web サービス入力モジュールを使用するのではなく、**データのインポート** モジュールを使用することもできます。この場合、**データのインポート** モジュールは、通常は実行時にクエリを使用して SQL DB から読み取りを行ってデータを取得します。Web サービス パラメーターを使用して、他のサーバーやテーブルなどを動的に指すことができます。SDK ではこれらの両方のパターンがサポートされています。

次のサンプル コードでは、BES SDK を使用して Azure Machine Learning サービス エンドポイントに対してバッチ ジョブのサブミットおよび監視を行う方法が示されています。設定と呼び出しの詳細については、コメントを確認してください。

#### **サンプル コード**

	// This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.Azure.MachineLearning

	  using System;
	  using System.Collections.Generic;
	  using System.Threading.Tasks;

	  using Microsoft.Azure.MachineLearning;
	  using Microsoft.Azure.MachineLearning.Contracts;
	  using Microsoft.Azure.MachineLearning.Exceptions;

	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {	            
	            InvokeBatchExecutionService().Wait();
	        }

	        static async Task InvokeBatchExecutionService()
	        {
	            // First collect and fill in the URI and access key for your web service endpoint.
	            // These are available on your service's API help page.
	            var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
	            string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";

	            // Create an Azure Machine Learning runtime client for this endpoint
	            var runtimeClient = new RuntimeClient(endpointUri, accessKey);

	            // Define the request information for your batch job. This information can contain:
	            // -- A reference to the AzureBlob containing the input for your job run
	            // -- A set of values for global parameters defined as part of your experiment and service
	            // -- A set of output blob locations that allow you to redirect the job's results

	            // NOTE: This sample is applicable, as is, for a service with explicit input port and
	            // potential global parameters. Also, we choose to also demo how you could override the
	            // location of one of the output blobs that could be generated by your service. You might
	            // need to tweak these features to adjust the sample to your service.
	            //
	            // All of these properties of a BatchJobRequest shown below can be optional, depending on
	            // your service, so it is not required to specify all with any request.  If you do not want to
	            // use any of the parameters, a null value should be passed in its place.

	            // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references
                    // based on a blob SAS URI

                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");

                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.

                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME",
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };

	            // If applicable, you can also set the global parameters for your service
	            var globalParameters = new Dictionary<string, string>
	            {
	                { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
	            };

	            var jobRequest = new BatchJobRequest
	            {
	                Input = inputBlob,
	                GlobalParameters = globalParameters,
	                Outputs = outputLocations
	            };

	            try
	            {
	                // Register the batch job with the system, which will grant you access to a job object
	                BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);

	                // Start the job to allow it to be scheduled in the running queue
	                await job.StartAsync();

	                // Wait for the job's completion and handle the output
	                BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
	                if (jobStatus.JobState == JobState.Finished)
	                {
	                    // Process job outputs
	                    Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
	                    foreach (var output in jobStatus.Results)
	                    {
	                        Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
	                    }
	                }
	                else if (jobStatus.JobState == JobState.Failed)
	                {
	                    // Handle job failure
	                    Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
	                }
	            }
	            catch (ArgumentException aex)
	            {
	                Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
	            }
	            catch (RuntimeException runtimeError)
	            {
	                Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
	                Console.WriteLine("Error details:");
	                foreach (var errorDetails in runtimeError.Details)
	                {
	                    Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
	                }
	            }
	            catch (Exception ex)
	            {
	                Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
	            }
	        }
	    }
	}

#### Java for BES のコード サンプル
バッチ実行サービス REST API では、下の図のように入力サンプル csv と出力サンプル csv への参照から構成される JSON を取得し、バッチ予測を実行するジョブを Azure ML で作成します。完全なコードは [Github](https://github.com/nk773/AzureML_BESApp/tree/master/src/azureml_besapp) で確認できます。この Java サンプルには、[apache http クライアント ライブラリ](https://hc.apache.org/downloads.cgi)が必要です。


	{ "GlobalParameters": {}, 
    	"Inputs": { "input1": { "ConnectionString": 	"DefaultEndpointsProtocol=https;
			AccountName=myAcctName; AccountKey=Q8kkieg==", 
        	"RelativeLocation": "myContainer/sampleinput.csv" } }, 
    	"Outputs": { "output1": { "ConnectionString": 	"DefaultEndpointsProtocol=https;
			AccountName=myAcctName; AccountKey=kjC12xQ8kkieg==", 
        	"RelativeLocation": "myContainer/sampleoutput.csv" } } 
	} 


#####BES ジョブを作成する	
	    
	    /**
	     * Call REST API to create a job to Azure ML 
	     * for batch predictions
	     * @return response from the REST API
	     */	
	    public static String besCreateJob() {
	        
	        HttpPost post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpPost(apiurl);
	            client = HttpClientBuilder.create().build();
	            
	            // setup output message by copying JSON body into 
	            // apache StringEntity object along with content type
	            entity = new StringEntity(jsonBody, HTTP.UTF_8);
	            entity.setContentEncoding(HTTP.UTF_8);
	            entity.setContentType("text/json");
	
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
				// note a space after the word "Bearer " - don't miss that
	            post.setHeader("Authorization", ("Bearer "+apikey));
	            post.setEntity(entity);
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	            
	            jobId = EntityUtils.toString(authResponse.getEntity()).replaceAll(""", "");
	            
	            
	            return jobId;
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    
	    }
	    
#####以前に作成した BES ジョブを開始する	        
	    /**
	     * Call REST API for starting prediction job previously submitted 
	     * 
	     * @param job job to be started 
	     * @return response from the REST API
	     */	
	    public static String besStartJob(String job){
	        HttpPost post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpPost(startJobUrl+"/"+job+"/start?api-version=2.0");
	            client = HttpClientBuilder.create().build();
	         
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
	            post.setHeader("Authorization", ("Bearer "+apikey));
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	            
	            if (authResponse.getEntity()==null)
	            {
	                return authResponse.getStatusLine().toString();
	            }
	            
	            return EntityUtils.toString(authResponse.getEntity());
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    }
#####以前に作成した BES ジョブをキャンセルする
	    
	    /**
	     * Call REST API for canceling the batch job 
	     * 
	     * @param job job to be started 
	     * @return response from the REST API
	     */	
	    public static String besCancelJob(String job) {
	        HttpDelete post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpDelete(startJobUrl+job);
	            client = HttpClientBuilder.create().build();
	         
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
	            post.setHeader("Authorization", ("Bearer "+apikey));
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	         
	            if (authResponse.getEntity()==null)
	            {
	                return authResponse.getStatusLine().toString();
	            }
	            return EntityUtils.toString(authResponse.getEntity());
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    }
	    
###その他のプログラミング環境
API ヘルプ ページの swagger ドキュメントを使用し、[swagger.io](http://swagger.io/) サイトの指示に従うことで、他のさまざまな言語でコードを生成することもできます。[swagger.io](http://swagger.io/swagger-codegen/) にアクセスし、指示に従って、swagger コード、java、apache mvn をダウンロードします。以下は、他のプログラミング言語で swagger を設定する方法のまとめです。

* Java 7 以降がインストールされていることを確認する
* apache mvn をインストールする (ubuntu の場合、*apt-get install mvn* を使用)
* swagger の github にアクセスし、swagger プロジェクトを zip ファイルとしてダウンロードする
* swagger を解凍する
* swagger のソース ディレクトリから *mvn パッケージ*を実行して、swagger ツールを構築する

これですべての swagger ツールを使用できます。Java クライアント コードを生成する手順は次のとおりです。

* Azure ML API ヘルプ ページに移動する (たとえば、[こちら](https://studio.azureml.net/apihelp/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/jobs)から)
* Azure ML REST API 用の swagger.json の URL を見つける (API ヘルプ ページの上部の最後から 2 番目の箇条書き)
* swagger ドキュメント リンクをクリックする (たとえば、[こちら](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument)から)
* [swagger の Read Me ファイル](https://github.com/swagger-api/swagger-codegen/blob/master/README.md)に示されている次のコマンドを使用して、クライアント コードを生成する

**クライアント コードを生成するサンプル コマンド ライン**

	java -jar swagger-codegen-cli.jar generate\
	 -i https://ussouthcentral.services.azureml.net:443/workspaces/\
	fb62b56f29fc4ba4b8a8f900c9b89584/services/26a3afce1767461ab6e73d5a206fbd62/swagger.json\
	 -l java -o /home/username/sample

* 次に示す swagger [API ヘルプ ページ](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument)のサンプルにある "host"、"basePath"、"/swagger.json" の各フィールドの値を組み合わせて、上記のコマンド ラインで使用されている swagger URL を作成する

**サンプル API ヘルプ ページ**


	{
	  "swagger": "2.0",
	  "info": {
	    "version": "2.0",
	    "title": "Sample 5: Binary Classification with Web Service: Adult Dataset [Predictive Exp.]",
	    "description": "No description provided for this web service.",
	    "x-endpoint-name": "default"
	  },
	  "host": "ussouthcentral.services.azureml.net:443",
	  "basePath": "/workspaces/afbd553b9bac4c95be3d040998943a4f/services/26a3afce1767461ab6e73d5a206fbd62",
	  "schemes": [
	    "https"
	  ],
	  "consumes": [
	    "application/json"
	  ],
	  "produces": [
	    "application/json"
	  ],
	  "paths": {
	    "/swagger.json": {
	      "get": {
	        "summary": "Get swagger API document for the web service",
	        "operationId": "getSwaggerDocument",
	        

<!---HONumber=AcomDC_0914_2016-->