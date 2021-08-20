---
title: 'チュートリアル: FSSPEC を使用して Synapse Analytics Spark プールの ADLS データの読み取り/書き込みを行う'
description: 機械学習ワークロードの FSSPEC を使用して、専用の Spark プールで ADLS データの読み取り/書き込みを行う方法についてのチュートリアルです。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 06/07/2021
author: AjAgr
ms.author: ajagarw
ms.openlocfilehash: ecd0b923939ab1da724b46272dbaf60c44389d3d
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904316"
---
# <a name="tutorial-use-fsspec-to-readwrite-adls-data-in-synapse-analytics-spark-pool"></a>チュートリアル: FSSPEC を使用して Synapse Analytics Spark プールの ADLS データの読み取り/書き込みを行う

ファイルシステム仕様 (FSSPEC) を使用して、Azure Synapse Analytics の専用 Spark プールでリンクされたサービスを使用して Azure Data Lake Storage (ADLS) へのデータの読み取り/書き込みを行う方法について説明します。

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

   :::image type="content" source="media/tutorial-spark-pool-filesystem-spec/create-adls-linked-service.png" alt-text="ADLS Gen2 ストレージ アクセス キーを使用して、リンクされたサービスを作成します。":::


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

   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key)

   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name 
   adls_account_key = '' #Provide exact ADLS account key 
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name=adls_account_name,    account_key=adls_account_key, mode="wt")
   
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
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key)
   
   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>) 
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key, mode="wt") 
   
   with fsspec_handle.open() as f:
       data.to_csv(f) 
   ```

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics の機械学習機能](what-is-machine-learning.md)