---
title: ファイルと ACL 用の Azure Data Lake Storage Gen2 Python SDK (プレビュー)
description: Python を使用して、階層型名前空間 (HNS) が有効なストレージ アカウントでディレクトリ、ファイル、ディレクトリのアクセス制御リスト (ACL) を管理します。
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e07bb6aa9d1fe22baaebb7bc7239ce03a728c6b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431810"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Python を使用して Azure Data Lake Storage Gen2 のディレクトリ、ファイル、ACL を管理する (プレビュー)

この記事では、Python を使用して、階層型名前空間 (HNS) が有効なストレージ アカウントでディレクトリ、ファイル、アクセス許可を作成および管理する方法について説明します。 

> [!IMPORTANT]
> Python 向けの Azure Data Lake Storage クライアント ライブラリのサポートは、現在パブリック プレビュー段階です。

[パッケージ (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [フィードバックを送る](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
> * 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](data-lake-storage-quickstart-create-account.md)の手順に従います。

## <a name="set-up-your-project"></a>プロジェクトの設定

Python 向けの Azure Data Lake Storage クライアント ライブラリを、[pip](https://pypi.org/project/pip/) を使用してインストールします。

```
pip install azure-storage-file-datalake --pre
```

次の import ステートメントを、コード ファイルの先頭に追加します。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>アカウントに接続する

この記事のスニペットを使用するには、ストレージ アカウントを表す **DataLakeServiceClient** インスタンスを作成する必要があります。 最も簡単な方法は、アカウント キーを使用することです。 

この例では、アカウント キーを使用して、ストレージアカウントを表す **DataLakeServiceClient** インスタンスを作成します。 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- `storage_account_name` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

- `storage-account-key` プレースホルダーの値は、実際のストレージ アカウントのアクセス キーに置き換えます。

## <a name="create-a-file-system"></a>ファイル システムを作成する

ファイル システムは、ファイルのコンテナーとして機能します。 **FileSystemDataLakeServiceClient.create_file_system** メソッドを呼び出すことによって作成できます。

この例では、`my-file-system` という名前のファイル システムを作成します。

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>ディレクトリを作成する

**FileSystemClient.create_directory** メソッドを呼び出して、ディレクトリ参照を作成します。

この例では、`my-directory` という名前のディレクトリをファイル システムに追加します。 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>ディレクトリの名前変更または移動

**DataLakeDirectoryClient.rename_directory** メソッドを呼び出して、ディレクトリの名前を変更するか、ディレクトリを移動します。 目的のディレクトリのパスをパラメーターに渡します。 

この例では、サブディレクトリの名前を `my-subdirectory-renamed` に変更します。

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>ディレクトリを削除する

**DataLakeDirectoryClient.delete_directory** メソッドを呼び出して、ディレクトリを削除します。

この例では、`my-directory` という名前のディレクトリを削除します。  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>ディレクトリのアクセス許可を管理する

**DataLakeDirectoryClient.get_access_control** メソッドを呼び出してディレクトリのアクセス制御リスト (ACL) を取得し、**DataLakeDirectoryClient.set_access_control** メソッドを呼び出して ACL を設定します。

この例では、`my-directory` という名前のディレクトリの ACL を取得して設定します。 `rwxr-xrw-` 文字列により、所有ユーザーには読み取り、書き込み、および実行のアクセス許可が、所有グループには読み取りと実行のアクセス許可のみが、他のすべてのユーザーに読み取りと書き込みのアクセス許可が付与されます。

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>ファイルをディレクトリにアップロードする 

まず、**DataLakeFileClient** クラスのインスタンスを作成して、ターゲット ディレクトリにファイル参照を作成します。 **DataLakeFileClient.append_data** メソッドを呼び出して、ファイルをアップロードします。 **DataLakeFileClient.flush_data** メソッドを呼び出して、アップロードの完了を確認してください。

この例では、`my-directory` という名前のディレクトリにテキスト ファイルをアップロードします。   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>ファイルのアクセス許可を管理する

**DataLakeFileClient.get_access_control** メソッドを呼び出してファイルのアクセス制御リスト (ACL) を取得し、**DataLakeFileClient.set_access_control** メソッドを呼び出して ACL を設定します。

この例では、`my-file.txt` という名前のファイルの ACL を取得して設定します。 `rwxr-xrw-` 文字列により、所有ユーザーには読み取り、書き込み、および実行のアクセス許可が、所有グループには読み取りと実行のアクセス許可のみが、他のすべてのユーザーに読み取りと書き込みのアクセス許可が付与されます。

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>ディレクトリからダウンロードする 

書き込み用にローカル ファイルを開きます。 次に、ダウンロードするファイルを表す **DataLakeFileClient** インスタンスを作成します。 **DataLakeFileClient.read_file** を呼び出して、ファイルからバイトを読み取り、そのバイトをローカル ファイルに書き込みます。 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

**FileSystemClient.get_paths** メソッドを呼び出してディレクトリの内容を一覧表示し、結果を列挙します。

この例では、`my-directory` という名前のディレクトリにある各サブディレクトリとファイルのパスを出力します。

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>参照

* [API リファレンス ドキュメント](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [パッケージ (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/)
* [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [フィードバックを送る](https://github.com/Azure/azure-sdk-for-python/issues)
