---
title: 'チュートリアル: FSSPEC を使用して Synapse Analytics のサーバーレス Apache Spark プール内の ADLS データの読み取りと書き込みを行う'
description: PySpark ノートブックで FSSPEC を使用して、サーバーレス Apache Spark プール内の ADLS データの読み取りと書き込みを行う方法についてのチュートリアルです。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 742aeca5df76558b6dcd81ceb184029f807a1a65
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029591"
---
# <a name="tutorial-use-fsspec-to-readwrite-adls-data-in-serverless-apache-spark-pool-in-synapse-analytics"></a>チュートリアル: FSSPEC を使用して Synapse Analytics のサーバーレス Apache Spark プール内の ADLS データの読み取りと書き込みを行う

Filesystem Spec (FSSPEC) とリンク サービスを使用して、Azure Synapse Analytics のサーバーレス Apache Spark プール内の Azure Data Lake Storage データの読み取りと書き込みを行う方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> - 専用 Spark セッションで ADLS データの読み取り/書き込みを行います。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- Azure Data Lake Storage Gen2 ストレージ アカウントが既定のストレージとして構成されている [Azure Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 使用する Data Lake Storage Gen2 ファイル システムの "*Storage Blob データ共同作成者*" である必要があります。
- Azure Synapse Analytics ワークスペースの Spark プール。 詳細については、[Azure Synapse での Spark プールの作成](../get-started-analyze-spark.md)に関する記事を参照してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-linked-services"></a>リンクされたサービスを作成します

Azure Synapse Analytics で、リンクされたサービスとは、他のサービスへの接続情報を定義した場所です。 このセクションでは、Azure Synapse Analytics および Azure Data Lake Storage Gen2 のリンクされたサービスを追加します。

1. Azure Synapse Studio を開き、 **[管理]** タブを選択します。
1. **[外部接続]** で、 **[リンクされたサービス]** を選択します。
1. リンクされたサービスを追加するには、 **[新規]** を選択します。
1. リストから [Azure Data Lake Storage Gen2] タイルを選択し、 **[続行]** を選択します。
1. ご利用の認証資格情報を入力します。 アカウント キーは現在サポートされている認証の種類です。 **[接続のテスト]** を選択して、ご利用の資格情報が正しいことを確認します。 **[作成]** を選択します。

   :::image type="content" source="media/tutorial-spark-pool-filesystem-spec/create-adls-linked-service.png" alt-text="ADLS Gen2 ストレージ アクセス キーを使用してリンクされたサービスを作成するスクリーンショット。":::


## <a name="readwrite-data-using-storage-account-name-and-key"></a>ストレージ アカウントの名前とキーを使用してデータの読み取り/書き込みを行う

FSSPEC は、ストレージ アカウントの名前とキーを直接指定して、ADLS データの読み取り/書き込みを行うことができます。

1. Synapse Studio で、 **[データ]**  >  **[リンク]**  >  **[Azure Data Lake Storage Gen2]** を開き ます。 データを既定のストレージ アカウントにアップロードします。

1. 次のコードを実行します。

   > [!NOTE]
   > このスクリプトを実行する前に、ファイルの URL、ADLS Gen2 ストレージ名およびキーを更新してください。

   ```PYSPARK
   # To read data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name
   adls_account_key = '' #Provide exact ADLS account key

   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key)

   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name 
   adls_account_key = '' #Provide exact ADLS account key 
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   
   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key, mode="wt")
   
   with fsspec_handle.open() as f:
    data.to_csv(f)
   ```

## <a name="readwrite-data-using-linked-service"></a>リンクされたサービスを使用したデータの読み取り/書き込み

FSSPEC は、リンクされたサービス名を指定して、ADLS データの読み取り/書き込みを行うことができます。


1. Synapse Studio で、 **[データ]**  >  **[リンク]**  >  **[Azure Data Lake Storage Gen2]** を開き ます。 データを既定のストレージ アカウントにアップロードします。

1. 次のコードを実行します。

   > [!NOTE]
   > このスクリプトを実行する前に、ファイルの URL、リンクされたサービス名、ADLS Gen2 のストレージ名を更新してください。

   ```PYSPARK
   # To read data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>)
   
   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key)
   
   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>) 
   
   fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key, mode="wt") 
   
   with fsspec_handle.open() as f:
       data.to_csv(f) 
   ```

## <a name="upload-file-from-local-file-system-to-default-adls-storage-account-of-synapse-workspace"></a>Synapse ワークスペースのローカル ファイル システムから既定の ADLS ストレージ アカウントにファイルをアップロードします。

FSSPEC は、ローカル ファイル システムから Synapse ワークスペースの既定の ADLS ストレージ アカウントにファイルをアップロードできます。


次のコードを実行します。

   > [!NOTE]
   > このスクリプトのファイル URL を更新してから実行してください。

   ```PYSPARK
   # Import libraries
   import fsspec
   import os
   
   # Set variables
   local_file_name = "<local_file_name>"
   ADLS_Store_Path = "abfs[s]://<filesystemname>@<account name>.dfs.windows.cor.net/"+local_file_name
   
   # Generate local file for testing 
   with open(local_file_name, mode='w') as f:
       for i in range(1000):
           f.write("Testing local file functionality\n")
   print("Created: " + local_file_name)

   # Upload local file to ADLS 
   fs = fsspec.filesystem('abfs[s]')
   fs.upload(local_file_name, ADLS_Store_Path)
   ```

## <a name="next-steps"></a>次のステップ

- [サーバーレス Apache Spark プールを使用した Synapse Analytics の Pandas](tutorial-use-pandas-spark-pool.md)
- [Azure Synapse Analytics](../index.yml)
- [FSSPEC 公式ドキュメント](https://filesystem-spec.readthedocs.io/en/latest/)
