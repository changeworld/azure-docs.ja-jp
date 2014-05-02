<properties linkid="develop-python-blob-service" urlDisplayName="BLOB サービス" pageTitle="BLOB ストレージの使用方法 (Python) | Microsoft Azure" metaKeywords="Azure BLOB サービス Python, Azure BLOB Python" description="Azure BLOB サービスを使用して、BLOB をアップロード、列挙、ダウンロード、削除する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="Python から BLOB ストレージ サービスを使用する方法" authors="" videoId="" scriptId="" />

# Python から BLOB ストレージ サービスを使用する方法
このガイドでは、Azure BLOB ストレージ サービスを使用して一般的なシナリオを
実行する方法について説明します。サンプルは Python API を使用して記述されて
います。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、
**ダウンロード**、および**削除**です。BLOB の詳細については、
「[次のステップ][]」のセクションを参照してください。

## 目次

[BLOB ストレージとは][]   
 [概念][]   
 [Azure のストレージ アカウントの作成][]   
 [方法: コンテナーを作成する][]   
 [方法: コンテナーに BLOB をアップロードする][]   
 [方法: コンテナー内の BLOB を一覧表示する][]   
 [方法: BLOB をダウンロードする][]   
 [方法: BLOB を削除する][]   
 [方法: サイズが大きい BLOB のアップロードとダウンロード][]   
 [次のステップ][]   

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Azure のストレージ アカウントの作成

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>方法: コンテナーを作成する

**注:** Python またはクライアント ライブラリをインストールする場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/)」を参照してください。


**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作でき
ます。次のコードでは、**BlobService** オブジェクトを作成します。プログラム
を使用して Azure ストレージにアクセスするすべての Python ファイルの先頭付近
に、次のコードを追加します。

	from azure.storage import *

次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、**BlobService** オブジェクトを作成します。'myaccount' と 'mykey' の部分は、実際のアカウントとキーに置き換えます。

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

ストレージの BLOB はすべてコンテナー内に格納されます。コンテナーが存在しない場合は、**BlobService** オブジェクトを使用してコンテナーを作成できます。

	blob_service.create_container('mycontainer')

既定では、新しいコンテナーはプライベートであるため、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを指定する必要があります (前と同じ方法で)。コンテナー内のファイルをすべてのユーザーが利用できるようにする場合は、次のコードを使用して、コンテナーを作成しパブリック アクセス レベルを渡します。

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

または、次のコードを使用してコンテナーを作成した後で、コンテナーを変更することもできます。

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

この変更後、パブリック コンテナー内の BLOB は、インターネットに接続している
すべてのユーザーが表示できますが、変更または削除できるのは、このコンテナー
の作成と変更を行ったユーザーだけです。

## <a name="upload-blob"> </a>方法: コンテナーに BLOB をアップロードする

BLOB にファイルをアップロードするには、**put_blob** メソッドを使用して 
BLOB を作成し、ファイル ストリームを BLOB の内容として使用します。
最初に **task1.txt** というファイル (任意の内容で問題ありません) を作成し、
そのファイルを Python ファイルと同じディレクトリに保存します。

	myblob = open(r'task1.txt', 'r').read()
	blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')

## <a name="list-blob"> </a>方法: コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、**list_blobs** メソッドを **for** 
ループで使用して、コンテナー内の各 BLOB の名前を表示します。次のコードでは、
コンテナー内の各 BLOB の**名前**と **URL** をコンソールに出力しています。

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## <a name="download-blobs"> </a>方法: BLOB をダウンロードする

BLOB をダウンロードするには、**get_blob** メソッドを使用して、ローカル 
ファイルに保存できるストリーム オブジェクトに BLOB の内容を転送します。

	blob = blob_service.get_blob('mycontainer', 'myblob')
	with open(r'out-task1.txt', 'w') as f:
		f.write(blob)

## <a name="delete-blobs"> </a>方法: BLOB を削除する

最後に、BLOB を削除するには、**delete_blob** を呼び出します。

	blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="large-blobs"> </a>方法: サイズが大きい BLOB のアップロードとダウンロード

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

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ 
タスクを実行する方法については、次のリンク先を参照してください。

- MSDN リファレンス: [Azure のデータの格納とアクセス][]
-  [Azure のストレージ チーム ブログ][]

  [次のステップ]: #next-steps
  [BLOB ストレージとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [方法: コンテナーを作成する]: #create-container
  [方法: コンテナーに BLOB をアップロードする]: #upload-blob
  [方法: コンテナー内の BLOB を一覧表示する]: #list-blob
  [方法: BLOB をダウンロードする]: #download-blobs
  [方法: BLOB を削除する]: #delete-blobs
  [方法: サイズが大きい BLOB のアップロードとダウンロード]: #large-blobs
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/

