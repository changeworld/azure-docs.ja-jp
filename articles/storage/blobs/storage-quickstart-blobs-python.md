---
title: "Azure クイックスタート - Python を使用して Azure Blob Storage との間でオブジェクトを転送する | Microsoft Docs"
description: "Python を使って Azure Blob Storage との間で双方向にオブジェクトを転送する方法を説明します"
services: storage
documentationcenter: storage
author: ruthogunnnaike
manager: cwatson
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2017
ms.author: v-ruogun
ms.openlocfilehash: 44ec416a814ff6a5fef79ef21e2f54ce4ce4da17
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2017
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-python"></a>Python を使用して Azure Blob Storage との間でオブジェクトを転送する
このクイックスタートでは、Python を使って、Azure Blob Storage 内のコンテナーでブロック BLOB のアップロード、ダウンロード、一覧取得を行う方法を説明します。 

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。 
* [Python](https://www.python.org/downloads/) のインストール
* [Azure Storage SDK for Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python) をダウンロードしてインストールします。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-storage-account-using-the-azure-portal"></a>Azure Portal を使用してストレージ アカウントを作成する

最初に、このクイックスタートで使う新しい汎用的なストレージ アカウントを作成します。 

1. [Azure Portal](https://portal.azure.com) にアクセスし、Azure アカウントを使ってログインします。 
2. ハブ メニューで、**[新規]** > **[ストレージ]** > **[ストレージ アカウント - Blob、File、Table、Queue]** の順に選びます。 
3. ストレージ アカウントの名前を入力します。 名前の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 また、一意である必要があります。
4. [`Deployment model`] を **[リソース マネージャー]** に設定します。
5. [`Account kind`] を **[汎用]** に設定します。
6. [`Performance`] を **[Standard]** に設定します。 
7. [`Replication`] を **[ローカル冗長ストレージ (LRS)]** に設定します。
8. [`Storage service encryption`] を **[無効]** に設定します。
9. [`Secure transfer required`] を **[無効]** に設定します。
10. サブスクリプションを選択します。 
11. [`resource group`] では、新しいリソース グループを作成して一意名を指定します。 
12. [`Location`] でストレージ アカウントに使う場所を選びます。
13. **[ダッシュボードにピン留めする]** をオンにし、**[作成]** をクリックしてストレージ アカウントを作成します。 

ストレージ アカウントが作成されて、ダッシュボードにピン留めされます。 そのストレージ アカウントをクリックして開きます。 **[設定]** で **[アクセス キー]** をクリックします。 キーを選び、ストレージ アカウント名をクリップボードにコピーして、後で使うためにメモ帳に貼り付けます。

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード
このクイックスタートで使う[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git)は、基本的な Python アプリケーションです。  

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 この Python プログラムを開くには、storage-blobs-python-quickstart フォルダーを探し、example.py ファイルを探します。  

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成
アプリケーションでは、ストレージ アカウント名とアカウント キーを指定して `BlockBlobService` オブジェクトを作成する必要があります。 お使いの IDE のソリューション エクスプローラーから `example.py` ファイルを開きます。 **accountname** および **accountkey** の値を、ご自分のアカウント名とキーに置き換えます。 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>サンプルの実行
このサンプルでは、"ドキュメント" フォルダーにテスト ファイルを作成します。 サンプル プログラムは、Blob Storage にテスト ファイルをアップロードし、コンテナー内の BLOB を一覧表示し、新しい名前を付けてファイルをダウンロードします。 

サンプルを実行します。 次の出力は、アプリケーションを実行するときに返される出力の例です。
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
任意のキーを押して続行すると、サンプル プログラムによってストレージ コンテナーとファイルが削除されます。 続行する前に、"ドキュメント" フォルダーで 2 つのファイルをチェックします。 それらを開いて、同じであるかどうか確認します。

[Azure Storage Explorer](http://storageexplorer.com) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。 

ファイルを確認した後、任意のキーを押してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、example.py ファイルを開いてコードを確認します。 

## <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する
最初に、Blob Storage にアクセスして管理するために使うオブジェクトへの参照を作成します。 これらのオブジェクトはお互いを基にして作成され、各オブジェクトは、一覧で次にあるオブジェクトによって使われます。

* お使いのストレージ アカウントの Blob service を指す **BlockBlobService** オブジェクトをインスタンス化します。 

* アクセスしているコンテナーを表す **CloudBlobContainer** オブジェクトをインスタンス化します。 コンテナーは、コンピューターでフォルダーを使ってファイルを整理するのと同じように、BLOB を整理するために使われます。

Cloud Blob コンテナーを作成した後は、関心がある特定の BLOB を指す **CloudBlockBlob** オブジェクトをインスタンス化して、アップロード、ダウンロード、コピーなどの操作を実行できます。

このセクションでは、オブジェクトをインスタンス化し、新しいコンテナーを作成した後、BLOB がパブリックになるようにコンテナーに対するアクセス許可を設定します。 コンテナーの名前は **quickstartblobs** です。 


```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
## <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 最もよく使われるのはブロック BLOB であり、このクイックスタートでもそれを使います。  

ファイルを BLOB にアップロードするには、ローカル ドライブ上でディレクトリ名とファイル名を結合することにより、ファイルの完全なパスを取得します。 **create\_blob\_from\_path** メソッドを使用して、指定されたパスにファイルをアップロードします。 

サンプル コードは、アップロードとダウンロードに使うローカル ファイルを作成し、アップロードするファイルを **file\_path\_to\_file** として、BLOB の名前を **local\_file\_name** として格納します。 次の例では、ファイルを **quickstartblobs** という名前のコンテナーにアップロードします。

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Blob Storage では複数のアップロード方法を使うことができます。 たとえば、メモリ ストリームがある場合、**create\_blob\_from\_path** ではなく **create\_blob\_from\_stream** メソッドを使うことができます。. 

ブロック BLOB の最大サイズは 4.7 TB であり、Excel スプレッドシートから大きなビデオ ファイルまで何にでも使うことができます。 ページ BLOB は、主に、IaaS VM のバックアップ用の VHD ファイルに使われます。 追加 BLOB は、ファイルに書き込んでから情報を追加する場合など、ログ記録に使われます。 BLOB ストレージに格納されているほとんどのオブジェクトはブロック BLOB です。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

**list_blobs** メソッドを使用してコンテナー内のファイルの一覧を取得します。 このメソッドは、ジェネレーターを返します。 次のコードは、BLOB の一覧を取得し、ループ処理して、コンテナー内に見つかった BLOB の名前を表示します。  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

## <a name="download-the-blobs"></a>BLOB のダウンロード

**get\_blob\_to\_path** メソッドを使用して、ローカル ディスクに BLOB をダウンロードします。 次のコードは、前のセクションでアップロードされた BLOB をダウンロードします。 "_DOWNLOADED" は、ローカル ディスク上で両方のファイルを確認できるように、BLOB の名前にサフィックスとして追加されます。 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイックスタートでアップロードした BLOB が不要になった場合は、**delete\_container** を使ってコンテナー全体を削除できます。 作成されたファイルが不要になった場合は、**delete\_blob** メソッドを使ってファイルを削除します。

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="next-steps"></a>次のステップ
 
このクイックスタートでは、Python を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 Blob Storage の操作の詳細を学習するには、Blob Storage の操作方法に進みます。

> [!div class="nextstepaction"]
> [Blob Storage の操作方法](./storage-python-how-to-use-blob-storage.md)
 

Storage Explorer と BLOB について詳しくは、「[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。
