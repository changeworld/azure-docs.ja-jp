<properties
   pageTitle="Stream Analytics から Data Lake Store へのデータのストリーム| Azure"
   description="Azure Stream Analytics を使用した Azure Data Lake Store へのデータのストリーム"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>

# Azure Stream Analytics を使用した Azure Storage BLOB から Data Lake Store へのデータ ストリーム

この記事では、Azure Data Lake Store を Azure Stream Analytics ジョブの出力として使用する方法について説明します。ここでは、Azure Storage BLOB (入力) からデータを読み取り、そのデータを Data Lake Store (出力) に書き込む簡単なシナリオを紹介します。

>[AZURE.NOTE] 現時点では、Stream Analytics 向けの Data Lake Store 出力の作成と構成は [Azure クラシック ポータル](https://manage.windowsazure.com)でのみサポートされています。そのため、このチュートリアルの一部は、Azure クラシック ポータルを使用します。

## 前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- Data Lake Store パブリック プレビューに対して、**Azure サブスクリプションを有効にする**。[手順](data-lake-store-get-started-portal.md#signup)を参照してください。

- **Azure Storage アカウント**。このアカウントから BLOB コンテナーを使用して、Stream Analytics ジョブ向けのデータを入力します。このチュートリアルでは、「**datalakestoreasa**」という名前のストレージ アカウントを作成して、そのアカウント内に「**datalakestoreasacontainer**」という名前のコンテナーを作成します。コンテナーを作成したら、サンプル データ ファイルをアップロードします。サンプル データ ファイルは [Azure Data Lake Git リポジトリ](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)で取得できます。[Azure Storage Explorer](http://storageexplorer.com/) など各種クライアントを使用して、BLOB コンテナーにデータをアップロードします。

	>[AZURE.NOTE] Azure ポータルからアカウントを作成する場合は、**クラシック** デプロイ モデルで作成するようにします。これにより、ストレージ アカウントが Azure クラシック ポータルからアクセスできるようになります。これは Azure クラシック ポータルを使用して、Stream Analytics ジョブを作成するためです。クラシック デプロイで Azure ポータルからストレージ アカウントを作成する方法については、「[Create an Azure Storage account (Azure Storage アカウントの作成)](../storage/storage-create-storage-account/#create-a-storage-account)」を参照してください。
	>
	> または、Azure クラシック ポータルからストレージ アカウントを作成することもできます。

- **Azure Data Lake Store アカウント**。「[Azure ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。


## Stream Analytics のジョブの作成

まず、入力ソースと出力先を含む Stream Analytics ジョブを作成します。このチュートリアルでは、ソースは Azure BLOB コンテナー、出力先は Data Lake Store です。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインオンします。

2. 画面左下の **[新規]**、 **[データ サービス]**、**[Stream Analytics]**、**[簡易作成]** をクリックします。次の値を入力してから、**[Stream Analytics ジョブの作成]**をクリックします。

	![Stream Analytics のジョブの作成](./media/data-lake-store-stream-analytics/create.job.png "Stream Analytics のジョブの作成")

## ジョブに BLOB 入力を作成

1. Stream Analytics ジョブのページを開き、**[入力]** タブをクリックしてから **[入力の追加]** をクリックし、 ウィザードを起動します。

2. **[入力をジョブに追加します]** ページで **[データ ストリーム]** を選択し、右向きの矢印をクリックします。

	![入力をジョブに追加](./media/data-lake-store-stream-analytics/create.input.1.png "入力をジョブに追加")

3. **[データ ストリームをジョブに追加します]** ページで **[Blob ストレージ]** を選択し、右向きの矢印をクリックします。

	![データ ストリームをジョブに追加](./media/data-lake-store-stream-analytics/create.input.2.png "データ ストリームをジョブに追加")

4. **[BLOB ストレージの設定]** ページで、入力データソースとして使用する BLOB ストレージの詳細情報を入力します。

	![BLOB ストレージの設定を入力](./media/data-lake-store-stream-analytics/create.input.3.png "BLOB ストレージの設定を入力")

	* **入力のエイリアスを入力します**。これは、ジョブの入力に付ける一意の名前です。
	* **ストレージ アカウントを選択します**。ストレージ アカウントは Stream Analytics ジョブと同じリージョンに配置し、リージョン間でのデータ転送が有料にならないようにします。
	* **ストレージ コンテナーを指定します**。新しいコンテナーを作成することも、既存のコンテナーを選択することもできます。

	右向きの矢印をクリックします。

5. **[シリアル化の設定]** ページでシリアル化形式を **CSV**、区切り記号を**タブ**、エンコードを **UTF8** と設定し、チェック マークをクリックします。

	![データ シリアル化の設定を入力](./media/data-lake-store-stream-analytics/create.input.4.png "データ シリアル化の設定を入力")

6. ウィザードが完了すると BLOB の入力が **[入力]** タブの下に追加され、**[診断]** 列に **[OK]** と表示されます。下部の **[テスト接続]** ボタンで、入力への接続を明示的にテストすることもできます。

## ジョブに Data Lake Store 出力を作成

1. Stream Analytics ジョブのページを開き、**[出力]** タブをクリックしてから **[出力の追加]** をクリックし、ウィザードを起動します。

2. **[出力をジョブに追加します]** ページで **[Data Lake Store]** を選択し、右向きの矢印をクリックします。

	![出力をジョブに追加](./media/data-lake-store-stream-analytics/create.output.1.png "出力をジョブに追加")

3. **[接続の承認]** ページでは、Data Lake Store アカウントを作成済みの場合は **[今すぐ承認]** をクリックします。アカウントがない場合は **[今すぐサインアップしてください。]** をクリックして、新しいアカウントを作成します。このチュートリアルでは、「前提条件」に記載したように、Data Lake Store アカウントがすでに作成済みの場合について説明しています。Azure クラシック ポータルへのサインインに使用する資格情報で、自動的に承認されます。

	![Data Lake Store の承認](./media/data-lake-store-stream-analytics/create.output.2.png "Data Lake Store の承認")

4. **[Data Lake Store 設定]** ページで、次の画面キャプチャに示すように情報を入力します。

	![Data Lake Store の設定を指定](./media/data-lake-store-stream-analytics/create.output.3.png "Data Lake Store の設定を指定")

	* **出力のエイリアスを入力します**。これは、ジョブの出力に付ける一意の名前です。
	* **Data Lake Storeアカウントを指定します**。「前提条件」に記載したように、これはすでに作成済みとします。
	* **パスのプレフィックス パターンを指定します**。これは Stream Analytics ジョブによって Data Lake Store に書き込まれる出力ファイルの識別に必要です。ジョブによって書き込まれる出力のタイトルはGUID 形式であるため、プレフィックスを含めると書き込まれた出力の識別に役立ちます。日時スタンプをプレフィックスの一部に含める場合は、プレフィックス パターンに `{date}/{time}` を含めるようにします。これを含めることで **[日付形式]** と **[時刻形式]** フィールドが有効になり、好みの形式を選択できます。

	右向きの矢印をクリックします。

5. **[シリアル化の設定]** ページでシリアル化形式を **CSV**、区切り記号を**タブ**、エンコードを **UTF8** と設定し、チェック マークをクリックします。

	![出力形式の指定](./media/data-lake-store-stream-analytics/create.output.4.png "出力形式の指定")

6. ウィザードが完了すると Data Lake Store の出力が **[出力]** タブの下に追加され、**[診断]** 列に **[OK]** と表示されます。下の **[テスト接続]** ボタンで、出力への接続を明示的にテストすることもできます。

## Stream Analytics ジョブの実行

Stream Analytics ジョブを実行するには、[クエリ] タブからクエリを実行する必要があります。このチュートリアルでは、次の画面キャプチャに示すように、プレースホルダーをジョブの入力および出力エイリアスで置き換えて、サンプル クエリを実行することができます。

![Run query](./media/data-lake-store-stream-analytics/run.query.png "Run query")

画面下部の **[保存]** をクリックしてから、**[開始]** をクリックします。ダイアログ ボックスで **[ユーザー設定時刻]** を選択し、過去の日付を **1/1/2016** のように選択します。チェック マークをクリックしてジョブを開始します。ジョブが開始されるまでに数分かかる場合があります。

![ジョブの時刻の設定](./media/data-lake-store-stream-analytics/run.query.2.png "ジョブの時刻の設定")

ジョブが開始したら **[監視]** タブをクリックして、データの処理状況を確認します。

![ジョブの監視](./media/data-lake-store-stream-analytics/run.query.3.png "ジョブの監視")

最後に、[Azure ポータル](https://portal.azure.com)を使用して Data Lake Store アカウントを開き、データがアカウントに正常に書き込まれたかどうかを確認できます。

![出力の確認](./media/data-lake-store-stream-analytics/run.query.4.png "出力の確認")

[データ エクスプローラー] ウィンドウで、Data Lake Store 出力設定 (`streamanalytics/job/output/{date}/{time}`) で指定したとおりにフォルダーに出力が書き込まれていることがわかります。

## 関連項目

* [Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0713_2016-->