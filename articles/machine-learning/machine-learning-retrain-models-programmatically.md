<properties
	pageTitle="プログラムによる Machine Learning のモデルの再トレーニング | Microsoft Azure"
	description="Azure Machine Learning でプログラムによるモデルの再トレーニングをしてWeb サービスを更新し、新しくトレーニングを行ったモデルを使用する方法について説明します。"
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="raymondl;garye"/>


#プログラムによる Machine Learning のモデルの再トレーニング  

Azure Machine Learning における Machine Learning のモデル運用プロセスの一環として、モデルのトレーニングと保存を行う必要があります。このモデルは、予測 Web サービスの作成に使用されます。これによって、Web サイト、ダッシュボード、モバイル アプリでこの Web サービスを使用できます。

多くの場合、新しいデータを使って最初の手順で作成したモデルで再トレーニングを行う必要があります。これはこれまで Azure ML UI によってのみできることでしたが、プログラムによる再トレーニング API 機能を導入することで、モデルの再トレーニングと Web サービスの更新ができるようになり、プログラムによる再トレーニング API を使って、新しくトレーニングを行ったモデルを使用できるようになりました。

このドキュメントでは上記のプロセスについて説明し、再トレーニング API を使用する方法を示します。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##再トレーニングを行う理由: 問題の定義  
ML のトレーニング プロセスの一環として、モデルのトレーニングではデータのセットを使用します。モデルの再トレーニングが必要とされるのは、新しいデータが有効になったシナリオや、API のコンシューマーがモデルのトレーニングを行う独自のデータを持っている場合、データをフィルター処理する必要があり、そのデータのサブセットでモデルのトレーニングを行う場合などです。

これらのシナリオでは、開発者または API のコンシューマーはプログラムによる API を通じて簡単にクライアントを作成でき、独自のデータを使用して 1 回のみまたは定期的に、モデルの再トレーニングを行うことができます。その後再トレーニングの結果を評価し、さらに Web サービス API を更新して、新しくトレーニングを行ったモデルを使用できます。

##再トレーニングを行う方法: エンド ツー エンドのプロセス  
このプロセスには、まず Web サービスとして発行されたトレーニング実験と予測実験が必要です。トレーニング済みのモデルを再トレーニングできるようにするには、トレーニング済みのモデルの出力で、トレーニング実験を Web サービスとして発行する必要があります。これにより、モデルへの API アクセスが有効になり再トレーニングを行うことができます。再トレーニングを設定するプロセスには、次の手順が含まれます。

![][1]

図 1: 再トレーニング プロセスの概要

1. *トレーニング実験を作成する* このサンプルでは Azure ML のサンプル実験の "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" という実験を使います。次の図では、一部のモジュールを省略してサンプルを簡略化しています。また、この実験の名前を "Census Model" にします。

 	![][2]

	これらの要素がそろえば、画面の下部にある [実行] をクリックして、この実験を実行できます。  
2. *スコア付け実験を作成して Web サービスとして発行する*  

	![][3]

	実験の実行が完了したら、[Create Predicative Experiment (予測実験の作成)] をクリックします。これにより予測実験が作成され、モデルはトレーニング済みのモデルとして保存され、次に示すように "Web Service Input (Web サービス入力)" と "Web Service Output (Web サービス出力)" のモジュールが追加されます。次に、[実行] をクリックします。

	実験の実行が完了した後で [Publish Web Service] をクリックすると、予測実験が Web サービスとして実験の実行が完了した後で [Publish Web Service] をクリックすると、スコア付け実験が Web サービスとして発行され、既定のエンドポイントが作成されます。発行され、既定のエンドポイントが作成されます。この Web サービスのトレーニング済みのモデルは更新できます。以下をご覧ください。これで、このエンドポイントの詳細が画面に表示されます。  
3. *トレーニング実験を Web サービスとして発行する* トレーニング済みのモデルの再トレーニングを行うには、上記の手順 1 で Web サービスとして作成したトレーニング実験を発行する必要があります。この Web サービスでは、["トレーニング モデル"][train-model] モジュールにつながっている "Web サービス出力" モジュールが必要です。これにより新しいトレーニング済みのモデルを生成できます。左側のウィンドウの実験アイコンをクリックし、"Census Model" という名前の実験をクリックしてトレーニング実験に戻ります。  

	次に、1 個の "Web サービス入力" モジュールと 2 個の "Web サービス出力" モジュールをワークフローに追加します。"トレーニング モデル" 用の Web サービス出力により、新しいトレーニング済みのモデルを使用できます。"モデルの評価" につながっている出力は、モジュールにおける "モデルの評価" の出力を返します。

	ここまで完了したら [実行] をクリックします。実験の実行が完了すると、ワークフローは次のようになります。

	![][4]

	次に [Deploy Web Service (Web サービスのデプロイ)] ボタン、[Yes (はい)] の順にクリックします。これでトレーニング実験が Web サービスとしてデプロイされ、トレーニング済みのモデルとモデル評価の結果を生成します。Web サービス ダッシュボードが、API キーとバッチ実行用 API ヘルプ ページとともに表示されます。トレーニング済みのモデルの作成に使用できるのはバッチ実行メソッドのみです。  
4. *新しいエンドポイントを追加する* 上記の手順 2 で発行した予測 Web サービスは、既定のエンドポイントで作成されました。既定のエンドポイントは元のトレーニングスコア付け実験との同期が維持されるため、既定のエンドポイントにおけるトレーニング済みのモデルを置き換えることはできません。更新できるエンドポイントを作成するには、Azure クラシック ポータルにアクセスし [エンドポイントの追加] をクリックします (詳細は[こちら](machine-learning-create-endpoint.md))。

5. *新しいデータと BES によりモデルの再トレーニングを行う* 再トレーニング API を呼び出せるように、Visual Studio で新しい C# コンソール アプリケーションが作成されました ([新規] > [プロジェクト] > [Windows デスクトップ] > [コンソール アプリケーション])。

	次にトレーニング Web サービスのバッチ実行用 API ヘルプ ページ (上記の手順 3 で作成) から C# のコード サンプルをコピーして、Program.cs ファイルに貼り付けます。このとき、名前空間を変更しないように注意します。

	コード サンプルには、更新が必要なコードの箇所を示すコメントが含まれます。また、Reqeust Payload で "output1" の場所を指定する場合、次に示すように、"RelativeLocation" のファイル拡張子を ".ilearner" に変更する必要があります。

	```c#
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
	```
	1. Azure Storage の情報を提供する。BES 用コード サンプルは、ローカル ドライブのファイル (たとえば "C:\\temp\\CensusIpnput.csv") を Azure Storage にアップロードして、処理し、結果を Azure Storage に戻して書き込みます。  

		これを完了するには、ストレージ アカウント用 Azure クラシック ポータルでストレージ アカウント名、キー、コンテナー情報を取得して、このコードを更新する必要があります。また、入力ファイルがコードで指定した場所で有効であることを確認する必要があります。

		トレーニング実験を 2 個の出力で設定したことで、次のように、結果に双方のストレージの場所情報が含まれます。"output1" はトレーニング済みのモデルの出力であり、"output2" は "モデルの評価" の出力です。また、トレーニング済みのモデルの出力 (Output1) のファイル拡張子が ".csv" ではなく ".ilearner" であることに注意してください。

		![][6]

6. *再トレーニングの結果を評価する* 上記の "output2" の出力結果の "BaseLocation"、"RelativeLocaiton"、"SasBlobToken" を組み合わせてブラウザーのアドレス バーに完全な URL を貼り付けると、再トレーニング済みのモデルのパフォーマンス結果を確認できます。

	これにより、新しくトレーニングを行ったモデルが、既存のモデルに代わるのに十分なパフォーマンスを行うかどうかがわかります。

7. *追加のエンドポイントにおけるトレーニング済みのモデルを更新する* このプロセスを完了するには、上記の手順 4 で作成した予測エンドポイントのトレーニング済みのモデルを更新する必要があります。

	上記の BES 出力は "output1" の再トレーニングの結果に関する情報を示しており、再トレーニング済みのモデルの場所情報が含まれます。ここで、このトレーニング済みのモデルを取得し、スコア付けエンドポイント (上記の手順 4 で作成) を更新する必要があります。サンプル コードを次に示します。

	```C#
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
	```

	この呼び出しの "ApiKey" と "endpointUrl" は、エンドポイントのダッシュボードに表示されます。

	この呼び出しが成功すると、新しいエンドポイントが約 15 秒以内に再トレーニング済みのモデルを使用して開始します。

##概要  
再トレーニング API を使用して、予測 Web サービスのトレーニング済みのモデルを更新することができ、新しいデータによる予測モデルの再トレーニングや、お客様独自のデータによるモデルの再トレーニングを目的としたモデルの配布といったシナリオを可能にします。

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0211_2016-->