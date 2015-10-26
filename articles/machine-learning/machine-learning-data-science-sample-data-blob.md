<properties 
	pageTitle="Azure BLOB ストレージ内のデータのサンプリング | Microsoft Azure" 
	description="Azure BLOB ストレージのサンプル データ" 
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
	ms.date="09/01/2015" 
	ms.author="sunliangms;fashah;msolhab;garye;bradsev" />

#<a name="heading"></a>Azure BLOB ストレージ内のデータのサンプリング

このドキュメントでは、Azure BLOB ストレージに格納されたデータのサンプリングについて説明します。これは、プログラムを使用してこのデータをダウンロードしてから、Python のサンプル コードでサンプリングして行います。これを行う手順は、次のとおりです。

## データのダウンロードとダウン サンプリング
1. 次の Python のサンプル コードから、BLOB サービスを使用して Azure BLOB ストレージからデータをダウンロードします。 

	    from azure.storage import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. 上記でダウンロードしたファイルから、Pandas データ フレームにデータを読み取ります。

		import pandas as pd

	    #directly ready from file on disk
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

3. 次のように、`numpy` の `random.choice` を使用してデータをダウン サンプリングします。

	    # A 1 percent sample
    	sample_ratio = 0.01 
    	sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    	sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    	dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

	これで、上記の 1 パーセントのサンプルのデータ フレームを操作して、さらなる探索および特徴の生成を行えるようになりました。

##<a name="heading"></a>データをアップロードし、Azure Machine Learning で読み取る

次のサンプル コードを使用すると、次のようにデータをダウンサンプリングして、 Azure ML で直接使用することができます。

1. データ フレームをローカル ファイルに書き込む

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 次のサンプル コードを使用してローカル ファイルを Azure BLOB にアップロードする

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. 次のスクリーン ショットに示すように、Azure ML の[リーダー](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)を使用して Azure BLOB からデータを読み取ります。
 
![リーダー BLOB][1]

[1]: ./media/machine-learning-data-science-sample-data-blob/reader_blob.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=Oct15_HO3-->