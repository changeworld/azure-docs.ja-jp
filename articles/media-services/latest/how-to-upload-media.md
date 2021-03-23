---
title:メディアをアップロードする:Azure Media Services の説明:ストリーミングまたはエンコードのためにメディアをアップロードする方法について説明します。
services: media-services documentationcenter: '' author:IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: how-to ms.date:08/31/2020 ms.author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>ストリーミングまたはエンコードのためにメディアをアップロードする

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services で、アセットに関連付けられている BLOB コンテナーにデジタル ファイル (メディア) をアップロードします。 [Asset](/rest/api/media/operations/asset) エンティティには、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を格納できます。 ファイルをアセットのコンテナーにアップロードすると、コンテンツがクラウドに安全に保存され、処理したりストリーミングしたりできるようになります。

ただし、作業を開始する前に、いくつかの値を収集するか、検討する必要があります。

1. アップロードするファイルへのローカル ファイル パス
1. アセット (コンテナー) のアセット ID
1. 拡張子を含む、アップロードしたファイルに使用する名前
1. 使用しているストレージ アカウントの名前
1. ストレージ アカウントのアクセス キー

## <a name="portal"></a>[ポータル](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the CLI](./includes/task-upload-file-to-asset-cli.md)]

## <a name="python"></a>[Python](#tab/python)

コードによって既に認証が確立されており、入力資産も既に作成済みであることを前提に、次のコード スニペットを使用して、その資産 (in_container) にローカル ファイルをアップロードします。

```python
#The storage objects
from azure.storage.blob import BlobServiceClient, BlobClient

#Establish storage variables
storage_account_name = '<your storage account name'
storage_account_key = '<your storage account key'
storage_blob_url = 'https://<your storage account name>.blob.core.windows.net/'

in_container = 'asset-' + inputAsset.asset_id

#The file path of local file you want to upload
source_file = "ignite.mp4"

# Use the Storage SDK to upload the video
blob_service_client = BlobServiceClient(account_url=storage_blob_url, credential=storage_account_key)
blob_client = blob_service_client.get_blob_client(in_container,source_file)

# Upload the video to storage as a block blob
with open(source_file, "rb") as data:
    blob_client.upload_blob(data, blob_type="BlockBlob")
```

---
<!-- add these to the tabs when available -->
その他の方法については、[.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)、[Java](../../storage/blobs/storage-quickstart-blobs-java.md)、[Python](../../storage/blobs/storage-quickstart-blobs-python.md)、[JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md) での BLOB の操作に関する [Azure Storage のドキュメント](../../storage/blobs/index.yml)を参照してください。

## <a name="next-steps"></a>次のステップ

> [Media Services の概要](media-services-overview.md)
