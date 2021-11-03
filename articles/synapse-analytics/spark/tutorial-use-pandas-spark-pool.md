---
title: 'チュートリアル: Synapse Analytics のサーバーレス Apache Spark プールで、Pandas を使用して ADLS データの読み取り/書き込みを行う'
description: PySpark ノートブックで Pandas を使用して、サーバーレス Apache Spark プール内の ADLS データの読み取り/書き込みを行う方法についてのチュートリアルです。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4f977ad6a6cc38a269eedbe3e107b166bd74a4f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029590"
---
# <a name="tutorial-use-pandas-to-readwrite-azure-data-lake-storage-gen2-data-in-serverless-apache-spark-pool-in-synapse-analytics"></a>チュートリアル: Synapse Analytics のサーバーレス Apache Spark プールで、Pandas を使用した Azure Data Lake Storage Gen2 データの読み取り/書き込みを行う

Pandas を使用して、Azure Synapse Analytics のサーバーレス Apache Spark プールを使用して Azure Data Lake Storage Gen2 (ADLS) にデータの読み取り/書き込みを行う方法について説明します。 このチュートリアルの例では、Synapse だけでなく、Excel ファイルと Parquet ファイルで Pandas を使用して csv データを読み取る方法を示します。

このチュートリアルで学習する内容は次のとおりです。

   > [!div class="checklist"]
   > - Spark セッションで、Pandas を使用して ADLS Gen2 データの読み取り/書き込みを行います。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- Azure Data Lake Storage Gen2 ストレージ アカウントが既定のストレージ (つまりプライマリ ストレージ) として構成されている [Azure Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 使用する Data Lake Storage Gen2 ファイル システムの "*Storage Blob データ共同作成者*" である必要があります。
- Azure Synapse Analytics ワークスペースのサーバーレス Apache Spark プール。 詳細については、[Azure Synapse での Spark プールの作成](../get-started-analyze-spark.md)に関する記事を参照してください。
- [セカンダリ Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md) アカウント (Synapse ワークスペースの既定値ではない) を構成します。 使用する Data Lake Storage Gen2 ファイル システムの "*Storage Blob データ共同作成者*" である必要があります。
- リンクされたサービスを作成します。Azure Synapse Analytics では、リンクされたサービスは、サービスへの接続情報を定義します。 このチュートリアルでは、Azure Synapse Analytics および Azure Data Lake Storage Gen2 のリンクされたサービスを追加します。

   1. Azure Synapse Studio を開き、 **[管理]** タブを選択します。
   1. **[外部接続]** で、 **[リンクされたサービス]** を選択します。
   1. リンクされたサービスを追加するには、 **[新規]** を選択します。
   1. リストから [Azure Data Lake Storage Gen2] タイルを選択し、 **[続行]** を選択します。
   1. ご利用の認証資格情報を入力します。 アカウント キー、サービス プリンシパル (SP)、資格情報、Managed Service Identity (MSI) が、認証の種類として現在サポートされています。 認証のために選択する前に、*Storage BLOB データ共同作成者* が SP と MSI のストレージに割り当てられていることを確認してください。 ご利用の資格情報が正しいことを確認するために、**接続をテスト** します。 **[作成]** を選択します。

   :::image type="content" source="media/tutorial-use-pandas-spark-pool/create-adls-linked-service.png" alt-text="ADLS Gen2 ストレージ アクセス キーを使用してリンクされたサービスを作成するスクリーンショット。":::


> [!NOTE]
> - Pandas 機能は、Azure Synapse Analytics の **Python 3.8** および **Spark3** サーバーレス Apache Spark プールでサポートされています。 
> - 次のバージョンで使用可能なサポート: **pandas 1.2.3、fsspec 2021.10.0、adlfs 0.7.7**
> - **Azure Data Lake Storage Gen2 URI** (abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path) および **FSSPEC の短縮 URL** (abfs[s]://container_name/file_path) の両方をサポートする機能があります。


## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。


## <a name="readwrite-data-to-default-adls-storage-account-of-synapse-workspace"></a>Synapse ワークスペースの既定の ADLS ストレージ アカウントに対してデータの読み取り/書き込みを行う

Pandas は、ファイル パスを直接指定することで、ADLS データの読み取り/書き込みを行うことができます。

次のコードを実行します。

   > [!NOTE]
   > このスクリプトのファイル URL を更新してから実行してください。

   ```PYSPARK
      #Read data file from URI of default Azure Data Lake Storage Gen2
   
      import pandas
   
      #read csv file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path')
      print(df)
   
      #write csv file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path')
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
   
      #read csv file
      df = pandas.read_csv('abfs[s]://container_name/file_path')
      print(df)
   
      #write csv file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path')
   ```

## <a name="readwrite-data-using-secondary-adls-account"></a>セカンダリ ADLS アカウントを使用してデータの読み取り/書き込みを行う

Pandas は、セカンダリ ADLS アカウント データの読み取り/書き込みを、次の方法で行うことができます。
   - リンクされたサービスを使用します (ストレージ アカウント キー、サービス プリンシパルなどの認証オプションを使用して、サービス ID と資格情報を管理します)。
   - ストレージ オプションを使用して、クライアント ID とシークレット、SAS キー、ストレージ アカウント キー、および接続文字列を直接渡します。

**リンクされたサービスの使用**

次のコードを実行します。

   > [!NOTE]
   > このスクリプトを実行する前に、スクリプトのファイル URL とリンクされたサービス名を更新します。

   ```PYSPARK
      #Read data file from URI of secondary Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   file_path', storage_options = {'linked_service' : 'linked_service_name'})
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path',    storage_options = {'linked_service' : 'linked_service_name'})
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://container_name/file_path', storage_options =       {'linked_service' : 'linked_service_name'})
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path', storage_options = {'linked_service' :    'linked_service_name'})
   ```


**ストレージ オプションを使用して**、クライアント ID とシークレット、SAS キー、ストレージ アカウント キー、接続文字列を直接渡します。

次のコードを実行します。

   > [!NOTE]
   > このスクリプトを実行する前に、ファイルの URL と storage_options を更新してください。

   ```PYSPARK
      #Read data file from URI of secondary Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   file_path', storage_options = {'account_key' : 'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path',    storage_options = {'account_key' : 'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://container_name/file_path', storage_options = {'account_key' :    'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path', storage_options = {'account_key' :    'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   ```


## <a name="example-to-readwrite-parquet-file"></a>Parquet ファイルの読み取り/書き込みの例 

次のコードを実行します。

   > [!NOTE]
   > このスクリプトのファイル URL を更新してから実行してください。

   ```PYSPARK
      import pandas
      
      #read parquet file
      df = pandas.read_parquet('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   parquet_file_path')
      print(df)
      
      #write parquet file
      df.to_parquet('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   parquet_file_path')
   ```

## <a name="example-to-readwrite-excel-file"></a>Excel ファイルの読み取り/書き込みの例 

次のコードを実行します。

   > [!NOTE]
   > このスクリプトのファイル URL を更新してから実行してください。

   ```PYSPARK
      import pandas
      
      #read excel file
      df = pandas.read_excel('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   excel_file_path')
      print(df)
      
      #write excel file
      df.to_excel('abfs[s]://file_system_name@account_name.dfs.core.windows.net/excel_file_path')
   ```

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics](../index.yml)
