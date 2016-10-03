<properties
	pageTitle="プログラムによる Machine Learning のモデルの再トレーニング | Microsoft Azure"
	description="Azure Machine Learning でプログラムによるモデルの再トレーニングをしてWeb サービスを更新し、新しくトレーニングを行ったモデルを使用する方法について説明します。"
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="raymondl;garye;v-donglo"/>


#プログラムによる Machine Learning のモデルの再トレーニング  

Azure Machine Learning における Machine Learning のモデル運用プロセスの一環として、モデルのトレーニングと保存が行われます。その後、このモデルを使用して、予測 Web サービスを作成します。これによって、Web サイト、ダッシュボード、モバイル アプリでこの Web サービスを使用できます。

Machine Learning を使って作成するモデルは、通常、静的ではありません。新しいデータが使用可能になるか、API のコンシューマーに独自のデータがある場合は、モデルを再トレーニングする必要があります。場合によっては、フィルターを適用してデータのサブセットを取得したうえで、モデルを再トレーニングする必要があります。

再トレーニングは頻繁に発生する可能性があります。再トレーニング API を使用して、モデルをプログラムによって再トレーニングし、新しくトレーニングされたモデルで Web サービスを更新できます。

このドキュメントでは再トレーニングのプロセスについて説明し、再トレーニング API を使用する方法を示します。

## 再トレーニングを行う理由: 問題の定義  

ML のトレーニング プロセスの一環として、モデルのトレーニングではデータのセットを使用します。Machine Learning を使って作成するモデルは、通常、静的ではありません。新しいデータが使用可能になるか、API のコンシューマーに独自のデータがある場合は、モデルを再トレーニングする必要があります。また、フィルターを適用してデータのサブセットを取得したうえで、モデルを再トレーニングしなければならないシナリオもあります。

これらのシナリオでは、開発者または API のコンシューマーはプログラムによる API を通じて簡単にクライアントを作成でき、独自のデータを使用して 1 回のみまたは定期的に、モデルの再トレーニングを行うことができます。その後再トレーニングの結果を評価し、さらに Web サービス API を更新して、新しくトレーニングを行ったモデルを使用できます。

##再トレーニングを行う方法: エンド ツー エンドのプロセス  

このプロセスには、まず Web サービスとして発行されたトレーニング実験と予測実験が必要です。トレーニング済みのモデルを再トレーニングできるようにするには、トレーニング済みのモデルの出力で、トレーニング実験を Web サービスとして発行する必要があります。これにより、モデルへの API アクセスが有効になり再トレーニングを行うことができます。

再トレーニングを設定するプロセスには、次の手順が含まれます。

![再トレーニング プロセスの概要][1]

図 1: 再トレーニング プロセスの概要

## トレーニング実験を作成する
 
この例では、Microsoft Azure Machine Learning のサンプル "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" を使用します。
	
実験を作成するには:

1.	Microsoft Azure Machine Learning Studio にサインインします。
2.	ダッシュボードの右下隅の下部にある **[新規]** をクリックします。
3.	Microsoft サンプルから、[Sample 5] を選択します。
4.	実験の名前を変更するには、実験キャンバスの上部にある実験名 [Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset] を選択します。
5.	「Census Model」と入力します。
6.	実験キャンバスの下部で、**[実行]** をクリックします。
7.	**[Set Up Web Service (Web サービスの設定)]** をクリックして、**[Retraining Web Service (Web サービスの再トレーニング)]** を選択します。

 	![最初の実験。][2]

図 2: 最初の実験。

## スコア付け実験を作成して Web サービスとして発行する  

次に、予測実験を作成します。

1.	実験キャンバスの下部で、**[Set Up Web Service (Web サービスの設定)]** をクリックして **[Predictive Web Service (予測 Web サービス)]** を選択します。これによりモデルがトレーニング済みのモデルとして保存され、Web サービス入力モジュールと Web サービス出力モジュールが追加されます。
2.	**[実行]** をクリックします。
3.	実験の実行が完了したら、**[Deploy Web Service [Classic] \(Web サービスのデプロイ [従来])]** をクリックします。これにより、予測実験が従来の Web サービスとしてデプロイされます。

## トレーニング実験をトレーニング Web サービスとしてデプロイする

トレーニング済みのモデルを再トレーニングするには、再トレーニング Web サービスとして作成したトレーニング実験をデプロイする必要があります。この Web サービスでは、["トレーニング モデル"][train-model] モジュールにつながっている "Web サービス出力" モジュールが必要です。これにより新しいトレーニング済みのモデルを生成できます。

1. トレーニング実験に戻るには、左側のウィンドウの実験アイコンをクリックし、Census Model という名前の実験をクリックします。
2. [Search Experiment Items (実験項目の検索)] 検索ボックスに、「Web サービス」と入力します。
3. Web サービス入力モジュールを実験キャンバスにドラッグし、その出力を、見つからないデータのクリーンアップ モジュールに接続します。
4. 2 つの "*Web サービス出力*" モジュールを実験キャンバスにドラッグします。"*モデルのトレーニング*" モジュールの出力を一方に、"*モデルの評価*" モジュールの出力をもう一方に接続します。モデルのトレーニングの Web サービス出力により、新しいトレーニング済みのモデルを使用できます。モデルの評価に関連付けられている出力は、そのモジュールのモデルの評価出力を返します。
5. **[実行]** をクリックします。
6. 実験キャンバスの下部で **[Set Up Web Service (Web サービスの設定)]** をクリックし、**[Retraining Web Service (Web サービスの再トレーニング)]** を選択します。これで、トレーニング済みモデルとモデル評価の結果を生成するトレーニング実験が Web サービスとしてデプロイされます。Web サービス **ダッシュボード**が、API キーとバッチ実行用 API ヘルプ ページとともに表示されます。トレーニング済みのモデルの作成に使用できるのはバッチ実行メソッドのみです。
  
実験の実行が完了すると、ワークフローは次のようになります。

![実行後のワークフロー。][4]

図 3: 実行後のワークフロー。

## 新しいエンドポイントを追加する
 
デプロイした予測 Web サービスは、既定のスコア付けエンドポイントです。既定のエンドポイントは、元のトレーニングおよびスコア付け実験との同期が維持されるため、既定のエンドポイントのトレーニング済みモデルは置き換えることができません。

新しいスコア付けエンドポイントを作成するには、トレーニング済みモデルで更新できる予測 Web サービスで次の操作を行います。

>[AZURE.NOTE] エンドポイントは、必ず予測 Web サービスに追加します。トレーニング Web サービスではありません。トレーニング Web サービスと予測 Web サービスの両方を正しくデプロイすると、2 つの Web サービスが個別に表示されます。予測 Web サービスの末尾は "[predictive exp.]" です。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にログインします。
2. 左側のメニューで **[Machine Learning]** をクリックします。
3. [名前] でワークスペースをクリックし、**[Web サービス]** をクリックします。
4. [名前] で **[Census Model [predictive exp.]]** をクリックします。
5. ページの下部にある **[エンドポイントの追加]** をクリックします。エンドポイントの追加の詳細については、「[エンドポイントを作成する](machine-learning-create-endpoint.md)」をご覧ください。

[GitHub リポジトリ](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs)で提供されているサンプル コードを使用して、スコア付けエンドポイントを追加することもできます。

## BES を使用して新しいデータでモデルを再トレーニングする

再トレーニング API を呼び出すには:

1. Visual Studio で C# コンソール アプリケーションを作成します ([新規] -> [プロジェクト] -> [Windows デスクトップ] -> [コンソール アプリケーション])。
2. トレーニング Web サービスのバッチ実行用 API ヘルプ ページから C# のサンプル コードをコピーして、Program.cs ファイルに貼り付けます。このとき、名前空間を変更しないように注意します。
3. サンプル コードには、更新が必要なコードの箇所を示すコメントが含まれます。
4. 要求ペイロードで出力場所を指定する場合は、*RelativeLocation* で指定されたファイルの拡張子を、csv から ilearner に変更する必要があります。次の例を参照してください。

		Outputs = new Dictionary<string, AzureBlobDataReference>()
		{
			{
				"output1",
				new AzureBlobDataReference()
				{
					ConnectionString = "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==",
					RelativeLocation = "mycontainer/output1results.ilearner"
				}
			},
		},

>[AZURE.NOTE] 出力場所の名前は、このチュートリアルで使用されている名前とは異なる場合があります。これは、Web サービス出力モジュールを追加した順序に基づいています。このトレーニング実験は 2 つの出力で設定したため、結果には、両方のストレージの場所に関する情報が含まれています。

### Azure Storage 情報を更新する

BES サンプル コードは、ファイルをローカル ドライブ ("C:\\temp\\CensusIpnput.csv" など) から Azure Storage にアップロードして処理し、その結果を Azure Storage に書き込みます。

このタスクを完了するには、ストレージ アカウント用 Azure クラシック ポータルでストレージ アカウント名、キー、コンテナー情報を取得して、コード内の対応する値を更新する必要があります。

また、入力ファイルがコードで指定した場所で有効であることを確認する必要があります。

![再トレーニング出力][6]

図 4: 再トレーニング出力。

## 再トレーニングの結果を評価する
 
アプリケーションを実行すると、評価結果へのアクセスに必要な URL と SAS トークンが出力に示されます。

再トレーニング済みモデルのパフォーマンス結果を確認するには、*output2* の出力結果 (前の再トレーニング出力の画像を参照) の *BaseLocation*、*RelativeLocation*、*SasBlobToken* を組み合わせて、ブラウザーのアドレス バーに完全な URL を貼り付けます。

この結果で、新しくトレーニングされたモデルが、既存のモデルに代わるのに十分なパフォーマンスを発揮しているかどうかを確認します。

## 追加エンドポイントのトレーニング済みモデルを更新する

再トレーニング プロセスを完了するには、追加した新しいエンドポイントのトレーニング済みモデルを更新する必要があります。

* Azure ポータルを使用して新しいエンドポイントを追加した場合、Azure ポータルで新しいエンドポイントの名前をクリックしてから、**UpdateResource** リンクをクリックすると、エンドポイントのモデルを更新するときに必要な URL を入手できます。
* サンプル コードを使用してエンドポイントを追加した場合は、*HelpLocationURL* 値によって特定されたヘルプ URL の場所が出力に追加されます。

パスの URL を取得するには:

1. URL をコピーし、ブラウザーに貼り付けます。
2. [リソースの更新] リンクをクリックします。
3. PATCH 要求の POST URL をコピーします。次に例を示します。

		PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

これで、トレーニング済みモデルを使用して、前に作成したスコア付けエンドポイントを更新できます。

次のサンプル コードは、*BaseLocation*、*RelativeLocation*、*SasBlobToken*、および PATCH URL を使用して、エンドポイントを更新する方法を示しています。

	private async Task OverwriteModel()
	{
		var resourceLocations = new
		{
			Resources = new[]
			{
				new
				{
					Name = "Census Model [trained model]",
					Location = new AzureBlobDataReference()
					{
						BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
						RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
						SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
					}
				}
			}
		};

		using (var client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

			using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
			{
				request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
				HttpResponseMessage response = await client.SendAsync(request);

				if (!response.IsSuccessStatusCode)
				{
					await WriteFailedResponse(response);
				}

				// Do what you want with a successful response here.
			}
		}
	}

この呼び出しの *apiKey* と *endpointUrl* は、エンドポイント ダッシュボードから取得できます。

*Resources* の *Name* パラメーターは、予測実験で保存したトレーニング済みモデルのリソース名と一致する必要があります。リソース名を取得するには:

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にログインします。
2. 左側のメニューで **[Machine Learning]** をクリックします。
3. [名前] でワークスペースをクリックし、**[Web サービス]** をクリックします。
4. [名前] で **[Census Model [predictive exp.]]** をクリックします。
5. 追加した新しいエンドポイントをクリックします。
6. エンドポイント ダッシュボードで、*[リソースの更新]* をクリックします。
7. Web サービスの [Update Resource API Documentation (リソース API ドキュメントの更新)] ページで、**[Updatable Resource (更新可能なリソース)]** の **[リソース名]** を確認します。

エンドポイントの更新が完了する前に SAS トークンの有効期限が切れる場合は、ジョブ ID で GET を実行し、新しいトークンを取得する必要があります。

コードが正常に実行すると、新しいエンドポイントは、約 30 秒後に再トレーニング済みモデルを使用し始めます。

##概要  
再トレーニング API を使用して、予測 Web サービスのトレーニング済みモデルを更新すると、次のようなシナリオを有効にできます。

* 新しいデータでの定期的なモデルの再トレーニング。
* 自身のデータでモデルを再トレーニングすることを目標としている顧客へのモデルの配布。

## 次のステップ
[Troubleshooting the retraining of an Azure Machine Learning classic web service (Azure Machine Learning の従来の Web サービスにおける再トレーニングに関するトラブルシューティング)](machine-learning-troubleshooting-retraining-models.md)

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0914_2016-->