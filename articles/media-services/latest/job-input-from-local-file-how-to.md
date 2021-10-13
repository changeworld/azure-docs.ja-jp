---
title: ローカル ファイルからジョブの入力を作成する
description: この記事では、ローカル ファイルから Azure Media Services ジョブの入力を作成する手順を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 05/25/2021
ms.author: inhenkel
ms.openlocfilehash: fa8b26c2f26ed93bda0ba96b695e94951b72af6b
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658217"
---
# <a name="create-a-job-input-from-a-local-file"></a>ローカル ファイルからジョブの入力を作成する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services v3 では、ビデオを処理するジョブを送信するときに、入力ビデオを検索する場所を Media Services に指示する必要があります。 入力ビデオは Media Service 資産として格納できます。この場合は、(ローカルまたは Azure Blob ストレージに格納されている) ファイルに基づいて入力資産を作成します。 このトピックでは、ローカル ファイルからジョブの入力を作成する方法を示します。 完全な例については、こちらの [GitHub サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)を参照してください。

## <a name="prerequisites"></a>前提条件

* [Media Services アカウントを作成する](./account-create-how-to.md)

## <a name="net-sample"></a>.Net のサンプル

次のコードは、入力資産を作成してジョブの入力として使用する方法を示しています。 CreateInputAsset 関数は、次のアクションを行います。

* 資産を作成する
* 書き込み可能な [SAS URL](../../storage/common/storage-sas-overview.md) をアセットの[ストレージ内のコンテナー](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-a-blob-to-a-container)に取得する
* SAS URL を使用してストレージ内のコンテナーにファイルをアップロードする

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

次のコード スニペットでは、出力アセットがまだ存在しない場合、出力アセットを作成します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

次のコード スニペットでは、エンコーディング ジョブを送信します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>ジョブ エラー コード

[エラー コード](/rest/api/media/jobs/get#joberrorcode)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[HTTPS URL からジョブの入力を作成する](job-input-from-http-how-to.md)
