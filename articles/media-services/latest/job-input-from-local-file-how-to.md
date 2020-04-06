---
title: ローカル ファイルから Azure Media Services ジョブの入力を作成する | Microsoft Docs
description: この記事では、ローカル ファイルから Azure Media Services ジョブの入力を作成する手順を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: aba987ba232a29ffc240f72039b1e24bb87a2ed4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345921"
---
# <a name="create-a-job-input-from-a-local-file"></a>ローカル ファイルからジョブの入力を作成する

Media Services v3 では、ビデオを処理するジョブを送信するときに、入力ビデオを検索する場所を Media Services に指示する必要があります。 入力ビデオは Media Service 資産として格納できます。この場合は、(ローカルまたは Azure Blob ストレージに格納されている) ファイルに基づいて入力資産を作成します。 このトピックでは、ローカル ファイルからジョブの入力を作成する方法を示します。 完全な例については、こちらの [GitHub サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)を参照してください。

## <a name="prerequisites"></a>前提条件 

* [Media Services アカウントを作成する](create-account-cli-how-to.md)
* 「[アセットの管理](manage-asset-concept.md)」を確認する。

## <a name="net-sample"></a>.Net のサンプル

次のコードは、入力資産を作成してジョブの入力として使用する方法を示しています。 CreateInputAsset 関数は、次のアクションを行います。

* 資産を作成する
* 書き込み可能な [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) をアセットの[ストレージ内のコンテナー](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)に取得する
* SAS URL を使用してストレージ内のコンテナーにファイルをアップロードする

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

次のコード スニペットでは、出力アセットがまだ存在しない場合、出力アセットを作成します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

次のコード スニペットでは、エンコーディング ジョブを送信します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>ジョブ エラー コード

[エラー コード](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[HTTPS URL からジョブの入力を作成する](job-input-from-http-how-to.md)
