<properties 
	pageTitle="Azure Blob ストレージとの間のデータの移動 | Microsoft Azure" 
	description="Azure Blob ストレージとの間のデータの移動" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="sunliangms;sachouks;mohabib;bradsev" />

# Azure Blob ストレージとの間のデータの移動

高度な分析プロセスで使用されるさまざまなデータ サイエンス ワークフローで必要なリソースを確認するには、記事「[Azure Machine Learning での Advanced Analytics Process and Technology (ADAPT) のためのシナリオ](../machine-learning-data-science-plan-sample-scenarios.md)」が役立ちます。Azure Blob ストレージとの間でデータを移動する必要がある場合、次の方法のいずれかを使用します。

- [Azure ストレージ エクスプローラーの使用](#explorer)
- [AzCopy コマンド ライン ユーティリティの使用](#AzCopy)
- [Python での Azure SDK の使用](#PythonSDK)


> [AZURE.TIP]別の方法として、[Azure Data Factory](https://azure.microsoft.com/ja-jp/services/data-factory/) を使用して、Azure BLOB ストレージからデータをダウンロードして公開済みの Azure Machine Learning Web サービスに渡し、予測分析の結果を受け取ってストレージにアップロードするパイプラインを作成してスケジュールできます。詳細については、「[Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する](../data-factory/data-factory-create-predictive-pipelines.md)」を参照してください。

<para></para>

> [AZURE.NOTE]Azure BLOB ストレージの完全な概要については、「[Azure BLOB の基礎](../storage-dotnet-how-to-use-blobs.md)」と「[Azure BLOB Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)」を参照してください。

データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。この情報を取得する方法については、「[ストレージ アカウントの管理](../storage-create-storage-account.md)」の「方法: ストレージ アクセス キーの表示、コピーおよび再生成」を参照してください。このドキュメントは Azure Storage のアカウントと、対応するストレージ キーがあることを前提としています。


<a id="explorer"></a>
## Azure ストレージ エクスプローラーの使用 

Azure ストレージ エクスプローラーは、Azure Storage アカウント内のデータを検査および変更するための無料の Windows ベースのツールです。これは、「[Azure ストレージ エクスプローラー](http://azurestorageexplorer.codeplex.com/)」からダウンロードできます。次の手順では、Azure ストレージ エクスプローラーを使用してデータをアップロード/ダウンロードする方法について説明します。

1.  Azure ストレージ エクスプローラーの起動 
2.  アクセスするストレージ アカウントが、Azure ストレージ エクスプローラーに追加されていない場合は、[アカウントの追加] ボタンをクリックしてアカウントを追加します。既に追加されている場合は、[--ストレージ アカウントの選択--] ドロップダウンからアカウントを選択します。![Create workspace][1] <br>
3. ストレージ アカウント名とストレージ アカウント キーを入力して、[ストレージ アカウントの追加] をクリックします。複数のストレージ アカウントを追加できます。その場合、各アカウントがタブに表示されます。このストレージ アカウント下のコンテナーは、左側のパネルに表示されます。コンテナーを選択すると、右側のパネルにコンテナー内の BLOB が表示されます。![Create workspace][2] <br> ![Create workspace][3] <br>
4. データをアップロードするには、[アップロード] ボタンをクリックします。アップロードする 1 つまたは複数のファイルをファイル システムから選択し、[開く] をクリックしてファイルのアップロードを開始します。
5. 対応するコンテナー内の BLOB を選択し、[ダウンロード] ボタンをクリックして、データをダウンロードします。

<a id="AzCopy"></a>
## AzCopy の使用

AzCopy は、データをアップロードおよびダウンロードするためのコマンド ライン ユーティリティです。

**警告:** 高度な分析プロセスで既に設定されている VM とは異なるマシンを使用している場合は、「[AzCopy のダウンロードとインストール](../storage-use-azcopy.md#install)」のインストール手順を使用して、AzCopy をインストールしてください。

###BLOB との間でのファイルのアップロード/ダウンロードの例:

	# Uploading from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

> [AZURE.TIP]1.ファイルをアップロードする場合、/S を指定するとファイルを再帰的にアップロードします。このパラメーターを指定しなかった場合、サブディレクトリ内のファイルは 1 つもアップロードされません。2.ファイルをダウンロードする場合、/S を指定すると、指定したディレクトリとそのサブディレクトリ内のすべてのファイル、または指定されたディレクトリとそのサブディレクトリ内の指定したパターンと一致するすべてのファイルがダウンロードされるまで、コンテナーを再帰的に検索します。3./Source パラメーターを使用して、ダウンロードする特定の BLOB ファイルを指定することはできません。特定のファイルをダウンロードするには、ダウンロードする BLOB ファイル名を /Pattern パラメーターを使用して指定します。/S パラメーターは、AzCopy にファイル名のパターンを再帰的に検索させるために使用できます。パターンのパラメーターを指定しなかった場合、AzCopy はそのディレクトリ内のすべてのファイルをダウンロードします。

AzCopy の使用方法の詳細については、「[AzCopy コマンド ライン ユーティリティの概要](../storage-use-azcopy.md#install)」を参照してください。


<a id="PythonSDK"></a>
## Python の使用

Azure SDK で提供される Python API を使用して、以下のことを行うことができます。

- コンテナーを作成する
- コンテナーに BLOB をアップロードする
- BLOB をダウンロードする
- コンテナー内の BLOB を一覧表示する
- BLOB を削除する

このセクションでは、BLOB を一覧表示、アップロード、およびダウンロードする方法について説明します。Python API の使用方法の詳細については、「[Python から BLOB ストレージ サービスを使用する方法](../storage-python-how-to-use-blob-storage.md)」を参照してください。

> [AZURE.NOTE]高度な分析プロセスで既に設定されている VM とは異なるマシンを使用している場合、次のサンプル コードを使用する前に [Python Azure SDK](../python-how-to-install.md) をインストールする必要があります。

### BLOB へのデータのアップロード

プログラム的に Azure ストレージにアクセスするためのすべての Python コードの先頭付近に、次のスニペットを追加します。

	from azure.storage import BlobService

**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作できます。次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、BlobService オブジェクトを作成します。アカウント名とアカウント キーは実際のアカウントとキーに置き換えてください。
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

データを BLOB にアップロードするには、次のメソッドを使用します。
 
1. put_block_blob_from_path (指定されたパスからファイルの内容をアップロードします)
2. put_block_blob_from_file (既に開かれているファイル/ストリームから内容をアップロードします)
3. put_block_blob_from_bytes (バイトの配列をアップロードします)
4. put_block_blob_from_text (指定されたエンコーディングを使用して、指定されたテキスト値をアップロードします)
 
次のサンプル コードではコンテナーにローカル ファイルをアップロードします。
	
	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

次のサンプル コードでは、ローカル ディレクトリのすべてのファイル (ディレクトリを除く) を BLOB ストレージにアップロードします。

	from azure.storage import BlobService
	from os import listdir
	from os.path import isfile, join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]
	
	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name

### BLOB からのデータのダウンロード

BLOB からデータをダウンロードするには、次のメソッドを使用します。 1. get_blob_to_path 2. get_blob_to_file 3. get_blob_to_bytes 4. get_blob_to_text

これらのメソッドは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行します。

次のサンプル コードでは、コンテナー内の BLOB の内容をローカル ファイルにダウンロードします。

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

次のサンプル コードでは、コンテナーからすべての BLOB をダウンロードします。list_blobs を使用して、コンテナーで使用可能な BLOB の一覧を取得し、それらをローカル ディレクトリにダウンロードします。

	from azure.storage import BlobService
	from os.path import join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	
	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png
 

<!---HONumber=July15_HO3-->