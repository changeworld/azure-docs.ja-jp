<properties linkid="develop-python-blob-service" UrlDisplayName="BLOB サービス" pageTitle="BLOB ストレージの使用方法 (Python) - Windows Azure の機能ガイド" services=”Storage” MetaKeywords="Azure BLOB サービス Python, Azure BLOB Python" description="Windows Azure BLOB サービスを使用して、BLOB のアップロード、一覧表示、ダウンロード、および削除を実行する方法を説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Python から BLOB サービスを使用する方法"/>

# Python から BLOB ストレージ サービスを使用する方法
このガイドでは、Windows Azure BLOB ストレージ サービスを使用して
一般的なシナリオを実行する方法について説明します。サンプルは Python API を使用して
記述されています。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、
**ダウンロード**、および**削除**です。BLOB の詳細については、
「[次のステップ][]」のセクションを参照してください。

## 目次

[BLOB ストレージとは][]
 [概念][]
 [Windows Azure ストレージ アカウントの作成][]
 [コンテナーを作成する方法][]
 [コンテナーに BLOB をアップロードする方法][]
 [コンテナー内の BLOB を一覧表示する方法][]
 [BLOB をダウンロードする方法][]
 [BLOB を削除する方法][]
 [サイズが大きい BLOB のダウンロードとアップロードの方法][]
 [次のステップ][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Windows Azure ストレージ アカウントの作成

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>コンテナーを作成する方法

**注:** Python またはクライアント ライブラリをインストールする場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/)」を参照してください。


**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作できます。次の
コードでは、**BlobService** オブジェクトを作成します。プログラムを使用して
Windows Azure のストレージにアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.storage import *

次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、**BlobService** オブジェクトを作成します。"myaccount" と "mykey" の部分は、実際のアカウントとキーに置き換えます。

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

ストレージの BLOB はすべてコンテナー内に格納されます。コンテナーが存在しない場合は、**BlobService** オブジェクトを使用してコンテナーを作成できます。

	blob_service.create_container('mycontainer')

既定では、新しいコンテナーはプライベートなので、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを指定する必要があります (前と同じ方法で)。コンテナー内のファイルをすべてのユーザーが利用できるようにする場合は、次のコードを使用して、コンテナーを作成しパブリック アクセス レベルを渡します。

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

または、次のコードを使用してコンテナーを作成した後で、コンテナーを変更することもできます。

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

この変更後、パブリック コンテナー内の BLOB は、インターネットに
接続しているすべてのユーザーが表示できますが、変更または削除できるのは、このコンテナーの作成と変更を行ったユーザーだけです。

## <a name="upload-blob"> </a>コンテナーに BLOB をアップロードする方法

BLOB にファイルをアップロードするには、**put_blob**
メソッドを使用して BLOB を作成し、ファイル ストリームを BLOB の内容として使用します。
最初に **task1.txt** というファイル (任意の内容で問題ありません)
を作成し、そのファイルを Python ファイルと同じディレクトリに保存します。

	myblob = open(r'task1.txt', 'r').read()
	blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')

## <a name="list-blob"> </a>コンテナー内の BLOB を一覧表示する方法

コンテナー内の BLOB を一覧表示するには、**list_blobs** メソッドを
**for** ループで使用して、コンテナー内の各 BLOB の名前を表示します。次の
コードでは、コンテナー内の各 BLOB の**名前**と **URL** をコンソールに
出力しています。

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## <a name="download-blobs"> </a>BLOB をダウンロードする方法

BLOB をダウンロードするには、**get_blob** メソッドを使用して、
ローカル ファイルに保存できるストリーム オブジェクトに BLOB の内容
を転送します。

	blob = blob_service.get_blob('mycontainer', 'myblob')
	with open(r'out-task1.txt', 'w') as f:
		f.write(blob)

## <a name="delete-blobs"> </a>BLOB を削除する方法

最後に、BLOB を削除するには、**delete_blob** を呼び出します。

	blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="large-blobs"> </a>サイズが大きい BLOB のダウンロードとアップロードの方法

ブロック BLOB の最大サイズは 200 GB です。64 MB 以下の BLOB の場合、前に説明したように、**put\_blob** や **get\_blob** を 1 回呼び出して、BLOB のアップロードやダウンロードを実行できます。64 MB を超える BLOB の場合、4 MB 以下のブロックで BLOB のアップロードやダウンロードを実行する必要があります。

次のコードは、任意のサイズのブロック BLOB のアップロードやダウンロードを行う関数の例を示しています。

import base64

chunk_size = 4 * 1024 * 1024

def upload(blob_service, container_name, blob_name, file_path):
        blob_service.create_container(container_name, None, None, False)
        blob_service.put_blob(container_name, blob_name, '', 'BlockBlob')

block_ids = []
index = 0
        with open(file_path, 'rb') as f:
            while True:
                data = f.read(chunk_size)
                if data:
                    length = len(data)
                    block_id = base64.b64encode(str(index))
                    blob_service.put_block(container_name, blob_name, data, block_id)
                    block_ids.append(block_id)
                    index += 1
                else:
                    break

blob_service.put_block_list(container_name, blob_name, block_ids)

def download(blob_service, container_name, blob_name, file_path):
        props = blob_service.get_blob_properties(container_name, blob_name)
        blob_size = int(props['content-length'])

index = 0
        with open(file_path, 'wb') as f:
            while index < blob_size:
                chunk_range = 'bytes={}-{}'.format(index, index + chunk_size - 1)
                data = blob_service.get_blob(container_name, blob_name, x_ms_range=chunk_range)
                length = len(data)
                index += length
                if length > 0:
                    f.write(data)
                    if length < chunk_size:
                        break
                else:
                    break

200 GB を超える BLOB が必要な場合は、ブロック BLOB ではなくページ BLOB を使用できます。ページ BLOB の最大サイズは 1 TB で、ページは 512 バイトのページ境界に合わせて整理されます。ページ BLOB を作成するには **put\_blob** を、ページ BLOB に書き出すには **put\_page** を、ページ BLOB から読み取るには **get\_blob** を使用します。

## <a name="next-steps"> </a>次のステップ

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクを
実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Windows Azure のデータの格納とアクセス][]
-   [Windows Azure ストレージ チーム ブログ][] (このページは英語の場合があります)

  [次のステップ]: #next-steps
[BLOB ストレージとは]: #what-is
[概念]: #concepts
[Windows Azure ストレージ アカウントの作成]: #create-account
[コンテナーを作成する方法]: #create-container
[コンテナーに BLOB をアップロードする方法]: #upload-blob
[コンテナー内の BLOB を一覧表示する方法]: #list-blob
[BLOB をダウンロードする方法]: #download-blobs
[BLOB を削除する方法]: #delete-blobs
[サイズが大きい BLOB のダウンロードとアップロードの方法]: #large-blobs
[Windows Azure のデータの格納とアクセス]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Windows Azure ストレージ チーム ブログ (このページは英語の場合があります)]: http://blogs.msdn.com/b/windowsazurestorage/
