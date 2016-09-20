<properties
	pageTitle="Machine Learning 用 PowerShell モジュール | Microsoft Azure"
	description="Azure Machine Learning 用 PowerShell モジュールは、パブリック プレビューとしてご利用いただけます。ワークスペースや実験、Web サービスなどの作成と管理を PowerShell で行うことができます。"
	keywords="実験, 線形回帰, 機械学習アルゴリズム, 機械学習チュートリアル, 予測モデリング手法, データ サイエンス実験"
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/05/2016"
	ms.author="garye;haining"/>

# Microsoft Azure Machine Learning 用 PowerShell モジュール

Azure Machine Learning 用 PowerShell モジュールは、ワークスペースや実験、データセット、Web サービスなどを Windows PowerShell で管理することができる強力なツールです。

関連するドキュメントを閲覧したり、完全なソース コード付きでモジュールをダウンロードしたりするには、[https://aka.ms/amlps](https://aka.ms/amlps) にアクセスしてください。

## Machine Learning PowerShell モジュールとは

Machine Learning PowerShell モジュールは、Azure Machine Learning のワークスペースや実験、データセット、Web サービス、Web サービス エンドポイントを Windows PowerShell からすべて管理できる .NET ベースの DLL モジュールです。モジュールと共に、整然と分離された [C# API レイヤー](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)を含んだ完全なソース コードをダウンロードできます。この DLL を独自の .NET プロジェクトから参照すれば、.NET コードから Azure Machine Learning を管理することができます。また、この DLL はその土台となる REST API に依存しており、好きなクライアントから直接これらの REST API を利用することができます。

## PowerShell モジュールでできること

以下、この PowerShell モジュールで実行できるタスクの例を挙げます。ここに挙げた以外にも、さまざまな機能があります。[こちらの詳しいドキュメント](https://aka.ms/amlps)を参照してください。

- 管理証明書を使用して新しいワークスペースをプロビジョニングする ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- 実験グラフを表す JSON ファイルをエクスポート/インポートする ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph)/[Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- 実験を実行する ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- 予測実験から Web サービスを作成する ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- 発行済みの Web サービスに新しいエンドポイントを作成する ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- RRS/BES Web サービス エンドポイントを呼び出す ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint)/[Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

PowerShell を使用して既存の実験を実行する簡単な例を次に示します。

		#Find the first Experiment named “xyz”
		$exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
		#Run the Experiment
		Start-AmlExperiment -ExperimentId $exp.ExperimentId 

さらに詳しい使用例については、記事「[PowerShell を使用して 1 つの実験から複数の Machine Learning モデルと Web サービス エンドポイントを作成する](machine-learning-create-models-and-endpoints-with-powershell.md)」を参照してください。PowerShell モジュールを使用して、ごく一般的に要求されるタスクを自動化する例が紹介されています。

## 開始するには?

Machine Learning PowerShell を初めて使用する方は、GitHub から[リリース パッケージ](https://github.com/hning86/azuremlps/releases)をダウンロードし、[インストール手順](https://github.com/hning86/azuremlps/blob/master/README.md)に従ってください。ダウンロード/解凍した DLL のブロックを解除してから、ご使用の PowerShell 環境にインポートする必要があります。ほとんどのコマンドレットで、ワークスペース ID とワークスペース承認トークン、そのワークスペースが存在する Azure リージョンの指定が必要となります。これらの情報を指定するには、既定の config.json ファイルを使用するのが最も簡単です。この点については、インストール手順に詳しく取り上げられています。当然、Git ツリーを複製して、そのコードに Visual Studio からローカルで変更を加え、コンパイルしてもかまいません。

## 次のステップ

PowerShell モジュールは、このプレビュー期間中に引き続き改善され、拡張される予定です。「[Cortana Intelligence and Machine Learning Blog (Cortana Intelligence と Machine Learning のブログ)](https://blogs.technet.microsoft.com/machinelearning/)」で最新情報をチェックしてください。

<!---HONumber=AcomDC_0914_2016-->