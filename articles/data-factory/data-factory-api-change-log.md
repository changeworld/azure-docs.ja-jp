<properties 
	pageTitle="Data Factory - .NET API の変更ログ | Microsoft Azure" 
	description="Azure Data Factory.の特定のバージョンの .NET API における重大な変更、機能の追加、バグ修正などについて説明します。" 
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
	ms.date="01/30/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - .NET API の変更ログ 
この記事では、特定のバージョンの Azure Data Factory SDK の変更に関する情報を提供します。Azure Data Factory の最新の Nuget パッケージについては、[こちら](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)をご覧ください。

## バージョン 4.4.0
リリース日: 2016 年 1 月 28 日

### 機能の追加

- 次のリンクされたサービスの種類が、コピー アクティビティのデータ ソースおよびシンクとして追加されています。
	- [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx)。概念情報と例については、「[Azure Storage SAS Linked Service (Azure Storage SAS のリンクされたサービス)](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service)」を参照してください。 

## バージョン 4.3.0
リリース日: 2015 年 11 月 25 日

### 機能の追加

- 次のリンクされたサービスの種類が、コピー アクティビティのデータ ソースとして追加されています。
	- [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx)。概念情報と例については、「[Move data from HDFS using Data Factory (Data Factory を使用して HDFS からデータを移動する)](data-factory-hdfs-connector.md)」を参照してください。 
	- [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx)。概念情報と例については、「[Move data From ODBC data stores using Azure Data Factory (Azure Data Factory を使用して ODBC データ ストアからデータを移動する)](data-factory-odbc-connector.md)」を参照してください。 

## バージョン 4.2.0
リリース日: 2015 年 11 月 10 日

### 機能の追加

- 次の新しいアクティビティの種類が追加されました。 [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx) です。アクティビティの詳細については、「[更新リソース アクティビティを使用して Azure ML モデルを更新する](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity)」を参照してください。
- 新しい省略可能なプロパティ [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) が [AzureMLLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx) に追加されました。 
- [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) プロパティと [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) プロパティが [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) クラスに追加されました。 
- Data Factory サービスへのクライアントの呼び出しのタイムアウトの構成が許可されました。 


## バージョン 4.1.0
リリース日: 2015 年 10 月 28 日

### 機能の追加
* 次のリンクされたサービスの種類が追加されています。 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* 次のアクティビティの種類が追加されています。 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* 次のデータセットの型が追加されています。 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* 次のコピー アクティビティ用のソースとシンクの種類が追加されています。
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## バージョン 4.0.1
リリース日: 2015 年 10 月 13 日

### 重大な変更
次のクラスの名前が変更されました。新しい名前は、4.0.0 リリースより前の、クラスの元の名前です。
 
4\.0.0 での名前 | 4\.0.1 での名前
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## バージョン 4.0.0
リリース日: 2015 年 10 月 2 日

### 重大な変更



- 次のクラスとインターフェイスの名前が変更されました。

| 以前の名前 | 新しい名前 |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| テーブル | [Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    
- このリリースの **API バージョンは** **2015-10-01** です。

- **List** メソッドでページングされた結果が返されるようになりました。応答に空でない **NextLink** プロパティが含まれている場合、クライアント アプリケーションは、すべてのページが返されるまで次のページを取得し続ける必要があります。たとえば次のようになります。

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- **List** パイプライン API では、全詳細ではなく、パイプラインの概要だけが返されます。たとえば、パイプライン概要のアクティビティには、名前と種類だけが含まれます。

### 機能の追加
- [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) クラスでは、Azure SQL Data Warehouse へのべき等コピーをサポートするために、**SliceIdentifierColumnName** と **SqlWriterCleanupScript** の 2 つの新しいプロパティがサポートされるようになりました。これらのプロパティの詳細については、[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) に関する記事の「[メカニズム 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1)」および「[メカニズム 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2)」をご覧ください。

- コピー アクティビティの一環として、Azure SQL Database ソースと Azure SQL Data Warehouse ソースに対するストアド プロシージャの実行がサポートされるようになりました。[SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) クラスと [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) クラスには、これをサポートするプロパティとして、**SqlReaderStoredProcedureName** と **StoredProcedureParameters** があります。これらのプロパティの詳細については、Azure.com の [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) と [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) に関する記事をご覧ください。

<!---HONumber=AcomDC_0204_2016-->