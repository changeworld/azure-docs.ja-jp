---
title: Azure Media Services の資産 | Microsoft Docs
description: この記事では、資産とは何かについて説明し、Azure Media Services によるそれらの使用方法についても説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>資産

**資産** には、デジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) と、それらのファイルに関するメタデータが含まれます。 デジタル ファイルが資産にアップロードされた後は、Media Services エンコードおよびストリーミング ワークフローで使用できます。

資産は [Azure Storage アカウント](storage-account-concept.md)内の BLOB コンテナーにマップされ、資産内のファイルはブロック BLOB としてそのコンテナーに格納されます。 ストレージ SDK クライアントを使用して、コンテナー内の資産ファイルを操作できます。

Azure Media Services は、アカウントが汎用 v2 (GPv2) を使用しているときに、BLOB 層をサポートします。 GPv2 を使用して、クール ストレージまたはコールド ストレージにファイルを移動できます。 コールド ストレージは、不要になった中間ファイルをアーカイブするのに適しています (エンコード後など)。

Media Services v3 では、資産または HTTP(S) URL からジョブの入力を作成できます。 ジョブに対する入力として使用できる資産を作成するには、[ローカル ファイルからのジョブの入力の作成](job-input-from-local-file-how-to.md)に関する記事を参照してください。

「[Media Services のストレージ アカウント](storage-account-concept.md)」と「[Transform と Job](transform-concept.md)」も参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ファイルをストリーミングする](stream-files-dotnet-quickstart.md)
