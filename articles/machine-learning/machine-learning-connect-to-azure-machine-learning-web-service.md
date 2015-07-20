<properties 
	pageTitle="Machine Learning Web サービスを発行する | Microsoft Azure" 
	description="C# または Python を使用して、Azure Machine Learning Web サービスに承認キーを利用して接続します。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="derrickv" />


# Azure Machine Learning Web サービスに接続する 
開発者が体験する Azure Machine Learning は、入力データから予測をリアルタイムまたはバッチ モードで作成する Web サービス API です。Azure Machine Learning Studio を使用して予測を作成し、Azure Machine Learning Web サービスを発行します。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Studio を使用して Azure Machine Learning Web サービスを作成して発行する方法の詳細については、次をご覧ください。

- [Machine Learning Web サービスを発行する](machine-learning-publish-a-machine-learning-web-service.md)
- [ML Studio を使ってみる](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Azure Machine Learning のプレビュー](https://studio.azureml.net/)
- [Machine Learning ドキュメント センター](http://azure.microsoft.com/documentation/services/machine-learning/)

## Azure Machine Learning Web サービス ##

Azure Machine Learning (ML) Web サービスを使用して、外部のアプリケーションが ML のワークフローのスコア付けモデルとリアルタイムで通信します。ML Web サービスの呼び出しは、予測結果を外部のアプリケーションに返します。ML Web サービスの呼び出しを実行するために、予測発行時に作成される API キーを渡します。ML Web サービスは、Web プログラミング プロジェクトでよく選択されるアーキテクチャの REST に基づいています。

Azure Machine Learning には、2 種類のサービスがあります。

- 要求応答サービス (RRS) – 待ち時間が短く拡張性の高い、ML Studio から作成および発行されたステートレスなモデルへのインターフェイスを提供するサービス。
- バッチ実行サービス (BES) – データ レコードのバッチをスコア付けする非同期のサービス。

Azure Machine Learning Web サービスの詳細については、「[Machine Learning Web サービスを発行する](machine-learning-publish-a-machine-learning-web-service.md)」をご覧ください。

## Azure Machine Learning の承認キーを取得する ##
Web サービスの API キーを ML Web サービスから取得します。Microsoft Azure Machine Learning Studio または Azure 管理ポータルから取得できます。
### Microsoft Azure Machine Learning Studio ###
1. Microsoft Azure Machine Learning Studio で、左側の **[Web サービス]** をクリックします。
2. Web サービスをクリックします。**[ダッシュボード]** タブに [API キー] があります。

### Azure 管理ポータル ###

1. 左側の **[Machine Learning]** をクリックします。
2. ワークスペースをクリックします。
3. **[Web サービス]** をクリックします。
4. Web サービスをクリックします。
5. エンドポイントをクリックします。[API キー] が右下にあります。

## <a id="connect"></a>Azure Machine Learning Web サービスに接続する

HTTP 要求と応答をサポートする任意のプログラミング言語を使用して、Azure Machine Learning Web サービスに接続することができます。Azure ML Web サービス ヘルプ ページから、C#、Python、および R の例を表示できます。

### Azure ML Web サービス API のヘルプ ページを表示するには ###
Azure ML API ヘルプ ページは、Web サービスを発行するときに作成されます。「[Azure Machine Learning チュートリアル - Web サービスを発行する](machine-learning-walkthrough-5-publish-web-service.md)」を参照してください。


**Azure ML API のヘルプ ページを表示するには、**Microsoft Azure Machine Learning Studio で次のようにします。

1. **[Web サービス]** を選択します。
2. Web サービスを選択します。
3. **[API ヘルプ ページ]** - **[要求/応答]** または **[バッチの実行]** を選択します。


**Azure ML API のヘルプ ページ** Azure ML API のヘルプ ページには、次のような予測 Web サービスに関する詳細が含まれています。


<table>
	<tr>
		<td>&#160;</td>
		<td>例 </td>
	</tr>
	<tr>
		<td>POST URI 要求 </td>

		<td>https://ussouthcentral.services.azureml.net/workspaces/{WorkspaceId}/services/{ServiceId}/score
		</td>
	</tr>
	<tr>
		<td>要求のサンプル </td>
		<td>{ <br/> 
			&#160;&#160; "Id": "score00001",  <br/>
			&#160;&#160; "Instance": <br/>
			&#160;&#160;&#160;&#160; {  <br/>  
 			&#160;&#160;&#160;&#160; &#160;&#160; "FeatureVector": { <br/>
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col1": "0",<br/>      
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col2": "0",<br/>      
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col3": "0",<br/>  
			&#160;&#160;&#160;&#160; &#160;&#160;  ... },  <br/>
			&#160;&#160;&#160;&#160;   "GlobalParameters": {}   <br/>
			&#160;&#160;&#160;&#160; { <br/>
		{</td>
	</tr>
	<tr>
		<td>応答本文 </td>
		<td>
		<table style="width: 100%">

			<tr>
				<td><B>名前</B></td>
				<td><B>データ型</B></td>
			</tr>
	
			<tr>
				<td>機能</td>
				<td>String</td>
			</tr>
			<tr>
				<td>カウント</td>
				<td>数値</td>
			</tr>
			<tr>
				<td>一意の値の数 </td>
				<td>数値 </td>
			</tr>
			<tr>
				<td>...</td>
				<td>...</td>
			</tr>
		</table>
		</td>
	</tr>
	<tr>
		<td>応答のサンプル </td>
		<td>["Col1","1","1",…] </td>
	</tr>
	<tr>
		<td>サンプル コード </td>
		<td>(C#、Python、および R のサンプル コード) </td>
	</tr>
</table>

**注** これらの例は、Azure ML のサンプル コレクションに含まれる「サンプル 1: UCI からデータセットをダウンロード: 成人向け 2 クラス データセット」からのものです。

### C# のサンプル ###

Azure ML Web サービスに接続するには、ScoreData を渡す **HttpClient** を使用します。ScoreData には、ScoreData を表す数値機能の n 次元ベクトルである FeatureVector が含まれています。API キーを使用して、Azure ML サービスを認証できます。

ML Web サービスに接続するには、**Microsoft.AspNet.WebApi.Client** Nuget パッケージをインストールする必要があります。

**Microsoft.AspNet.WebApi.Client Nuget in Visual Studio をインストールする**

1. 「UCI からデータセットをダウンロード: 成人向け 2 クラス データセット」Web サービスを発行します。
2. **[ツール]** > **[Nuget パッケージ マネージャー]** > **[Package Manager Console]** をクリックします。
2. **[Install-Package Microsoft.AspNet.WebApi.Client]** を選択します。

**サンプル コードを実行するには**

1. Azure ML サンプル コレクションに含まれる「サンプル 1: UCI からデータセットをダウンロード: 成人向け 2 クラス データセット」実験を発行します。
2. Web サービスからのキーを持つ apiKey を割り当てます。Azure の ML 承認キーを取得する方法を参照してください。
3. 要求の URI を含む serviceUri を割り当てます。要求の URI を取得する方法を参照してください。

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
	    public class ScoreData
	    {
	        public Dictionary<string, string> FeatureVector { get; set; }
	        public Dictionary<string, string> GlobalParameters { get; set; }
	    }
	
	    public class ScoreRequest
	    {
	        public string Id { get; set; }
	        public ScoreData Instance { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	
	            Console.ReadLine();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            //Assign apiKey with the key from a web service.
	            const string apiKey = "{ApiKey}";
	
	            //Assign serviceUri with the Request URI. See How to get a Request URI.
	            const string serviceUri = "{ServiceUri}";
	            
	            using (var client = new HttpClient())
	            {
	                ScoreData scoreData = new ScoreData()
	                {
	                    //Input data
	                    FeatureVector = new Dictionary<string, string>() 
	                    {
	                        { "Col1", "0" },
	                        { "Col2", "0" },
	                        { "Col3", "0" },
	                        { "Col4", "0" },
	                        { "Col5", "0" },
	                        { "Col6", "0" },
	                        { "Col7", "0" },
	                        { "Col8", "0" },
	                        { "Col9", "0" },
	                        { "Col10", "0" },
	                        { "Col11", "0" },
	                        { "Col12", "0" },
	                        { "Col13", "0" },
	                        { "Col14", "0" },
	                        { "Col15", "0" },
	                    },
	                    GlobalParameters = 
	                        new Dictionary<string, string>() {}
	                };
	
	                ScoreRequest scoreRequest = new ScoreRequest()
	                {
	                    Id = "score00001",
	                    Instance = scoreData
	                };
	
	                //Set authorization header
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	             
	                client.BaseAddress = new Uri(serviceUrl);
	
	                //Post HTTP response message
	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    //Read result string
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


### Python サンプル ###

Azure ML Web サービスに接続するには、ScoreData を渡す **urllib2** ライブラリを使用します。ScoreData には、ScoreData を表す数値機能の n 次元ベクトルである FeatureVector が含まれています。API キーを使用して、Azure ML サービスを認証できます。


**サンプル コードを実行するには**

1. Azure ML サンプル コレクションに含まれる「サンプル 1: UCI からデータセットをダウンロード: 成人向け 2 クラス データセット」実験を発行します。
2. Web サービスからのキーを持つ apiKey を割り当てます。Azure の ML 承認キーを取得する方法を参照してください。
3. 要求の URI を含む serviceUri を割り当てます。要求の URI を取得する方法を参照してください。

		import urllib2
		# If you are using Python 3+, import urllib instead of urllib2
	
		import json 
	
		data =  {
	            "Id": "score00001",
	            "Instance": {
	                "FeatureVector": {
	                    "Col1": "0",
	                    "Col2": "0",
	                    "Col3": "0",
	                    "Col4": "0",
	                    "Col5": "0",
	                    "Col6": "0",
	                    "Col7": "0",
	                    "Col8": "0",
	                    "Col9": "0",
	                    "Col10": "0",
	                    "Col11": "0",
	                    "Col12": "0",
	                    "Col13": "0",
	                    "Col14": "0",
	                    "Col15": "0",
	                },
	                "GlobalParameters": { }
	            }
	        }
	
		body = str.encode(json.dumps(data))
	
		#Assign serviceUrl with the Request URI. See How to get a Request URI.
		uri = '{ServiceUri}'
	
		#Assign apiKey with the key from a web service.
		api_key = '{ApiKey}'
		headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	
		req = urllib2.Request(uri, body, headers) 
		response = urllib2.urlopen(req)
	
		#If you are using Python 3+, replace urllib2 with urllib.request in the above code:
		#req = urllib.request.Request(uri, body, headers) 
		#response = urllib.request.urlopen(req)
	
		result = response.read()
		print(result) 
 

<!---HONumber=July15_HO2-->