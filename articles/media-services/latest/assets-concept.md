---
title: アセット
titleSuffix: Azure Media Services
description: アセットとは何か、および Azure Media Services によってどのように使用されるかについて説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ce32343faefbcf2484ec0b1b39f752654a2d8514
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303615"
---
# <a name="assets-in-azure-media-services"></a>Azure Media Services のアセット

Azure Media Services では、[アセット](https://docs.microsoft.com/rest/api/media/assets)は中核的な概念です。 アセットでは、メディアの入力 (たとえば、アップロードまたはライブ取り込みを通して)、メディアの出力 (ジョブ出力から)、およびメディアの公開 (ストリーム配信の場合) を行います。 

アセットは [Azure Storage アカウント](storage-account-concept.md)内の BLOB コンテナーにマップされ、アセット内のファイルはブロック BLOB としてそのコンテナーに格納されます。 アセットには、Azure Storage 内に格納されているデジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) に関する情報が含まれています。

Media Services は、アカウントが汎用 v2 (GPv2) ストレージを使用している場合に、BLOB 層をサポートします。 GPv2 を使用して、[クール ストレージまたはアーカイブ ストレージ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)にファイルを移動できます。 **アーカイブ** ストレージは、(エンコード後などに) 不要になったソース ファイルをアーカイブするのに適しています。

**アーカイブ** ストレージ層は、既にエンコードされ、エンコード ジョブの出力が出力 BLOB コンテナーに配置されている非常に大きなソース ファイルの場合のみ推奨されます。 アセットに関連付け、コンテンツのストリーム配信や分析に使用する出力コンテナー内の BLOB は、**ホット**または**クール** ストレージ層に存在する必要があります。

## <a name="naming"></a>名前を付ける 

### <a name="assets"></a>アセット

アセット名は一意である必要があります。 Media Services v3 のリソース名 (アセット、ジョブ、変換など) には、Azure Resource Manager の名前付け規則が適用されます。 詳細については、「[名前付け規則](media-services-apis-overview.md#naming-conventions)」を参照してください。

### <a name="blobs"></a>BLOB

アセット内のファイルまたは BLOB の名前は、[BLOB 名の要件](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)と[NTFS 名の要件](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)の両方に従っている必要があります。 これらの要件は、ファイルが BLOB ストレージからローカルの NTFS ディスクにコピーされて処理できるようにするためのものです。

## <a name="map-v3-asset-properties-to-v2"></a>v3 と v2 の資産のプロパティのマッピング

次の表は、v3 の[資産](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)のプロパティが v2 の資産のプロパティにどのようにマッピングされるかを示しています。

|v3 のプロパティ|v2 のプロパティ|
|---|---|
|`id` - (一意) Azure Resource Manager の完全なパス。[アセット](https://docs.microsoft.com/rest/api/media/assets/createorupdate)に関するページで例を参照してください||
|`name` - (一意)「[名前付け規則](media-services-apis-overview.md#naming-conventions)」を参照してください ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` - (一意) 値は `nb:cid:UUID:` プレフィックスで始まります。|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (作成オプション)|
|`type`||

## <a name="storage-side-encryption"></a>ストレージ側の暗号化

保存時の資産を保護するには、ストレージ側の暗号化で資産を暗号化する必要があります。 次の表では、Media Services でのストレージ側暗号化のしくみを示します。

|暗号化オプション|説明|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services のストレージの暗号化|AES-256 暗号化、Media Services によって管理されるキー。|サポートされています<sup>(1)</sup>|サポートされていません<sup>(2)</sup>|
|[Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage によって提供されるサーバー側暗号化、Azure またはお客様が管理するキー。|サポートされています|サポートされています|
|[ストレージ クライアント側暗号化](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage によって提供されるクライアント側暗号化、お客様が Key Vault で管理するキー。|サポートされていません|サポートされていません|

<sup>1</sup> Media Services は、クリアな、どのような形式でも暗号化されていないコンテンツの処理をサポートしますが、そうすることは推奨されません。

<sup>2</sup> Media Services v3 では、ストレージの暗号化 (AES-256 暗号化) は、Media Services v2 で資産を作成した場合の下位互換性のためにのみサポートされています。 つまり、v3 は、既存のストレージの暗号化済みアセットでは動作しますが、そのようなアセットを新規作成することはできません。

## <a name="next-steps"></a>次のステップ

[Media Services でアセットを管理する](manage-asset-concept.md)

## <a name="see-also"></a>関連項目

[Media Services v2 と v3 の違い](migrate-from-v2-to-v3.md)
