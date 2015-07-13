<properties 	
	pageTitle="Azure Data Factory - サンプル" 
	description="Azure Data Factory サービスに付属するサンプルについて詳細に説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - サンプル

## Azure ポータルのサンプル
Azure ポータルの **[パイプラインのサンプル]** ブレードを使用して、Azure Data Factory サンプルを迅速にデプロイ、レビュー、テストできます。

1. 新しいデータ ファクトリを作成するか、既存のデータ ファクトリを開きます。データ ファクトリを作成する手順については、「[Azure Data Factory を使ってみる][data-factory-get-started]」を参照してください
2. データ ファクトリの **[Data Factory]** ブレードで、**[パイプラインのサンプル]** タイルをクリックします。

	![Sample pipelines tile](./media/data-factory-samples/SamplePipelinesTile.png)

2. **[パイプラインのサンプル]** ブレードで、デプロイする**サンプル**をクリックします。
	
	![Sample pipelines blade](./media/data-factory-samples/SampleTile.png)

3. このサンプルの構成設定を指定します。たとえば、Azure ストレージ アカウント名とアカウント キー、Azure SQL サーバーの名前、データベース、ユーザー ID、パスワードなどです。

	![Sample blade](./media/data-factory-samples/SampleBlade.png)

4. 構成設定の指定が完了したら **[作成]** をクリックして、サンプルのパイプラインと、そのパイプラインで使用するリンクされたサービスとテーブルを作成またはデプロイします。
5. 前に **[パイプラインのサンプル]** ブレードでクリックしたサンプルのタイルに、デプロイの状態が表示されます。

	![Deployment status](./media/data-factory-samples/DeploymentStatus.png)

6. サンプルのタイルに**デプロイに成功した**ことを示すメッセージが表示されたら、**[パイプラインのサンプル]** ブレードを閉じます。
5. **[Data Factory]** ブレードで、リンクされたサービス、データ セット、パイプラインがデータ ファクトリに追加されたことを確認できます。  

	![Data Factory blade](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

次の表で、**[パイプラインのサンプル]** タイルで使用できるサンプルについて簡単に説明します。

サンプル名 | description
----------- | -----------
ゲーム会社の顧客プロファイル | Contoso は、ゲーム機、携帯機器、パーソナル コンピューター (PC) など、複数のプラットフォーム向けにゲームを製作するゲーム会社です。これらのゲームはそれぞれが大量のログを産み出します。Contoso 社の目標は、これらのゲームが産み出すログを収集解析することで有益な情報を手に入れ、アップセルやクロスセルの機会を見極め、新しい魅力的な機能などを開発する、つまりビジネスを向上させて顧客により良い体験を提供することです。このサンプルでは、サンプル ログを収集、処理して参照データで強化し、このデータを変換して Contoso 社 が最近開始したマーケティング キャンペーンの有効性を評価します。
 
## GitHub のサンプル
[GitHub の Azure-DataFactory リポジトリ](https://github.com/azure/azure-datafactory)には、Azure Data Factory サービスを迅速に導入sしたり、スクリプトを変更して独自のアプリケーションで使用したりするのに役立ついくつかのサンプルがあります。Samples\JSON フォルダーには、一般的なシナリオ用の JSON スニペットが含まれています。

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=62-->