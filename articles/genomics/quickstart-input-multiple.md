---
title: "クイックスタート: 複数の入力を使用するワークフローを送信する | Microsoft Docs"
titleSuffix: Azure
description: "このクイックスタートでは、msgen クライアントがインストールされており、そのサービスを通じてサンプル データが正常に実行されていることを前提としています。"
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: d410516f807b7914e15bed1fb93ee58d3e340d1e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2017
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>同じサンプルに属する複数の入力を使用するワークフローを送信する

このクイックスタートでは、入力ファイルが**同じサンプルに属する**複数の FASTQ ファイルまたは BAM ファイルである場合に、Microsoft Genomics サービスにワークフローを送信する方法を示します。 ただし、FASTQ ファイルと BAM ファイルを同じ送信に混在させることは**できません**。

このトピックでは、`msgen` クライアントをインストールして実行済みであること、および Azure Storage の使い方に詳しいことを前提としています。 提供されたサンプル データを使用するワークフローを正常に送信したら、このクイックスタートを進める準備は完了です。 


## <a name="multiple-bam-files"></a>複数の BAM ファイル

### <a name="upload-your-input-files-to-azure-storage"></a>Azure Storage に入力ファイルをアップロードする
入力として使用する BAM ファイルが複数あり (*reads.bam*、*additional_reads.bam*、*yet_more_reads.bam*)、それらを Azure のストレージ アカウントである *myaccount* にアップロード済みであるとします。 API URL とアクセス キーは用意されています。 **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** に出力します。


### <a name="submit-your-job-to-the-msgen-client"></a>`msgen` クライアントにジョブを送信する 

--input-blob-name-1 引数にすべての BAM ファイルの名前を渡すことで、複数の BAM ファイルを送信できます。 すべてのファイルが同じサンプルに属している必要がありますが、ファイルの順序は重要ではありません。 Windows と Unix のコマンド ライン、および構成ファイルを使ったサンプルの送信を以下に示します。 わかりやすくするために改行が追加されています。


Windows の場合:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Unix の場合:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


構成ファイルを使う場合のファイルの内容は次のとおりです。

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

次の呼び出しを使って `config.txt` ファイルを送信します: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>複数の FASTQ ファイルのペア

### <a name="upload-your-input-files-to-azure-storage"></a>Azure Storage に入力ファイルをアップロードする
入力として使用する FASTQ ファイルのペアが複数ある (*reads_1.fq.gz* と *reads_2.fq.gz*、*additional_reads_1.fq.gz* と *additional_reads_2.fq.gz*、*yet_more_reads_1.fq.gz* と *yet_more_reads_2.fq.gz*) とします。 それらを Azure のストレージ アカウントである *myaccount* にアップロード済みで、API URL とアクセス キーは用意されています。 **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** に出力します。


### <a name="submit-your-job-to-the-msgen-client"></a>`msgen` クライアントにジョブを送信する 

FASTQ ファイルのペアは同じサンプルに属しているだけでなく、一緒に処理する必要があります。  ファイル名の順序は、--input-blob-name-1 と --input-blob-name-2 に引数として渡される場合に重要になります。 

Windows と Unix のコマンド ライン、および構成ファイルを使ったサンプルの送信を以下に示します。 わかりやすくするために改行が追加されています。


Windows の場合:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Unix の場合:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


構成ファイルを使う場合のファイルの内容は次のとおりです。

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

次の呼び出しを使って `config.txt` ファイルを送信します: `msgen submit -f config.txt`

## <a name="next-steps"></a>次のステップ
この記事では、複数の BAM ファイルまたは FASTQ ファイルのペアを Azure Storage にアップロードして、`msgen` Python クライアント経由で Microsoft Genomics サービスにワークフローを送信しました。 ワークフローの送信および Microsoft Genomics で使うことのできるその他のコマンドについて詳しくは、[FAQ](frequently-asked-questions-genomics.md) をご覧ください。 