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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087911"
---
# <a name="assets-in-azure-media-services-v3"></a>Azure Media Services v3 のアセット

Azure Media Services では、[アセット](https://docs.microsoft.com/rest/api/media/assets)は中核的な概念です。 アセットでは、メディアの入力 (たとえば、アップロードまたはライブ取り込みを通して)、メディアの出力 (ジョブ出力から)、およびメディアの公開 (ストリーム配信の場合) を行います。 

アセットは [Azure Storage アカウント](storage-account-concept.md)内の BLOB コンテナーにマップされ、アセット内のファイルはブロック BLOB としてそのコンテナーに格納されます。 アセットには、Azure Storage 内に格納されているデジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) に関する情報が含まれています。

Media Services は、アカウントが汎用 v2 (GPv2) ストレージを使用している場合に、BLOB 層をサポートします。 GPv2 を使用して、[クール ストレージまたはアーカイブ ストレージ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)にファイルを移動できます。 **アーカイブ** ストレージは、(エンコード後などに) 不要になったソース ファイルをアーカイブするのに適しています。

**アーカイブ** ストレージ層は、既にエンコードされ、エンコード ジョブの出力が出力 BLOB コンテナーに配置されている非常に大きなソース ファイルの場合のみ推奨されます。 アセットに関連付け、コンテンツのストリーム配信や分析に使用する出力コンテナー内の BLOB は、**ホット**または**クール** ストレージ層に存在する必要があります。

## <a name="naming"></a>名前を付ける 

### <a name="assets"></a>アセット

アセット名は一意である必要があります。 Media Services v3 のリソース名 (アセット、ジョブ、変換など) には、Azure Resource Manager の名前付け規則が適用されます。 詳細については、「[名前付け規則](media-services-apis-overview.md#naming-conventions)」を参照してください。

### <a name="blobs"></a>BLOB

アセット内のファイルまたは BLOB の名前は、[BLOB 名の要件](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)と[NTFS 名の要件](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)の両方に従っている必要があります。 これらの要件は、ファイルが BLOB ストレージからローカルの NTFS ディスクにコピーされて処理できるようにするためのものです。

## <a name="next-steps"></a>次のステップ

[Media Services でアセットを管理する](manage-asset-concept.md)

## <a name="see-also"></a>関連項目

[Media Services v2 と v3 の違い](migrate-from-v2-to-v3.md)
